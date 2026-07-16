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

## 1. Implementacja techniczna

Proces wdrożenia wymagał precyzyjnej konfiguracji rurociągu danych (data pipeline) oraz wyeliminowania przeszkód sieciowych.

### Konfiguracja połączenia
* **Splunk Receiver:** Skonfigurowano port `9997` na serwerze Linux (`192.168.0.105`) jako główny punkt wejścia dla logów z agentów.
* **Universal Forwarder:** Wdrożono konfigurację `outputs.conf` oraz `inputs.conf` na hoście Windows (`192.168.0.110`), wskazując serwer indeksujący jako cel przesyłu danych.

**Konfiguracja `outputs.conf` na Windows:**
```ini
[tcpout]
defaultGroup = default-autolb-group

[tcpout:default-autolb-group]
server = 192.168.0.105:9997
```
Rozwiązywanie problemów: Kluczowym etapem była diagnostyka sieciowa, polegająca na otwarciu portu 9997 w systemie Linux (zarządzanie regułami iptables oraz nftables) oraz rozwiązaniu problemów z uprawnieniami w systemie Windows. w .md

## 2. Weryfikacja przepływu danych (Test "Oneshot")

Aby potwierdzić poprawność działania "rury" (pipeline), wykonano test przesyłu pliku testowego `hosts` z systemu Windows do Splunka za pomocą komendy `.\splunk add oneshot`. Pozytywny wynik testu (`Added the following monitor...`) potwierdził drożność komunikacji między stacją roboczą a serwerem SIEM.

**Komenda weryfikacyjna:**

```powershell
.\splunk add oneshot "C:\Windows\System32\drivers\etc\hosts" -index main -sourcetype test_data
```
