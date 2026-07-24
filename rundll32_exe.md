# Case Study SOC: Wykrywanie sieciowego ataku Rundll32 SMB 

## 1. Cel projektu
Celem tego projektu laboratoryjnego jest przeprowadzenie, analiza oraz wykrycie zaawansowanego ataku typu **Signed Binary Proxy Execution** z wykorzystaniem zaufanego, cyfrowo podpisanego narzędzia systemowego Windows – **Rundll32.exe** (technika sklasyfikowana w MITRE ATT&CK jako **T1218.011**). 

W scenariuszu zasymulowałem pełny wektor sieciowy (C2 / Attacker Node), w którym stacja robocza ofiary pobiera i próbuje wykonać zasób ze zdalnego serwera kontrolowanego przez napastnika. Projekt został zaprojektowany ze szczególnym naciskiem na **brak agenta Sysmon**. Oznacza to, że cała telemetria opiera się na **natywnych logach systemowych Windows (Windows Security Event Logs – Event ID 4688 z włączonym pełnym audytem linii poleceń)** zebranych i przeanalizowanych w systemie SIEM Splunk.
