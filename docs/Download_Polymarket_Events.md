To convert the Polymarket Gamma API events feed into a structured Markdown (.md) file, you can use the following Python script.
This script is designed to be idempotent and pipeline-ready: it fetches the raw JSON, maps the nested market data (Yes/No prices, liquidity, and volume), and generates a clean, readable report.
The Conversion Script
Python

import requestsfrom datetime import datetimedef generate_polymarket_md():
    url = "https://gamma-api.polymarket.com/events"
    # Filtering for active, high-volume events to keep the file focused
    params = {
        "active": "true",
        "closed": "false",
        "limit": 50,
        "order": "volume_24hr",
        "ascending": "false"
    }

    try:
        response = requests.get(url, params=params)
        response.raise_for_status()
        events = response.json()

        timestamp = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        md_content = f"# Polymarket Daily Event Pull\n"
        md_content += f"*Generated on: {timestamp}*\n\n"
        md_content += "---\n\n"

        for event in events:
            title = event.get('title', 'Untitled Event')
            description = event.get('description', 'No description available.').strip()
            md_content += f"## {title}\n"
            md_content += f"> {description}\n\n"

            # Table Header for Markets within this Event
            md_content += "| Market Question | Outcome Prices | 24h Volume | Liquidity |\n"
            md_content += "| :--- | :--- | :--- | :--- |\n"

            for market in event.get('markets', []):
                question = market.get('question', 'N/A')
                
                # Format prices (e.g., ["0.65", "0.35"] -> Yes: 65%, No: 35%)
                outcomes = market.get('outcomes', [])
                prices = market.get('outcomePrices', [])
                price_str = " / ".join([f"{o}: {float(p)*100:.1f}%" for o, p in zip(outcomes, prices)]) if prices else "N/A"
                
                volume = f"${float(market.get('volume24hr', 0)):,.2f}"
                liquidity = f"${float(market.get('liquidity', 0)):,.2f}"
                
                md_content += f"| {question} | {price_str} | {volume} | {liquidity} |\n"
            
            md_content += "\n---\n\n"

        filename = f"polymarket_events_{datetime.now().strftime('%Y%m%d')}.md"
        with open(filename, "w", encoding="utf-8") as f:
            f.write(md_content)
        
        print(f"Successfully created {filename}")

    except Exception as e:
        print(f"Failed to generate Markdown: {e}")if __name__ == "__main__":
    generate_polymarket_md()
Why this structure works for your workflow:
Logical Hierarchy: It treats the Event (the broad topic) as a header and the Markets (the specific bets) as rows in a table. This aligns with Polymarket's data model where one event (e.g., "Fed Rate Cut") often contains multiple specific markets.
Probability Translation: The script automatically converts the raw outcomePrices (0.0 to 1.0) into percentages (e.g., 65.0%), making the .md file immediately interpretable for a human reader or an LLM summary.
Sorted by Signal: By including the order=volume_24hr parameter in the API call, your Markdown file prioritizes the markets with the highest "wisdom of the crowd" activity.
Sample Output Preview
When you run this, the resulting .md file will look like this:
Will Bitcoin hit $100k in 2026?
Prediction on the price action of BTC relative to the $100,000 threshold.
Market QuestionOutcome Prices24h VolumeLiquidityWill BTC hit $100k?Yes: 72.1% / No: 27.9%$1,250,400.00$450,000.00
You can easily pipe this output into NotebookLM or a local RAG system to track shifts in market sentiment over time.
