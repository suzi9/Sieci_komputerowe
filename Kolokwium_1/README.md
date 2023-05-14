# 1 punkt ćwiczenia -> poniżej-------------------------------------------------------------------

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

# 2 punkt ćwiczenia -> poniżej-------------------------------------------------------------------

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
Sprawdzamy to na routerach, na których została ta konfiguracja zrobiona.
```ps1
>   run show interfaces lo0
```
Prawidłowo to skonfigurowaliśmy jeżeli w wyświetlonych informacjach jest:
Physical interface: lo0, Enabled, Physical link is Up

A potem robimy:
```ps1
>   run show interfaces lo0.'NUMEREK'
```
Prawidłowo to skonfigurowaliśmy jeżeli w wyświetlonych informacjach dodatkowo jest:
Local: 200.0.2.144
200.0.2.144 to przykładowy adres, ale ogólnie powinien tam być adres dla loopbacka\
który skonfigurowaliśmy na tym routerze

# 3 punkt ćwiczenia -> poniżej-------------------------------------------------------------------

## Konfigurowanie protokołu OSPF
```ps1
> set routing-instances 'ROUTERX' protocols ospf area 0 interface ge-0/0/'INTERFEJS'.'VLAN'
```

## Konfigurować OSPF pasywny tam gdzie interfejs routera idzie do switcha???????????????????
## czy to coś zmienia?????????????????


# 4 punkt ćwiczenia -> poniżej-------------------------------------------------------------------

## Konfigurowanie protokołu RIP
```ps1
> set routing-instances ROUTERX protocols rip group GRUPA1 neighbor ge-0/0/1.X00
```

# 5 punkt ćwiczenia -> poniżej-------------------------------------------------------------------

## Rozgłaszanie w protokole RIP wszystkich informacji o trasach otrzymanych z protokołu RIP
```ps1
> set policy-options policy-statement FROM-RIP-X from protocol rip
> set policy-options policy-statement FROM-RIP-X then accept
> set routing-instances ROUTERX protocols rip group GRUPA1 export FROM-RIP-X
```
## Rozgłaszanie w protokole RIP wszystkie informacje trasy bezpośrednio podłączonej
```ps1
> set policy-options policy-statement FROM-DIRECT-X from protocol direct
> set policy-options policy-statement FROM-DIRECT-X then accept
> set routing-instances ROUTERX protocols rip group GRUPA1 export FROM-DIRECT-X
```


# 7 punkt ćwiczenia -> poniżej-------------------------------------------------------------------

## Polityka zapewniająca to że adresy interfejsów loopback nie będą rozgłaszane przez protokół RIP oraz OSPF
```ps1
> set policy-options policy-statement REJECT-LOOPBACK-'NUMEREK' from interface lo0.'NUMEREK'
> set policy-options policy-statement REJECT-LOOPBACK-'NUMEREK' then reject
> set protocols rip export REJECT-LOOPBACK-'NUMEREK'  ## sprawdzić tą komendę przed jej wykonaniem a po, co się zmieni i czy na pewno ją trzeba
> set protocols ospf export REJECT-LOOPBACK-'NUMEREK'  ## i tą również 
```

# 8 punkt ćwiczenia -> poniżej-------------------------------------------------------------------

## Konfigurowanie routingu statycznego
Pamiętać że w routingu statycznym korzystamy z adresu sieci, czyli np. na kartce z obliczeniami \
mamy VLAN o numerze 251 -> są tu 2 urządzenia -> a jego ADRES SIECI TO: 145.168.2.192/30 \
I ten adres sieci WRAZ Z MASKĄ zapisujemy w tej komendzie:

[Adres_sieci] -> przykładowy zapis w komendzie: 145.168.2.192/30 \
[IP_routera] -> przykładowy zapis w komendzie: 193.168.X.30      -> pamiętamy że zapisujemy bez maski

```ps1
> set routing-instances ROUTERX routing-options static route [Adres_sieci] next-hop [IP_routera]
```