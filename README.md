# Socks

# 🚀 Quantum Computing Stock Monitor & Research Dashboard

Complete system for monitoring and analyzing quantum computing stocks with automated technical analysis and comprehensive market research.

## 📦 What You Get

### 1. Python Monitoring Agent (`quantum_stock_monitor.py`)
- **Real-time stock tracking** for 13 quantum-related stocks
- **Technical indicators**: RSI, MACD, Bollinger Bands, SMA (20/50/200)
- **Signal scoring**: Automated STRONG BUY alerts when 3+ bullish signals align
- **Hourly checks**: Continuous monitoring during market hours
- **JSON export**: Data saved for dashboard consumption

### 2. Research Dashboard (`quantum-stock-dashboard.html`)
- **Visual interface** for market intelligence
- **Stock categorization**: Quantum pure-plays, Big Tech, Defense, AI Infrastructure
- **Market insights**: $1.2B quantum sensing market by 2032, 17-18% CAGR
- **Technology overview**: Qubit modalities, Q-CTRL AI software, applications
- **Catalyst timeline**: Upcoming milestones through 2030

## 📊 Stocks Monitored

### 🔬 Quantum Pure-Plays (HIGH GROWTH)
- **IONQ** - Trapped-ion systems | 112% revenue growth | 118% upside potential
- **RGTI** - Superconducting arrays | DOE contracts | Recent 13% rally
- **QUBT** - Photonic QEC | NASA partnerships | 24% YTD gains

### 💻 Big Tech
- **IBM** - 1,121-qubit Condor roadmap to 2028
- **GOOGL** - Willow chip (105 qubits)
- **MSFT** - Topological qubits (1M+ target)
- **AMZN** - AWS Braket quantum cloud

### 🛡️ Defense & Industrial
- **HON** - Quantinuum (64-qubit H-series)
- **LMT** - Quantum navigation systems

### 🏭 AI Infrastructure (BENEFICIARIES)
- **VRT** - Data center cooling | Crushed Nvidia YTD
- **ETN** - Power systems | Doubled AI demand
- **FIX** - HVAC for data centers
- **HDSN** - Refrigerants & thermal management

## 🚀 Quick Start

### Prerequisites
```bash
# Python 3.8 or higher
python --version

# Install dependencies
pip install yfinance pandas schedule
```

### Running the Agent
```bash
# Start monitoring
python quantum_stock_monitor.py

# Output includes:
# - Hourly technical analysis for all 13 stocks
# - Signal scores (0-6 scale)
# - STRONG BUY alerts (score ≥3)
# - Data export to quantum_stock_data/latest_results.json
```

### Using the Dashboard
```bash
# Open in browser
open quantum-stock-dashboard.html

# Features:
# - Market overview and statistics
# - Stock watchlist with descriptions
# - Technical analysis framework
# - Catalyst timeline
# - CTA buttons for agent download/instructions
```

## 📈 Signal Scoring System

The agent uses a composite scoring system (0-6 points):

| Signal | Score | Meaning |
|--------|-------|---------|
| Price > SMA20 > SMA50 | +1.0 | Strong uptrend |
| Price > SMA200 | +1.0 | Long-term bullish |
| RSI 50-70 | +1.0 | Momentum without overbought |
| RSI < 30 | +1.5 | Oversold bounce opportunity |
| MACD > Signal | +1.0 | Bullish momentum |
| BB Position < 20% | +1.5 | Near lower band, bounce likely |
| Volume > 1.5x avg | +1.0 | Strong conviction |

**STRONG BUY Alert** triggers at score ≥3.0

## 🎯 Key Market Insights (from Grok's Research)

1. **Government Investment Rumors** (Oct 23, 2025)
   - Quantum stocks rallied 9-13% on equity stake news
   - IONQ, RGTI leading the surge

2. **Quantum Sensing Market**
   - $340M (2024) → $1.2B (2032)
   - 17-18% CAGR
   - $1.25B+ global investment

3. **Fault-Tolerant Computing Timeline**
   - IBM targeting 2029-2030 for scalable systems
   - Microsoft's topological qubits aim for 1M+ qubits
   - Commercial deployment 2026-2027

