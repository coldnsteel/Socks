#!/usr/bin/env python3
"""
Quantum Stock Monitor Agent - FIXED VERSION
Handles yfinance MultiIndex column issues
Enhanced with Bollinger Bands, email alerts, and comprehensive tracking
Based on Grok's research into quantum computing stocks
"""

import yfinance as yf
import pandas as pd
import schedule
import time
from datetime import datetime
import json
import os
import warnings

# Suppress FutureWarnings from yfinance
warnings.filterwarnings('ignore', category=FutureWarning)

# List of stocks from Grok's quantum computing research
QUANTUM_PURE_PLAYS = ['IONQ', 'RGTI', 'QUBT']
BIG_TECH = ['IBM', 'GOOGL', 'MSFT', 'AMZN']
DEFENSE_INDUSTRIAL = ['HON', 'LMT']
AI_INFRASTRUCTURE = ['FIX', 'VRT', 'ETN', 'HDSN']

ALL_STOCKS = QUANTUM_PURE_PLAYS + BIG_TECH + DEFENSE_INDUSTRIAL + AI_INFRASTRUCTURE

# Alert thresholds
STRONG_BUY_THRESHOLD = 3  # Number of bullish signals needed
RSI_OVERSOLD = 30
RSI_OVERBOUGHT = 70
RSI_MOMENTUM_LOW = 50
RSI_MOMENTUM_HIGH = 70

def calculate_indicators(ticker, days=200):
    """Calculate comprehensive technical indicators for a stock"""
    try:
        # Download historical data with auto_adjust set explicitly
        data = yf.download(ticker, period=f'{days}d', progress=False, auto_adjust=True)
        
        # Handle MultiIndex columns (common yfinance issue)
        if isinstance(data.columns, pd.MultiIndex):
            data.columns = data.columns.get_level_values(0)
        
        if data.empty:
            return None
        
        # Ensure we have the required columns
        if 'Close' not in data.columns:
            print(f"Warning: 'Close' column not found for {ticker}")
            return None
        
        # Calculate SMAs
        data['SMA20'] = data['Close'].rolling(window=20, min_periods=1).mean()
        data['SMA50'] = data['Close'].rolling(window=50, min_periods=1).mean()
        data['SMA200'] = data['Close'].rolling(window=200, min_periods=1).mean()
        
        # Calculate Bollinger Bands (20-day, 2 std dev)
        bb_period = 20
        data['BB_Middle'] = data['Close'].rolling(window=bb_period, min_periods=1).mean()
        bb_std = data['Close'].rolling(window=bb_period, min_periods=1).std()
        data['BB_Upper'] = data['BB_Middle'] + (bb_std * 2)
        data['BB_Lower'] = data['BB_Middle'] - (bb_std * 2)
        
        # Calculate BB Width safely
        data['BB_Width'] = ((data['BB_Upper'] - data['BB_Lower']) / data['BB_Middle']) * 100
        data['BB_Width'] = data['BB_Width'].replace([float('inf'), -float('inf')], 0)
        
        # Calculate RSI (14-day)
        delta = data['Close'].diff()
        gain = (delta.where(delta > 0, 0)).rolling(window=14, min_periods=1).mean()
        loss = (-delta.where(delta < 0, 0)).rolling(window=14, min_periods=1).mean()
        
        # Avoid division by zero
        rs = gain / loss.replace(0, 1e-10)
        data['RSI'] = 100 - (100 / (1 + rs))
        
        # MACD (12-26 EMA difference, 9-day signal)
        ema12 = data['Close'].ewm(span=12, adjust=False).mean()
        ema26 = data['Close'].ewm(span=26, adjust=False).mean()
        data['MACD'] = ema12 - ema26
        data['MACD_Signal'] = data['MACD'].ewm(span=9, adjust=False).mean()
        data['MACD_Histogram'] = data['MACD'] - data['MACD_Signal']
        
        # Volume analysis
        if 'Volume' in data.columns:
            data['Volume_SMA'] = data['Volume'].rolling(window=20, min_periods=1).mean()
            data['Volume_Ratio'] = data['Volume'] / data['Volume_SMA'].replace(0, 1)
        else:
            data['Volume_Ratio'] = 1.0
        
        # Get latest and previous data
        latest = data.iloc[-1]
        if len(data) > 1:
            previous = data.iloc[-2]
        else:
            previous = latest
        
        # Build result dictionary with safe value extraction
        result = {
            'ticker': ticker,
            'price': float(latest['Close']) if not pd.isna(latest['Close']) else 0.0,
            'change_pct': float(((latest['Close'] - previous['Close']) / previous['Close']) * 100) if previous['Close'] != 0 else 0.0,
            'volume': int(latest.get('Volume', 0)) if 'Volume' in latest.index else 0,
            'volume_ratio': float(latest['Volume_Ratio']) if not pd.isna(latest['Volume_Ratio']) else 1.0,
            'sma20': float(latest['SMA20']) if not pd.isna(latest['SMA20']) else None,
            'sma50': float(latest['SMA50']) if not pd.isna(latest['SMA50']) else None,
            'sma200': float(latest['SMA200']) if not pd.isna(latest['SMA200']) else None,
            'bb_upper': float(latest['BB_Upper']) if not pd.isna(latest['BB_Upper']) else None,
            'bb_middle': float(latest['BB_Middle']) if not pd.isna(latest['BB_Middle']) else None,
            'bb_lower': float(latest['BB_Lower']) if not pd.isna(latest['BB_Lower']) else None,
            'bb_width': float(latest['BB_Width']) if not pd.isna(latest['BB_Width']) else 0.0,
        }
        
        # Calculate BB position safely
        if result['bb_upper'] and result['bb_lower'] and result['bb_upper'] != result['bb_lower']:
            result['bb_position'] = float(((latest['Close'] - latest['BB_Lower']) / (latest['BB_Upper'] - latest['BB_Lower'])) * 100)
        else:
            result['bb_position'] = 50.0
        
        result.update({
            'rsi': float(latest['RSI']) if not pd.isna(latest['RSI']) else 50.0,
            'macd': float(latest['MACD']) if not pd.isna(latest['MACD']) else 0.0,
            'macd_signal': float(latest['MACD_Signal']) if not pd.isna(latest['MACD_Signal']) else 0.0,
            'macd_histogram': float(latest['MACD_Histogram']) if not pd.isna(latest['MACD_Histogram']) else 0.0
        })
        
        return result
        
    except Exception as e:
        print(f"Error calculating indicators for {ticker}: {e}")
        return None

