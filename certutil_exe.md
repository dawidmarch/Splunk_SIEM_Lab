# Case Study: Certutil.exe

## 1. Cel projektu
Celem projektu jest wdrożenie procedury detekcyjnej dla techniki **Ingress Tool Transfer (MITRE ATT&CK T1105)**. Scenariusz zakłada wykorzystanie legalnego narzędzia systemowego (`certutil.exe`) do pobrania złośliwego payloadu z serwera zewnętrznego. 

## 2. MITRE ATT&CK Mapowanie
| Taktika | Technika | ID |
| :--- | :--- | :--- |
| Execution | Command and Scripting Interpreter | T1059 |
| Command and Control | Ingress Tool Transfer | T1105 |
