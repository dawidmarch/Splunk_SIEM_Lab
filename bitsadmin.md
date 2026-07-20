# Case Study: Detekcja BITSAdmin (LOLBin) w Środowisku Windows

## 1. Cel projektu
Wykrycie wykorzystania natywnego narzędzia systemowego `bitsadmin.exe` do transferu plików z zewnętrznego serwera. Celem jest identyfikacja wrogiej aktywności typu "Living-off-the-Land" (LOLBin) poprzez analizę logów audytu procesów Windows (Event ID 4688).

## 2. MITRE ATT&CK Mapowanie
| Tactic | Technique | ID |
| :--- | :--- | :--- |
| Defense Evasion | Masquerading | T1036 |
| Command and Control | Ingress Tool Transfer | T1105 |

*Wyjaśnienie:* `bitsadmin.exe` jest natywnym narzędziem Windowsa używanym do zarządzania transferami plików. Atakujący wykorzystują je do pobierania payloadu, co pozwala na ominięcie detekcji opartych na aktywności przeglądarek.

## 3. Metodologia ataku
### Krok 1: Przygotowanie środowiska (C2)

<img width="665" height="539" alt="1" src="https://github.com/user-attachments/assets/df665b98-8f9f-44d7-98b9-4ead1d4d072e" />

- Uruchomiono serwer HTTP na maszynie atakującej (Kali Linux) w celu serwowania pliku testowego.
- Komenda: `python3 -m http.server 80`

### Krok 2: Weryfikacja kanału komunikacji

<img width="533" height="367" alt="2" src="https://github.com/user-attachments/assets/88e0e93e-28ef-4d0d-9d2e-177e598740d3" />

Potwierdzono dostępność pliku przez protokół HTTP z maszyny docelowej.

### Krok 3: Wykonanie ataku (Windows 10)

<img width="1011" height="605" alt="3" src="https://github.com/user-attachments/assets/d28fb01d-3588-49c5-b5d9-c21faca7edfb" />

Uruchomiono `bitsadmin` z uprawnieniami administratora w celu pobrania pliku do katalogu publicznego.
