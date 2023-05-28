# Cheatsheet reti

## Host
### Configurazione indirizzo ip
```bash
ip addr add 192.168.0.1/24 dev eth0
```

### Configurazione route
```bash
ip route add 0.0.0.0/0 via <default gateway IP>
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

#### Impostazione indirizzo interfaccia
```
R1(config-if)#ip address 192.168.1.1 255.255.255.0
```

#### Velocità e duplex interfaccia
```
R1(config-if)#speed 100
R1(config-if)#duplex full
```
Utile per interfaccia one-arm di un router.

#### Configurazione sottointerfaccia
Creo sottointerfaccia per VLAN 2:
```
R1(config)#int fa0/0
R1(config)#no shut
R1(config-if)#int fa0/0.2
R1(config-subif)#encapsulation dot1q 2
R1(config-subif)#no shut
```
Utile per interfaccia one-arm di un router.

#### Rimozione sottointerfaccia
```
R1(config-if)#no int fa0/0.2
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

#### Porta access
```
SW1(config-if)#switchport access vlan 2
```

#### Porta trunk
```
SW1(config-if)#switchport mode trunk
```

#### Configurazione one-arm con switch multilayer
1. Creare nel database le VLAN
1. Configurare l'interfaccia one-arm come trunk
1. Creare delle *interfacce LVAN* e assegnare gli IP dei DG:
    ```
    SW1(config)#int vlan 2
    SW1(config-if)#ip address 192.168.1.254 255.255.255.0
    SW1(config-if)#no shut
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
