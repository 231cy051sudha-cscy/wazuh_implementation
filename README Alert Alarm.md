SOC Final Year Project – Real-Time Wazuh Alert Monitoring with Alarm
Project Overview

This project is a mini SOC alert monitoring system designed for beginners and cybersecurity researchers.

It allows you to:

Monitor Wazuh alerts in real-time

Play alarm sounds for high-severity events

Detect attacks such as brute-force login attempts or failed login patterns

Support both Windows and Linux agents

Goal: Provide a hands-on SOC environment where users can simulate attacks and immediately see alerts.

Detection Techniques Implemented

This project leverages Wazuh’s built-in rules and logs to detect security incidents. Detection techniques include : 
| Technique                         | Description                                              | Example                                       |
| --------------------------------- | -------------------------------------------------------- | --------------------------------------------- |
| **Failed login detection**        | Monitors repeated authentication failures                | PAM logs (Linux) or Event ID 4625 (Windows)   |
| **Brute-force detection**         | Correlates multiple login failures within a short period | Wazuh “Multiple failed logons” rule           |
| **System hardening violations**   | Detects AppArmor or SELinux denials                      | Level 3 alerts for AppArmor denied operations |
| **Unauthorized access attempts**  | Detects suspicious file or process access                | Audit logs from kernel or Sysmon on Windows   |
| **Alert severity classification** | Assigns severity levels to each event                    | Wazuh rule levels: 0–15                       |

Project Requirements
Ubuntu 24.04 or similar Linux with Wazuh Manager installed
Wazuh Agents installed on monitored machines (Windows/Linux)
Python 3.x
mpg123 for audio alerts
Optional: Hydra (for testing brute-force attacks)

Installation & Setup
1) Install Dependencies
sudo apt update
sudo apt install python3 python3-pip mpg123 hydra -y
2) Create Project Directory
mkdir -p ~/soc_alerts
cd ~/soc_alerts
3) Download Alarm Sound
   wget -O /home/sudha/alert_alarm.mp3 https://www.orangefreesounds.com/wp-content/uploads/2014/11/Beep-sound.mp3
Ensure file exists and is non-zero:
ls -l /home/sudha/alert_alarm.mp3
Python Alert Monitor Script :
Create file:
nano ~/soc_alerts/wazuh_alert_alarm.py # inside this paste below script

#!/usr/bin/env python3
import json
import subprocess
import time 

ALERT_FILE = "/var/ossec/logs/alerts/alerts.json"
ALARM_SOUND = "/home/sudha/alert_alarm.mp3"
THRESHOLD = 5  # Trigger alarm for level 5+

def play_alarm():
    subprocess.run(['mpg123', ALARM_SOUND])

def monitor_alerts():
    try:
        with open(ALERT_FILE, 'r') as f:
            f.seek(0, 2)  # Go to end of file
            while True:
                line = f.readline()
                if not line:
                    time.sleep(0.5)
                    continue
                try:
                    alert = json.loads(line.strip())
                    rule_info = alert.get('rule', {})
                    level = rule_info.get('level', 0)
                    description = rule_info.get('description', 'No description')
                    agent_name = alert.get('agent', {}).get('name', 'Unknown')

                    if level >= THRESHOLD:
                        print(f"[ALERT] Level: {level} | Agent: {agent_name} | Rule: {description}")
                        play_alarm()

                except json.JSONDecodeError:
                    continue
    except PermissionError:
        print(f"Permission denied! Run with sudo: sudo -E python3 {__file__}")

if __name__ == "__main__":
    monitor_alerts()

    Make it executable:
chmod +x ~/soc_alerts/wazuh_alert_alarm.py
Running the Script
sudo -E python3 ~/soc_alerts/wazuh_alert_alarm.py

Keeps running and waits for new alerts
Plays alarm sound for any alert with level ≥ THRESHOLD
Prints alert details: [ALERT] Level: X | Agent: Y | Rule: Z

Testing the System:
Manual Test:
sudo bash -c 'echo "{\"rule\":{\"level\":7,\"description\":\"Test alert\"},\"agent\":{\"name\":\"SOC-Test\"}}" >> /var/ossec/logs/alerts/alerts.json'
Terminal prints alert
Alarm plays

Simulated Attack Test:
On Kali Linux, run Hydra brute-force:
hydra -l administrator -P rockyou.txt rdp://<windows_ip>

Monitor Wazuh alerts in real-time:
sudo tail -f /var/ossec/logs/alerts/alerts.json
Level ≥ THRESHOLD will trigger alarm and print alert


| Command                                           | Purpose                         |
| ------------------------------------------------- | ------------------------------- |
| `mkdir -p ~/soc_alerts`                           | Create project folder           |
| `wget -O /home/sudha/alert_alarm.mp3 <url>`       | Download alarm MP3              |
| `chmod +x wazuh_alert_alarm.py`                   | Make script executable          |
| `sudo -E python3 wazuh_alert_alarm.py`            | Run script with root privileges |
| `sudo tail -f /var/ossec/logs/alerts/alerts.json` | Monitor alerts live             |
| `hydra -l user -P passwords rdp://IP`             | Simulate brute-force attack     |
| `nohup command &`                                 | Run script in background        |

