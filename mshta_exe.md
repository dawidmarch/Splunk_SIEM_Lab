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
