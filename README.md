## Ładowanie konfiguracji

Załadowanie konfiguracji wykonujemy tylko raz na danym routerze. Aby następnie na tej konfiguracji mogła\
pracować cała grupa. 
```ps1
> configure private
> load override LAN-gr1-dd.mm.rrr
> commit
```
Osoby które zalogowały się na router przed załadowaniem konfiguracji, muszą wykonać polecenie:
```ps1
> update
```

## Zapisywanie konfiguracji

Konfigurację powinna zapisać ostatnia osoba która planuje zakończyć działania na dzisiaj.\
Ale jeżeli została by kilka razy zapisana, to się nic nie stanie bo jest wtedy nadpisywana.\
Ważne aby zrobić "commit" przed zapisaniem konfiguracji.
```ps1
> commit
> update
> save LAN-gr1-dd.mm.rrr
```

## Wyświetlanie interfejsów
Wyświetlanie wszystkich szczegółów o interfejsach:
```ps1
> show interfaces
```
Natomiast gdy potrzebujemy wyswietlić krótkie podsumowanie konfiguracji interfejsów, korzystamy z polecenia:
```ps1
> show interfaces terse
```
Wyświetlenie bieżącego stanu interfejsów (pokazuje np. czy interfejs jest włączony lub też wyłączony): 
```ps1
> run show interfaces terse
```
Wyświetlenie konfiguracji interfejsów:
```ps1
> show configuration interfaces
```
Wyświetlenie konfiguracji wybranego interfejsu, np. ge-0/0/1:
```ps1
> show configuration interfaces ge-0/0/1 
```

## Wyświetlanie tablicy ARP
Tablica ARP zawiera informacje (adres IP i MAC) innych urządzeń w sieci, z którym nasze urządzenie\
ma nawiązane połączenie.
```ps1
> show arp
```
Wyświetlanie tablicy ARP dla konkretnego interfejsu, np. ge-0/0/1:
```ps1
> show arp intereface ge-0/0/1
```

## Wyświetlanie tablicy routingu
Wyświetlanie tablicy routingu, która zawiera informacje o skonfigurowanych trasach sieciowych:
```ps1
> show route
```
Wyświetlanie bieżącego stanu tablicy routingu, która zawiera informacje o aktualnych trasach sieciowych:
```ps1
> run show route
```

## Informacje dodatkowe
Oznaczenie ">" przy komendach służy tylko i wyłącznie do ich wyodrębnienia w tym dokumencie.\
Należy pamiętać że terminalu mogą występować różne znaki zachęty, w zależności od nabytych praw dostępu.