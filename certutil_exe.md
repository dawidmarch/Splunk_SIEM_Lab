# Case Study: Certutil.exe

## 1. Cel projektu
Celem projektu jest wdrożenie procedury detekcyjnej dla techniki **Ingress Tool Transfer (MITRE ATT&CK T1105)**. Scenariusz zakłada wykorzystanie legalnego narzędzia systemowego (`certutil.exe`) do pobrania złośliwego payloadu z serwera zewnętrznego. 

## 2. MITRE ATT&CK Mapowanie
| Taktika | Technika | ID |
| :--- | :--- | :--- |
| Execution | Command and Scripting Interpreter | T1059 |
| Command and Control | Ingress Tool Transfer | T1105 |

*Wyjaśnienie:* Napastnik wykorzystuje `certutil.exe` do pobrania pliku z zewnętrznego serwera, co jest klasyczną techniką mającą na celu uniknięcie detekcji przez sygnaturowe rozwiązania AV/EDR, które ufają podpisanym cyfrowo narzędziom Microsoftu.
