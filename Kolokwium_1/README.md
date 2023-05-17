# Początkowe przygotowanie
Logowanie na urządzenie.
```ps1
> telnet -> 172.30.33.[numer_routera] 
```

Załadowanie konfiguracji wykonujemy tylko raz na danym routerze. Aby następnie na tej konfiguracji mogła\
pracować cała grupa. 
```ps1
> configure private
> load override LAN-GR1-dd.mm.rrr (jeżeli to nowe urządzenie to ładujemy basic.cfg)
> commit
```
Osoby które zalogowały się na router przed załadowaniem konfiguracji, muszą wykonać polecenie:
```ps1
> update
```

Konfigurację powinna zapisać ostatnia osoba która planuje zakończyć działania na dzisiaj.\
Ale jeżeli została by kilka razy zapisana, to się nic nie stanie bo jest wtedy nadpisywana.\
Ważne aby zrobić "commit" przed zapisaniem konfiguracji.
```ps1
> commit
> update
> save LAN-GR1-dd.mm.rrr
```

# 1 punkt ćwiczenia -> poniżej----------------------------------------------

## Tworzymy wirtualny router i dodajemy do niego interfejs
```ps1
> set routing-instance ROUTERX instance-type virtual-router
> set routing-instance ROUTERX interface ge-0/0/1.X00 
```

## Konfigurujemy vlan i interfejs
```ps1
> set interfaces ge-0/0/1 vlan-tagging
> set interfaces ge-0/0/1 unit X00 vlan-id X00
> set interfaces ge-0/0/1 unit X00 family inet address 192.168.X.INTERFEJS (np .9/30)
```

# 2 punkt ćwiczenia -> poniżej----------------------------------------------

## Konfigurowanie adresu loopback na routerach korzystających z protokołu OSPF
Wszysto co jest w '' np. 'NUMEREK' wymienamy np. na 2. Czyli swój własny numerek \
który dostaliśmy od prowadzącego.
UWAGA -> commit robimy dopiero po wpisaniu tych dwóch komend
ZWRÓĆ UWAGĘ -> również na to że w 1 komendzie adres dla loopbacka może mieć inną postać
```ps1
> set interfaces lo0.'NUMEREK' family inet address 192.'NUMEREK'.0.'KONIEC_IP_ROUTERA'/32
> set routing-instances 'ROUTERX' interface lo0.'NUMEREK'
> commit
```

## Sprawdzanie poprawności konfiguracji adresu loopback 
```ps1
>  run show interfaces lo0.'NUMEREK'
```
Prawidłowo to skonfigurowaliśmy jeżeli w wyświetlonych informacjach dodatkowo jest:\
Local: 200.0.2.144\
200.0.2.144 to przykładowy adres, ale ogólnie powinien tam być adres dla loopbacka
który skonfigurowaliśmy na tym routerze

# 3 punkt ćwiczenia -> poniżej----------------------------------------------

## Konfigurowanie protokołu OSPF
```ps1
> set routing-instances 'ROUTERX' protocols ospf area 0 interface ge-0/0/'INTERFEJS'.'VLAN'
```
## Sprawdzanie poprawności skonfigurowania protokołu OSPF
```ps1
> run show ospf interface instance ROUTERX
```
Ważne aby sprawdzić czy odpowiednie interfejsy co zostały skonfigurowane, mają poprawną ilość\
sąsiadów w tabelce Nbrs.

## Konfigurowawnie OSPF na pasywny tam gdzie interfejs routera idzie do switcha -> tylko jeśli pisze O(p) a tak to jej nie dajemy
```ps1
> set routing-instances 'ROUTERX' protocols ospf area 0 interface ge-0/0/'INTERFEJS'.'VLAN' passive
```

# 4 punkt ćwiczenia -> poniżej----------------------------------------------

## Konfigurowanie protokołu RIP
```ps1
> set routing-instances ROUTERX protocols rip group GRUPA1 neighbor ge-0/0/1.X00
```

# 5 punkt ćwiczenia -> poniżej----------------------------------------------

## Rozgłaszanie w protokole RIP wszystkich informacji o trasach otrzymanych z protokołu RIP
```ps1
> set policy-options policy-statement POLITYKAX-RIP from protocol rip
> set policy-options policy-statement POLITYKAX-RIP then accept
> set routing-instances ROUTERX protocols rip group GRUPA1 export POLITYKAX-RIP
```
## Rozgłaszanie w protokole RIP wszystkie informacje trasy bezpośrednio podłączonej
```ps1
> set policy-options policy-statement POLITYKAX-DIRECT from protocol direct
> set policy-options policy-statement POLITYKAX-DIRECT then accept
> set routing-instances ROUTERX protocols rip group GRUPA1 export POLITYKAX-DIRECT
```

