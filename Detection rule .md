# rule based detection verifiying and brute force attack using hydra 
# to check the soc sever !
sudo tail -f /var/ossec/logs/alerts/alerts.json | grep ssh 
#t7: on kali linux 
Hydra →
SSH log entries →
/var/log/auth.log →
Decoder matched →
Rule 5710 triggered →
Repeated attempts →
Correlation rule 2502 triggered (Level 10) →
Alert stored →
SOC detects brute force
This is a brute-force simulation using Hydra from Kali Linux.
And Wazuh correctly detected it.
Expected levels:
5 → single failed login
7 → multiple attempts
8+ → brute force behavior
If you see level increasing = frequency rule working.

That means:
✔ Rule-based detection works
✔ Frequency-based escalation works
✔ MITRE mapping works
✔ IP tracking works
✔ Severity scaling works
This is real detection.
