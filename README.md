Crypto Arbitrage Bot
This project is a cryptocurrency arbitrage bot designed to take advantage of price differences between Binance and Kraken exchanges. The bot continuously monitors the prices of a predefined list of trading pairs and executes trades to profit from arbitrage opportunities.

Features
Multi-Exchange Support: The bot supports BinanceUS and Kraken exchanges.
Automated Trading: Automatically buys and sells cryptocurrencies to exploit price differences.
Error Handling: Includes basic error handling and retry mechanisms.
Configurable Trading Pairs: Easy to add or remove trading pairs.
Prerequisites
Python 3.x
ccxt library
Installation
Clone the repository:

bash
Copy code
git clone https://github.com/yourusername/crypto-arbitrage-bot.git
cd crypto-arbitrage-bot
Install the required Python packages:

bash
Copy code
pip install ccxt
Configuration
Set up your API keys:

Replace 'your_binance_api_key' and 'your_binance_secret_key' with your actual BinanceUS API keys.
Replace 'your_kraken_api_key' and 'your_kraken_secret_key' with your actual Kraken API keys.
Adjust trading pairs:

The trading_pairs list in the script defines the pairs to be monitored. Add or remove pairs as needed.
Usage
Run the bot:
bash
Copy code
python arbitrage_bot.py
Code Overview
Import Libraries
python
Copy code
import ccxt
import time
Define Exchanges
python
Copy code
exchanges = {
    'binance': ccxt.binanceus({
        'apiKey': 'your_binance_api_key',
        'secret': 'your_binance_secret_key'
    }),
    'kraken': ccxt.kraken({
        'apiKey': 'your_kraken_api_key',
        'secret': 'your_kraken_secret_key'
    }),
}
Define Trading Pairs
python
Copy code
trading_pairs = [
    "BTC/USDT", "ETH/USDT", "SOL/USDT", "USDC/USDT", "FET/USDT",
    "DOGE/USDT", "XRP/USDT", "NEAR/USDT", "HBAR/USDT", "ADA/USDT",
    "LINK/USDT", "SOL/BTC", "DOT/USDT", "LTC/USDT", "AVAX/USDT",
    "VET/USDT", "MATIC/USDT", "UNI/USDT", "BNB/BTC", "XLM/USDT",
    "BCH/USDT", "GALA/USDT", "SAND/USDT", "RVN/USDT", "ONE/USDT",
    "ALGO/USDT", "ETC/USDT", "KDA/USDT", "ADA/USDC", "FTM/USDT",
    "ATOM/USDT", "GRT/USDT", "CTSI/USDT", "MATIC/BTC", "BAT/USDT",
    "OP/USDT", "ZEC/USDT", "ENJ/USDT", "IOTA/USDT", "DGB/USDT",
    "QNT/USDT", "MXC/USDT", "1INCH/USDT", "FLOW/USDT", "CLV/USDT",
    "ACH/USDT", "SUSHI/USDT", "LTC/BTC", "BAND/USDT", "FIL/USDT",
    "T/USDT", "AUDIO/USDT", "REN/USDT", "ADX/USDT", "KAVA/USDT",
    "APE/USDT", "KNC/USDT", "ANKR/USDT", "DASH/USDT", "EOS/USDT",
    "CHZ/USDT", "STORJ/USDT", "LRC/USDT", "PROM/USDT", "LTO/USDT",
    "STMX/USDT", "SNX/USDT", "COMP/USDT", "BNT/USDT", "EGLD/USDT",
    "STG/USDT", "IOST/USDT", "OGN/USDT", "GLM/USDT", "DAI/USDT",
    "WBTC/BTC", "YFI/USDT", "TLM/USDT", "LOOM/USDT", "BAL/USDT",
    "VITE/USDT", "REQ/USDT"
]
Fetch Prices Function
python
Copy code
def fetch_prices():
    prices = {}
    for name, exchange in exchanges.items():
        for pair in trading_pairs:
            try:
                ticker = exchange.fetch_ticker(pair)
                prices[f"{name}_{pair}"] = ticker['last']
            except Exception as e:
                print(f"Error fetching {pair} price from {name}: {e}")
    return prices
Detect Arbitrage Function
python
Copy code
def detect_arbitrage(prices):
    arbitrage_opportunities = []
    
    for pair in trading_pairs:
        binance_price = prices.get(f"binance_{pair}")
        kraken_price = prices.get(f"kraken_{pair}")
        
        if binance_price is None or kraken_price is None:
            continue

        if binance_price < kraken_price:
            profit = kraken_price - binance_price
            arbitrage_opportunities.append(('binance', 'kraken', pair, profit))
        elif kraken_price < binance_price:
            profit = binance_price - kraken_price
            arbitrage_opportunities.append(('kraken', 'binance', pair, profit))
    
    return arbitrage_opportunities
Execute Trade Function
python
Copy code
def execute_trade(buy_exchange, sell_exchange, pair, amount):
    try:
        buy_order = exchanges[buy_exchange].create_market_buy_order(pair, amount)
        sell_order = exchanges[sell_exchange].create_market_sell_order(pair, amount)
        return buy_order, sell_order
    except Exception as e:
        print(f"Error executing trade between {buy_exchange} and {sell_exchange} for {pair}: {e}")
        return None, None
Main Loop
python
Copy code
def main():
    amount = 0.01  # Example amount
    while True:
        try:
            prices = fetch_prices()
            print(prices)
            
            opportunities = detect_arbitrage(prices)
            if opportunities:
                for opportunity in opportunities:
                    buy_exchange, sell_exchange, pair, profit = opportunity
                    buy_order, sell_order = execute_trade(buy_exchange, sell_exchange, pair, amount)
                    if buy_order and sell_order:
                        print(f"Executed trades: Buy {pair} on {buy_exchange}, sell on {sell_exchange}, profit: {profit}")
            
            time.sleep(5)
        
        except ccxt.BaseError as e:
            print(f"CCXT Error occurred: {e}")
            time.sleep(10)  # Wait a bit longer before retrying on CCXT error
        except Exception as e:
            print(f"Unexpected error occurred: {e}")
            time.sleep(10)  # Wait a bit longer before retrying on unexpected error

if __name__ == "__main__":
    main()
Contributing
Feel free to submit issues or pull requests. All contributions are welcome!

License
This project is licensed under the MIT License. See the LICENSE file for details.

Feel free to replace "your_binance_api_key" and "your_kraken_api_key" with actual API keys or environment variable placeholders for better security practices. Also, consider adding more sophisticated logging and notification mechanisms as per your requirements.






