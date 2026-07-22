# Case Study: Detekcja i Analiza Proxy Execution z Użyciem LOLBin (mshta.exe) w Środowisku Windows / Splunk Enterprise

## 1. Cel projektu
Celem tego projektu laboratoryjnego jest przeprowadzenie, zarejestrowanie oraz wykrycie zaawansowanego ataku typu **Living off the Land (LotL)** z wykorzystaniem natywnego, podpisanego cyfrowo narzędzia systemowego Windows – **`mshta.exe`** (Microsoft HTML Application Host).

Atak polega na zdalnym pobraniu i wykonaniu złośliwego skryptu HTA (HTML Application) z serwera atakującego (Kali Linux), co pozwala na ominięcie podstawowych mechanizmów kontroli aplikacji oraz ukrycie złośliwej aktywności pod płaszczykiem legalnego procesu systemowego.

W ramach projektu skonfigurowano odpowiednią telemetrię na stacji roboczej Windows 10, przesłano logi do **Splunk Enterprise** za pomocą **Splunk Universal Forwarder**, przeprowadzono symulację ataku oraz zaprojektowano zapytanie SPL do detekcji zagrożenia.
