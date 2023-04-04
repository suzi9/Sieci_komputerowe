## Ładowanie konfiguracji:

Załadowanie konfiguracji wykonujemy tylko raz na danym routerze. Aby następnie na tej konfiguracji mogła\
pracować cała grupa. 
```ps1
> configure private
 load override LAN-gr1-dd.mm.rrr
 commit
```
Osoby które zalogowały się na router przed załadowaniem konfiguracji, muszą wykonać polecenie:
```ps1
 update
```

## Zapisywanie konfiguracji:

Konfigurację powinna zapisać ostatnia osoba która planuje zakończyć działania na dzisiaj.\
Ale jeżeli została by kilka razy zapisana, to się nic nie stanie bo jest wtedy nadpisywana.\
Ważne aby zrobić "commit" przed zapisaniem konfiguracji.
```ps1
 commit
 update
 save LAN-gr1-dd.mm.rrr
```

## Wyświetlanie interfejsów
Wyświetlanie wszystkich szczegółów o interfejsach:
```ps1
 show interfaces
```
Natomiast gdy potrzebujemy krótkiego podsumowania o interfejsach, korzystamy z polecenia:
```ps1
 show interfaces terse
```