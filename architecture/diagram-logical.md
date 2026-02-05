# Logical Architecture Diagram
          
      ## Logical Architecture Diagram 

```text
                    [ Simulated Internet ]
                              |
                              |
                       +----------------+
                       |    pfSense     |
                       |    Firewall    |
                       +--------+-------+
                                |
               -----------------------------------
               |                                 |
        +--------------+                 +--------------+
        | Linux Agent  |                 | Windows Agent|
        +------+-------+                 +------+-------+
               |                                 |
               +---------------+-----------------+
                               |
                       +------------------+
                       |  Wazuh Manager   |
                       |   SIEM / SOC     |
                       +--------+---------+
                                |
                       +------------------+
                       |  SOC Analyst VM  |
                       | Investigation   |
                       +------------------+



## 📌 Notes
- All traffic and logs are contained within the lab
- No direct internet exposure for internal hosts
- Architecture mirrors real SOC environments
