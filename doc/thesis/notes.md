# Remarks

* inspiration in immediate GUIs (what I see is exactly how the document is structured)
* CSDDM allows to ensure, that no data are removed (just check, that no permissions contain `rem`)
* OLAP support?

# Sources

document DB modelling https://docs.mongodb.org/manual/core/data-modeling-introduction/
document referencing versus embedding https://docs.mongodb.org/manual/core/data-model-design/#data-modeling-referencing
documents have metadata and can contain arbitrary number (0..N) of items (and non-uniformly among the "same" records) https://en.wikipedia.org/wiki/Document-oriented_database#Documents
production-ready pure relational database SIRA_PRISE in Java http://shark.armchair.mb.ca/~erwin/ (uses prefix notation and doesn't have the flaws of SQL)
DataScript in-memory DB (based on Datomic/Datalog) https://github.com/tonsky/datascript
Datomic proprietary DB (based on Datalog) http://docs.datomic.com/query.html
Datalog Education System (has ODBC backend and thus can be used with SQLite locally or any other remote ODBC service) http://sourceforge.net/projects/des/
Datalog to SQL using Jelly Views http://home.agh.edu.pl/~wojnicki/phd/node1.html

# KONTROLA KVALITY DOKUMENTU

40 normostran (kazda 1800 znaku) => 72000; zcela vlastni obrazek nahrazuje text se srovnatelnou plochou
Citace: ČSN 01 0197
Počet znaku na radek max. 75
Vetsi bile místo na vnejsim hrbetu a dole
Pouzivat hlavicky a paticky
Pismo Antiqua/Palation/… 12pt, proklad 120%
Kontrola:
* Uvozovky
* Pomlcka – , spojovnik - , rozdelovnik ???
* Zalamovani radku a stranek (vdovy a sirotci)
* Mezery u interpunkcnich znaku a citaci
* Nejednotnost interpunkce ve vyctech a popisech obrazku a tabulek
* Nepouzivat „marketingove“ nazvy (technologie, reseni)
