# Teoria reti
## Spanning tree
### Traffico su trunk
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

### Porte root, designated e blocked
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

## Switch multilayer
### Tipi di interfacce disponibili
1. **Interfacce L2**
    * Classica switch
    * Access/Trunk
1. **Interfacce L3**
    * Classica router (se modalità access)
    * In trunk mode riceve traffico taggato da
    più VLAN
        * Dovranno essere create più
        sottointerfacce L3
        * Alternativa 1 per one-arm
1. **Interfaccia virtuale VLAN L3**
    * Creata con `interface vlan ...`
    * Gli viene assegnato indirizzo IP
    * Alternativa 2 per one-arm

## One-arm router
1. Configurare arm come trunk
1. Due alternative:
    1. Creo sottointerfacce L3
    1. Creo interfacce virtuali VLAN L3
        * In questo caso il traffico viene
        inoltrato alla VLAN giusta in base al id
        del frame 802.1Q