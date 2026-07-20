# Case Study: Detekcja BITSAdmin (LOLBin) w Środowisku Windows

## 1. Cel projektu
Wykrycie wykorzystania natywnego narzędzia systemowego `bitsadmin.exe` do transferu plików z zewnętrznego serwera. Celem jest identyfikacja wrogiej aktywności typu "Living-off-the-Land" (LOLBin) poprzez analizę logów audytu procesów Windows (Event ID 4688).

## 2. MITRE ATT&CK Mapowanie
| Tactic | Technique | ID |
| :--- | :--- | :--- |
| Defense Evasion | Masquerading | T1036 |
| Command and Control | Ingress Tool Transfer | T1105 |

*Wyjaśnienie:* `bitsadmin.exe` jest natywnym narzędziem Windowsa używanym do zarządzania transferami plików. Atakujący wykorzystują je do pobierania payloadu, co pozwala na ominięcie detekcji opartych na aktywności przeglądarek.