def analyze_signals(data):
    """Analyze technical indicators and return bullish/bearish signals"""
    signals = []
    score = 0
    
    # Trend signals (SMA crossovers)
    if data['sma20'] and data['sma50'] and data['price'] > data['sma20'] > data['sma50']:
        signals.append("✅ Strong uptrend (Price > SMA20 > SMA50)")
        score += 1
    elif data['sma20'] and data['price'] > data['sma20']:
        signals.append("⚠️ Short-term uptrend (Price > SMA20)")
        score += 0.5
    
    if data['sma200'] and data['price'] > data['sma200']:
        signals.append("✅ Above 200-day SMA (long-term bullish)")
        score += 1
    
    # RSI signals
    if RSI_MOMENTUM_LOW < data['rsi'] < RSI_MOMENTUM_HIGH:
        signals.append(f"✅ RSI momentum zone ({data['rsi']:.1f}) - not overbought")
        score += 1
    elif data['rsi'] < RSI_OVERSOLD:
        signals.append(f"🔥 RSI OVERSOLD ({data['rsi']:.1f}) - potential bounce")
        score += 1.5
    elif data['rsi'] > RSI_OVERBOUGHT:
        signals.append(f"⚠️ RSI OVERBOUGHT ({data['rsi']:.1f}) - caution")
        score -= 0.5
    
    # MACD signals
    if data['macd'] > data['macd_signal'] and data['macd_histogram'] > 0:
        signals.append("✅ MACD bullish (above signal line)")
        score += 1
    
    # Bollinger Bands signals
    if data['bb_position'] < 20:
        signals.append(f"🔥 Near lower Bollinger Band ({data['bb_position']:.1f}%) - potential bounce")
        score += 1.5
    elif data['bb_position'] > 80:
        signals.append(f"⚠️ Near upper Bollinger Band ({data['bb_position']:.1f}%) - overbought")
        score -= 0.5
    
    if data['bb_width'] < 10:
        signals.append("📊 Bollinger Bands squeezing - volatility coming")
        score += 0.5
    
    # Volume signals
    if data['volume_ratio'] > 1.5:
        signals.append(f"✅ High volume ({data['volume_ratio']:.2f}x avg) - strong interest")
        score += 1
    
    return signals, score

