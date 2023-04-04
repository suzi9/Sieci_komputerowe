## Ładowanie konfiguracji:

Załadowanie konfiguracji wykonujemy tylko raz na danym routerze. Aby następnie na tej konfiguracji mogła\
pracować cała grupa. 
```
> configure private
# load override LAN-gr1-dd.mm.rrr
# commit
```
Osoby które zalogowały się na router przed załadowaniem konfiguracji, muszą wykonać polecenie:
```
# update
```

## Zapisywanie konfiguracji:

Konfigurację powinna zapisać ostatnia osoba która planuje zakończyć działania na dzisiaj.\
Ale jeżeli została by kilka razy zapisana, to się nic nie stanie bo jest wtedy nadpisywana.\
Ważne aby zrobić "commit" przed zapisaniem konfiguracji.
```
# commit
# update
# save LAN-gr1-dd.mm.rrr
```

