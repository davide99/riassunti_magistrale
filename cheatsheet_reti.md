# Cheatsheet reti

## Host
### Configurazione indirizzo ip
```bash
ip addr add 192.168.0.1/24 dev eth0
```

### ARP
#### Mostrare cache ARP
```bash
arp -en
```

#### Impostare entry statica nell'ARP cache
```bash
arp -i eth0 -s 192.168.0.1 08:00:11:22:33:44
```

### iperf
Server:
```bash
iperf3 -s
```
Client:
```bash
iperf3 -c <IP server>
```

## IOS
### Modalit&agrave; accesso/configurazione
1. *User EXEC*: `R1>`
1. *Privileged EXEC*: `R1#`
    * Ci si entra con: `ena`
1. *Global configuration*: `R1(config)#`
1. *Interface configuration*: `R1(config-if)#`
    * Ci si entra con: `int fa0/1`

### Interfacce
#### Disabilitazione interfaccia
```
R1(config-if)#shut
```

#### Mostra informazioni su interfacce
```
R1#show int
```

## Configurazione switch
### Spanning tree
Lo spanning tree protocol è abilitato di default

#### Visualizzazione stato spanning tree
```
SW1#show spanning-tree vlan 1
```

#### Disabilitazione spanning tree
```
SW1(config)#no spanning-tree vlan 1
```

#### Cambio priorità switch
Gli incrementi/decrementi vanno fatti modulo 4096
```
SW1(config)#spanning-tree vlan 1 priority 4096
```

#### Abilitare PortFast
Di solito le porte a cui si connette un host non vengono
abilitate immediatamente da uno switch, per evitare che si
creino loop. Si può disabilitare questo comportamento con:
```
SW1(config-if)#spanning-tree portfast
```

### VLAN
#### Creare nuova vlan
```
SW1#vlan database
SW1(vlan)#vlan 2
```

#### Abilitare spanning tree su vlan
```
SW1(config)#spanning-tree vlan 2
```

#### Porta trunk
```
SW1(config-if)#switchport mode trunk
```


### Filtering database
#### Mostra filtering database
```
SW1#show mac-address-table
```

### Cambio aging entry filter databse
```
SW1(config)#mac-address-table aging-time 30
```

#### Impostare entry statica in filter database
```
SW1(config)#mac-address-table static 0800.1122.3344 int fa1/1 vlan 1
```

### Link aggregation
Voglio aggregare le interfacce 1/3 e 1/4:
```
SW1(config)#int range fa 1/3 - 4
SW1(config-if-range)# channel-group 1 mode on
```
Stessa cosa su SW2.

## Teoria
### Spanning tree
#### Traffico su trunk
Nel caso di un link trunk, di una rete con due VLAN:
* VLAN1
* VLAN2

Se la:
1. VLAN1 è la VLAN di default, ovvero quella delle porte access non configurate
1. VLAN2 è la VLAN native, ovvero quella VLAN t.c. se arriva un frame non taggato allora è riferito alla VLAN2

Allora sul link trunk passeranno 3 BPDU:
1. STP, untagged, si riferisce alla VLAN1 (di default)
    * Per una questione di retrocompatibilità
1. PVST-VLAN1, tagged perchè non è la VLAN nativa
1. PVST-VLAN2, untugged perchè VLAN nativa

#### Porte root, designated e blocked
Algoritmo:
1. Trovo **root bridge** &#x1f451;
1. Trovo **root port** &cir;
    * per ogni bridge non root
    * porta migliore per raggiunegre root
1. Trovo **designated port** &#x25cf;
    * per ogni link
    * porta migliore per raggiungere root
1. Trovo **blocked ports** =
    * porte che non sono nè *root* nè *designated*