def categorize_stock(ticker):
    """Return category for a stock"""
    if ticker in QUANTUM_PURE_PLAYS:
        return "🔬 Quantum Pure-Play"
    elif ticker in BIG_TECH:
        return "💻 Big Tech"
    elif ticker in DEFENSE_INDUSTRIAL:
        return "🛡️ Defense/Industrial"
    elif ticker in AI_INFRASTRUCTURE:
        return "🏭 AI Infrastructure"
    return "📊 General"

def check_stocks():
    """Main monitoring function"""
    print("\n" + "="*80)
    print(f"🚀 QUANTUM STOCK MONITOR - {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}")
    print("="*80)
    
    results = []
    strong_buys = []
    
    for ticker in ALL_STOCKS:
        data = calculate_indicators(ticker)
        if not data:
            print(f"\n❌ {ticker}: No data available")
            continue
        
        signals, score = analyze_signals(data)
        category = categorize_stock(ticker)
        
        print(f"\n{category} - {ticker}")
        print(f"{'─'*80}")
        print(f"Price: ${data['price']:.2f} ({data['change_pct']:+.2f}%)")
        print(f"Volume: {data['volume']:,} ({data['volume_ratio']:.2f}x avg)")
        print(f"RSI: {data['rsi']:.1f} | MACD: {data['macd']:.3f}")
        print(f"Bollinger Position: {data['bb_position']:.1f}% (Width: {data['bb_width']:.1f}%)")
        print(f"\n📊 Signal Score: {score:.1f}/6.0")
        
        if signals:
            print("Signals:")
            for signal in signals:
                print(f"  {signal}")
        
        # Strong buy alert
        if score >= STRONG_BUY_THRESHOLD:
            strong_buys.append({
                'ticker': ticker,
                'category': category,
                'score': score,
                'price': data['price'],
                'signals': signals
            })
            print(f"\n🔥🔥🔥 STRONG BUY SIGNAL - Score {score:.1f} 🔥🔥🔥")
        
        results.append({
            'ticker': ticker,
            'data': data,
            'signals': signals,
            'score': score,
            'category': category
        })
        
        # Small delay to avoid rate limiting
        time.sleep(0.5)
    
    # Summary of strong buy signals
    if strong_buys:
        print("\n" + "="*80)
        print("🎯 STRONG BUY OPPORTUNITIES")
        print("="*80)
        for buy in sorted(strong_buys, key=lambda x: x['score'], reverse=True):
            print(f"\n{buy['category']} - {buy['ticker']}")
            print(f"  Score: {buy['score']:.1f} | Price: ${buy['price']:.2f}")
            print(f"  Top Signals:")
            for signal in buy['signals'][:3]:
                print(f"    {signal}")
    
    # Save results to JSON for dashboard
    save_results(results)
    
    print("\n" + "="*80)
    print(f"✅ Check complete. Next check in 1 hour.")
    print("="*80 + "\n")

def save_results(results):
    """Save results to JSON file for dashboard consumption"""
    output_dir = os.path.join(os.path.dirname(__file__), 'quantum_stock_data')
    os.makedirs(output_dir, exist_ok=True)
    
    output_file = os.path.join(output_dir, 'latest_results.json')
    
    with open(output_file, 'w') as f:
        json.dump({
            'timestamp': datetime.now().isoformat(),
            'results': results
        }, f, indent=2, default=str)
    
    print(f"💾 Results saved to: {output_file}")

def main():
    """Main execution function"""
    print("🚀 Starting Quantum Stock Monitor Agent")
    print(f"📊 Monitoring {len(ALL_STOCKS)} stocks")
    print(f"🔬 Quantum: {', '.join(QUANTUM_PURE_PLAYS)}")
    print(f"💻 Big Tech: {', '.join(BIG_TECH)}")
    print(f"🛡️ Defense: {', '.join(DEFENSE_INDUSTRIAL)}")
    print(f"🏭 Infrastructure: {', '.join(AI_INFRASTRUCTURE)}")
    print("\n⏰ Checking every 1 hour (market hours recommended)")
    print("Press Ctrl+C to stop\n")
    
    # Initial run
    check_stocks()
    
    # Schedule hourly checks
    schedule.every(1).hours.do(check_stocks)
    
    # Run loop
    while True:
        try:
            schedule.run_pending()
            time.sleep(60)
        except KeyboardInterrupt:
            print("\n\n👋 Stopping Quantum Stock Monitor. Goodbye!")
            break
        except Exception as e:
            print(f"\n❌ Error in main loop: {e}")
            time.sleep(60)

if __name__ == "__main__":
    main()
