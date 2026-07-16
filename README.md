# Splunk_SIEM_Lab

Projekt prezentuje proces wdrożenia i konfiguracji systemu SIEM w oparciu o rozwiązanie Splunk Enterprise. Celem laboratorium było nawiązanie stabilnej komunikacji między agentem (Universal Forwarder) na systemie Windows 10 a serwerem indeksującym na Kali Linux, w celu centralnego zbierania logów i weryfikacji przepływu danych.

## Hardware & Host OS 

* **System operacyjny hosta:** Windows 10 Pro (64-bit)
* **Procesor (CPU):** Intel(R) Core(TM) i7-2600 CPU @ 3.40GHz
* **Pamięć RAM:** 32,0 GB
* **Dysk:** SSD Patriot P210 512GB

## Software Stack
* **Hiperwzorzec:** Oracle VirtualBox (wersja 7.2.10) – posłużył do stworzenia izolowanej sieci, w której maszyny komunikują się w obrębie podsieci `192.168.0.0/24`, zapewniając stabilne środowisko laboratoryjne.
* **Splunk Indexer (Serwer):** Kali Linux – `192.168.0.105` (Główna instancja Splunk Enterprise odbierająca logi na porcie 9997).
* **Endpoint (Ofiara):** Windows 10 Pro – `192.168.0.110` (Zainstalowany Splunk Universal Forwarder).
* **System Atakującego:** Kali Linux – `192.168.0.107` (Platforma przygotowana do generowania ruchu symulującego ataki).
