### Auto-cpuFreq - Automatic CPU Frequency Daemon
Auto-cpuFreq is a linux daemon that automatically makes on-the-fly adjustments to the CPU's governor state, toggles turbo-boost, and provides CLI monitoring. The project's goal is to increase performance and battery life on laptops running linux. 

#### Installation
To install from source and and run auto-cpufreq and a system daemon:
```
git clone https://github.com/AdnanHodzic/auto-cpufreq.git
cd auto-cpufreq
sudo ./auto-cpufreq-installer
sudo auto-cpufreq --install
# After auto-cpufreq in installed,
# Run the following command to monitor the daemon
sudo auto-cpufreq --stats
```

See Github Repo in references for other installation options
##### References
[Auto-cpuFreq Github Repo](https://github.com/AdnanHodzic/auto-cpufreq)
[Auto-cpuFreq Author's Blog Post](https://foolcontrol.org/?p=3124)