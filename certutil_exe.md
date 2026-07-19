# Case Study: Certutil.exe

## 1. Cel projektu
Celem projektu jest wdrożenie procedury detekcyjnej dla techniki **Ingress Tool Transfer (MITRE ATT&CK T1105)**. Scenariusz zakłada wykorzystanie legalnego narzędzia systemowego (`certutil.exe`) do pobrania złośliwego payloadu z serwera zewnętrznego. 

## 2. MITRE ATT&CK Mapowanie
| Taktika | Technika | ID |
| :--- | :--- | :--- |
| Execution | Command and Scripting Interpreter | T1059 |
| Command and Control | Ingress Tool Transfer | T1105 |

*Wyjaśnienie:* Napastnik wykorzystuje `certutil.exe` do pobrania pliku z zewnętrznego serwera, co jest klasyczną techniką mającą na celu uniknięcie detekcji przez sygnaturowe rozwiązania AV/EDR, które ufają podpisanym cyfrowo narzędziom Microsoftu.

## 3. Metodologia ataku

### Przygotowanie infrastruktury (Kali Linux)
Na maszynie atakującej wygenerowano payload przy użyciu `msfvenom` oraz uruchomiono serwer HTTP, aby wystawić plik w sieci lokalnej.

<img width="683" height="623" alt="2" src="https://github.com/user-attachments/assets/8c2d434a-486b-4264-a7ba-93c95168af56" />

Przygotowanie payloadu (`msfvenom`) oraz uruchomienie serwera HTTP (Python).*

### Egzekucja ataku (Windows 10)
Na stacji roboczej Windows 10 wykorzystano narzędzie `certutil.exe` do pobrania złośliwego pliku z serwera Kali. Jest to działanie, które w środowisku produkcyjnym często pozostaje niezauważone, jeśli nie monitoruje się aktywności sieciowej procesów.

<img width="855" height="687" alt="1" src="https://github.com/user-attachments/assets/5b1ad00c-9627-487a-85ca-1550b73ca004" />

Wykonanie ataku przy użyciu `certutil.exe`. Widoczne zakończenie sukcesem oraz ścieżka zapisu pliku `C:\Temp\payload.exe`.*

## 4. Analiza logów (Splunk SIEM)
W celu detekcji ataku, przeprowadzono analizę logów w systemie Splunk. Zidentyfikowano zdarzenie `EventCode 5156` (Windows Filtering Platform Connection), które jest kluczowym wskaźnikiem (IOC) dla ruchu sieciowego generowanego przez ten proces.

<img width="843" height="573" alt="3" src="https://github.com/user-attachments/assets/566294a9-d965-4d58-9efd-e0fb0db9b34e" />

Log w Splunku wskazujący na połączenie sieciowe wychodzące z procesu `certutil.exe` (PID: 5636) do serwera zewnętrznego (192.168.0.107).*