4. **AI Infrastructure Boom**
   - VRT beat earnings on data center cooling demand
   - $85B+ AI capex forecast benefits ETN, FIX, HDSN

## 🔧 Technical Indicators Explained

### RSI (Relative Strength Index)
- **< 30**: Oversold (potential buy)
- **50-70**: Bullish momentum
- **> 70**: Overbought (caution)

### MACD (Moving Average Convergence Divergence)
- **Above signal line**: Bullish
- **Positive histogram**: Buying pressure
- **Crossover up**: Buy signal

### Bollinger Bands
- **< 20% position**: Near lower band (bounce likely)
- **> 80% position**: Near upper band (overbought)
- **Width < 10%**: Squeeze (volatility coming)

### Volume
- **> 1.5x average**: High interest/conviction
- **Confirms price moves**: Trend validity

## 📅 Monitoring Schedule

**Recommended:** Run during market hours (9:30 AM - 4:00 PM ET)

- Agent checks every **1 hour**
- Saves JSON data after each check
- Dashboard can auto-refresh (optional)

**For 24/7 monitoring:**
```bash
# Run in background (Linux/Mac)
nohup python quantum_stock_monitor.py &

# Check logs
tail -f nohup.out
```

## 🎓 Usage Tips

1. **Start Before Market Open**
   - Get baseline readings
   - Catch pre-market moves

2. **Watch for Score Changes**
   - Rising scores = Building momentum
   - STRONG BUY alerts = Take action

3. **Cross-Reference News**
   - Government contracts
   - Earnings reports
   - Quantum breakthroughs

4. **Diversify Categories**
   - Mix quantum pure-plays with infrastructure
   - Balance high-risk/high-reward

5. **Set Price Alerts**
   - Use broker alerts with agent signals
   - Double confirmation strategy

## 🚨 Disclaimer

**This is NOT financial advice.**

- Stock trading involves risk
- Past performance ≠ future results
- Do your own research
- Consult financial advisors
- Use at your own risk

The agent provides technical analysis signals but cannot predict market movements. Always conduct due diligence before investing.

## 📝 File Structure

```
quantum-stock-system/
├── quantum_stock_monitor.py       # Python monitoring agent
├── quantum-stock-dashboard.html   # Research dashboard
├── README.md                      # This file
└── quantum_stock_data/            # Auto-created
    └── latest_results.json        # Agent output
```

## 🔄 Updates & Maintenance

### Adding New Stocks
Edit `quantum_stock_monitor.py`:
```python
# Add to appropriate category list
QUANTUM_PURE_PLAYS = ['IONQ', 'RGTI', 'QUBT', 'YOUR_STOCK']
```

### Adjusting Signal Thresholds
```python
STRONG_BUY_THRESHOLD = 3  # Change to 2 or 4
RSI_MOMENTUM_LOW = 50     # Adjust RSI ranges
```

### Changing Check Frequency
```python
schedule.every(1).hours.do(check_stocks)  # Change to .minutes for testing
```

## 💡 Future Enhancements

Potential additions:
- Email/SMS alerts for STRONG BUY signals
- Historical backtesting
- Portfolio tracking
- Real-time dashboard updates via WebSocket
- Machine learning price predictions
- Sentiment analysis from news

## 🤖 Credits

- **Research & Strategy**: Grok (xAI) - Quantum computing market analysis
- **Agent Development**: Claude (Anthropic) - Python monitoring system
- **Dashboard Design**: Claude - Visual research interface

A collaborative effort combining Grok's market intelligence with Claude's technical implementation.

## 📞 Support

For issues or questions:
1. Check signal scoring logic in agent code
2. Verify yfinance is fetching data correctly
3. Ensure JSON file is being created
4. Review market hours (9:30 AM - 4:00 PM ET)

---

**Last Updated**: October 27, 2025

**Version**: 1.0

**Status**: Production Ready ✅

🚀 **Happy Trading!** May your signals be strong and your gains be quantum! 📈
