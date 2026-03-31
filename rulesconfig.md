sudo cat /var/ossec/etc/rules/local_rules.xml
<!-- SOC ML + Burst Detection Rules -->

<group name="soc_ml_detection,ai_soc_research,">

  <!-- Base Keyword Detection Rule -->
  <rule id="100500" level="12">
    <match>attack|malware|exploit|unauthorized|scan</match>
    <description>
      SOC ML Attack Keyword Detected
    </description>
    <group>ai_soc_research,keyword_detection</group>
  </rule>

  <!-- Burst Correlation Rule -->
  <rule id="100901" level="15" frequency="5" timeframe="60">
    <if_matched_sid>100500</if_matched_sid>
    <same_source_ip />
    <description>
      AI Burst Anomaly: Repeated attack events from same source
    </description>
    <group>ai_soc_research,burst_detection,critical</group>
    <options>no_full_log</options>
  </rule>                                                                                                                          </group>


<!-- SSH Spike Anomaly Detection -->

<group name="local,ssh_anomaly,">

  <rule id="100100" level="14" frequency="15" timeframe="60">
    <if_matched_sid>5710</if_matched_sid>
    <same_source_ip />
    <description>
      ANOMALY: SSH login failure spike detected
    </description>
  </rule>

</group>
