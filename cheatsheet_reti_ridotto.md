# Cheatsheet reti ridotto

## Host
### Configurazione indirizzo IP
* Statico: `ip addr add 192.168.0.1/24 dev eth0`
* Dinamico: `dhcp` (solo su VPCS)

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
* Server: `iperf3 -s`
* Client: `iperf3 -c <IP server>`

## Router
### Ping
```
R1#ping <ip>
```

### Route
#### Mostrare route
```
R1#show ip route
```

#### Aggiungere route statica
```
R1(config)#ip route 8.8.8.0 255.255.255.0 192.168.5.2
```
Dove l'ultimo IP è quello del next hop (router).

### Interfacce
#### Mostra informazioni su interfacce
```
R1#show int
```

#### Impostazione indirizzo interfaccia
* Statico: `R1(config-if)#ip address 192.168.1.1 255.255.255.0`
* Dinamico: `R1(config-if)#ip address dhcp`

### NAT
Configurazione interfaccia *privata*:
```
R1(config-if)#ip nat inside
```

Configurazione interfaccia *pubblica*:
```
R1(config-if)#ip address dhcp
R1(config-if)#ip nat outside
```
Fare attenzione all'indirizzo settato dal dhcp, chiamiamolo `x`.

Crea pool indirizzi `ovrld`:
```
R1(config)#ip nat pool ovrld x x prefix 24
```

Permetti indirizzi nattabili:
```
R1(config)#ip nat inside source list 7 pool ovrld overload
```

Crea access list indirizzi nattabili:
```
R1(config)#access-list 7 permit 10.10.10.0 0.0.0.255
```

### Velocità e duplex interfaccia
```
R1(config-if)#speed 100
R1(config-if)#duplex full
```
Utile per interfaccia one-arm di un router.

### Configurazione sottointerfaccia
Creo sottointerfaccia per VLAN 2:
```
R1(config)#int fa0/0
R1(config)#no shut
R1(config-if)#int fa0/0.2
R1(config-subif)#encapsulation dot1q 2
R1(config-subif)#no shut
```
Utile per interfaccia one-arm di un router.

### Server DHCP
#### Configurazione
```
R1(config)#ip dhcp excluded-address 192.168.0.1 192.168.0.100
R1(config)#ip dhcp pool vlan1pool
R1(dhcp-config)#network 192.168.0.0 255.255.255.0
R1(dhcp-config)#default-router 192.168.0.1
R1(dhcp-config)#dns-server 8.8.8.8
```

#### Mostrare indirizzi assegnati
```
R1#show ip dhcp binding
```

### HSRP
```
R1(config-if)#standby 1 ip 192.168.1.1
R1(config-if)#standby 1 priority 200
R1(config-if)#standby 1 preempt
R1(config-if)#standby 1 track fa0/1 150
```

## Switch
### Spanning tree
#### Visualizzazione stato spanning tree
```
SW1#show spanning-tree vlan 1 (brief)
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

#### Porte switch
* Access: `SW1(config-if)#switchport access vlan 2`
* Trunk: `SW1(config-if)#switchport mode trunk`

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

#### Cambio aging entry filter databse
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
