# 📢 Community Testing Call - rudder2snipe

## 🚀 New Open Source Tool: rudder2snipe

I've forked and adapted the excellent **jamf2snipe** project to create **rudder2snipe** - a tool that synchronizes asset inventory between **Rudder.io** and **Snipe-IT**.

### 🔄 What it does:
- **Automatically syncs** nodes from Rudder to Snipe-IT assets
- **Maps custom fields** between systems  
- **Manages user assignments** and asset tags
- **Handles large environments** with rate limiting
- **Provides dry-run mode** for safe testing

### 🤝 Looking for Community Testers!

**We need your help to test this tool in real environments:**
- ✅ Small offices (10-100 assets)
- ✅ Enterprise deployments (100-1000+ assets)
- ✅ Different OS combinations (Linux, Windows, etc.)
- ✅ Various Rudder and Snipe-IT configurations

### 🛠️ Easy to Try:
```bash
git clone https://github.com/norbertoaquino/rudder2snipe.git
cd rudder2snipe
pip install -r requirements.txt
python3 rudder2snipe --connection_test  # Test your setup
python3 rudder2snipe --dryrun --verbose  # Safe test run
```

### 📚 Full Documentation Available:
- **Installation:** Step-by-step for all platforms
- **Configuration:** API mapping and custom fields
- **Examples:** Small office to enterprise setups
- **Troubleshooting:** Common issues and solutions

### 🎯 What We're Testing:
- Installation process across different environments
- Configuration complexity and edge cases
- Performance with various dataset sizes
- Error handling and recovery
- Documentation accuracy

### 📝 How to Help:
- **Try it out** in your test environment (always start with `--dryrun`)
- **Report success stories** - what worked well?
- **File issues** for bugs or feature requests
- **Share feedback** on documentation and usability

### 🔗 Links:
- **Repository:** https://github.com/norbertoaquino/rudder2snipe
- **Wiki:** https://github.com/norbertoaquino/rudder2snipe/wiki
- **Issues:** https://github.com/norbertoaquino/rudder2snipe/issues
- **Discussions:** https://github.com/norbertoaquino/rudder2snipe/discussions

### 🏆 Credits:
Based on the fantastic **jamf2snipe** by Brian Monroe - adapted for Rudder.io environments.

---

**Ready to help?** ⭐ Star the repo and let's make this tool awesome together!

*Your testing and feedback will help create a robust asset management solution for the Rudder community.*

#Rudder #SnipeIT #AssetManagement #OpenSource #Testing #DevOps #ITAM
