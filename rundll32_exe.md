# Case Study SOC: Wykrywanie sieciowego ataku Rundll32 SMB 

## 1. Cel projektu
Celem tego projektu laboratoryjnego jest przeprowadzenie, analiza oraz wykrycie zaawansowanego ataku typu **Signed Binary Proxy Execution** z wykorzystaniem zaufanego, cyfrowo podpisanego narzędzia systemowego Windows – **Rundll32.exe** (technika sklasyfikowana w MITRE ATT&CK jako **T1218.011**). 

W scenariuszu zasymulowałem pełny wektor sieciowy (C2 / Attacker Node), w którym stacja robocza ofiary pobiera i próbuje wykonać zasób ze zdalnego serwera kontrolowanego przez napastnika. Projekt został zaprojektowany ze szczególnym naciskiem na **brak agenta Sysmon**. Oznacza to, że cała telemetria opiera się na **natywnych logach systemowych Windows (Windows Security Event Logs – Event ID 4688 z włączonym pełnym audytem linii poleceń)** zebranych i przeanalizowanych w systemie SIEM Splunk.

## 2. MITRE ATT&CK Mapowanie

| Tactic | Technique ID | Technique Name | Data Sources |
| :--- | :--- | :--- | :--- |
| **Defense Evasion / Execution** | T1218.011 | Signed Binary Proxy Execution: Rundll32 | Process: Process Creation, Command Line Execution |

### Wyjaśnienie taktyczne:
Atakujący wykorzystują legalny plik binarny systemu Windows (`rundll32.exe`) do załadowania zewnętrznej biblioteki DLL bezpośrednio z zasobu sieciowego (np. protokół SMB udostępniony przez maszynę Kali Linux). Ponieważ `rundll32.exe` jest podpisany cyfrowo przez Microsoft, mechanizmy oparte na reputacji plików zazwyczaj nie blokują jego uruchomienia. Pozwala to na ominięcie restrykcji kontroli aplikacji oraz ukrycie złośliwej aktywności pod płaszczykiem standardowego ruchu operacyjnego.

## 3. Metodologia ataku

### Krok 1: Przygotowanie środowiska Windows (Audyt linii poleceń)
Aby zarejestrować pełną ścieżkę sieciową oraz argumenty przekazane do procesu bez obecności Sysmona, musimy upewnić się, że w systemie Windows włączony jest audyt tworzenia procesów.

* **Komenda (PowerShell jako Administrator na Windows 10):**
  ```powershell
  auditpol /set /subcategory:"Process Creation" /success:enable /failure:enable

### Krok 2: Uruchomienie węzła atakującego na Kali Linux (Serwer SMB)
Atakujący na maszynie Kali Linux przygotowuje folder roboczy oraz uruchamia serwer SMB za pomocą pakietu Impacket, udostępniając zasób w sieci lokalnej.

<img width="675" height="533" alt="1" src="https://github.com/user-attachments/assets/0ffa0ccb-837f-46cc-9f97-19debbf36619" />

* **Komenda (Kali Linux):**
  ```
  sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py share /tmp/smbFolder -smb2support ``` 