# 6 punkt ćwiczenia -> poniżej----------------------------------------------

## Redestrybucja tras z protokołu RIP do OSPF, i z OSPF do RIP
Redestrybucja tras z protokołu RIP do OSPF:
```ps1
> set routing-instances ROUTERX protocols ospf export POLITYKAX-DIRECT
```
Redestrybucja tras z protokołu OSPF do RIP:
```ps1
> set policy-options policy-statement FROM-OSPF-'NUMEREK' from protocol ospf
> set policy-options policy-statement FROM-OSPF-'NUMEREK' then accept
> set routing-instances 'ROUTERX' protocols rip group GRUPA1 export FROM-OSPF-'NUMEREK'
```

# 7 punkt ćwiczenia -> poniżej----------------------------------------------

## Polityka zapewniająca to że adresy interfejsów loopback nie będą rozgłaszane przez protokół RIP oraz OSPF
```ps1
> set policy-options prefix-list LISTAX-LOOPBACK [adres_loopback_tego_urządzenia \ maska]
> set policy-options policy-statement POLITYKAX-REJECT-LOOPBACK from prefix-list LISTAX-LOOPBACK
> set policy-options policy-statement POLITYKAX-REJECT-LOOPBACK then reject
```
## Importowanie ignorowania loopback do RIP'a
```ps1
> insert routing-instances ROUTERX protocols rip group GRUPA1 export POLITYKAX-REJECT-LOOPBACK before POLITYKAX-DIRECT
```

## Importowanie ignorowania loopback do OSPF'a na routerze na którym też jest RIP
```ps1
> insert routing-instances ROUTERX protocols ospf export POLITYKAX-REJECT-LOOPBACK before POLITYKAX-DIRECT
```

## Importowanie ignorowania loopback do OSPF'a na routerze bez RIP'a
```ps1
> set routing-instances ROUTERX protocols ospf export POLITYKAX-REJECT-LOOPBACK
```

## Sprawdzenie czy interfejs loopback przestał być rozgłaszany
Wpisujemy poniższą komende i patrzymy czy ten adres loopback (np. 200.0.X.nr_routera/32) który podaliśmy przy konfiguracji loopback
jest tam wypisany dla naszego ROUTERX, jeżeli go nie ma to poprawnie wykonaliśmy powyższe komendy
```ps1
> run show route
```

# 8 punkt ćwiczenia -> poniżej----------------------------------------------

## Konfigurowanie routingu statycznego
Pamiętać że w routingu statycznym korzystamy z adresu sieci, czyli np. na kartce z obliczeniami \
mamy VLAN o numerze 251 -> są tu 2 urządzenia -> a jego ADRES SIECI TO: 145.168.2.192/30 \
I ten adres sieci WRAZ Z MASKĄ zapisujemy w tej komendzie:

[Adres_sieci] -> przykładowy zapis w komendzie: 145.168.2.192/30 \
[IP_routera] -> przykładowy zapis w komendzie: 193.168.X.30      -> pamiętamy że zapisujemy bez maski

Po wpisaniu poniższej komendy trasa statyczna będzie odrazu aktywna więc nie trzeba nic więcej po niej robić.
```ps1
> set routing-instances ROUTERX routing-options static route [Adres_sieci]\maska next-hop [IP_routera_bez_maski]
```

A tu dodatokowa komenda która służy do aktywacji i deaktywacji ścieżek (mało prawdopodobne że na kolokwium
będzie o to prosił)
```ps1
> activate / deactivate routing-instances ROUTERX routing-options static
```

# 9 punkt ćwiczenia -> poniżej----------------------------------------------

## Tworzenie ścieżki domyślnej
```ps1
> set routing-options static route 0.0.0.0/0 next-hop [IP_routera_bez_maski]
```

# Inne ważne komendy
Jako iż nie mamy możliwości pingowania adresów w sieci jeśli mamy ich wiele, wtedy musimy ustalić
dokładnie z którego routera to ma iść za pomocą poniższej komendy.
```ps1
> run ping [adres_IP_urządzenia_docelowego] routing-instances ROUTERX
```
Natomiast ta komenda robi to samo co wyżej ale pokazuje nam nasz ścieżki
```ps1
> run traceroute [adres_IP_urządzenia_docelowego] routing instances ROUTERX
```