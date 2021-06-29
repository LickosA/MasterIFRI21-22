# Rapport TP - TCP/IP

Nom & Prénoms : **Malick K. ALASSANE** 
Filière :  **Master 1 -  Sécurité Informatique** 
**Institut de formation et de Recherche en Informatique**

# Présentation et Objectifs du TP

L'objectif principal de ce travail est de mettre en pratique les notions acquises à la fin du cours sur les **réseaux et infrastructures avancés**. Il s'agit de simuler une infrastructure réseau avec l'outil GNS3. 

*Mots Clés* : **GNS3**, **VirtualBo**x, **Cisco**, **Routage**, **IP**, **DNS**

# Installation et Configuration de GNS3

Le système de base que nous utilisons est fedora. Pour installer l'outil GNS3, voici la  commande que nous avions exécuté :

```bash
sudo dnf install gns3-server gns3-gui
```

Après une installation réussie de GNS3, nous avions intégrer une machine linux qui nous servira pour la suite du TP.

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_20-13-50.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_20-13-50.png)

Nous avions terminer cette partie d'installation, en intégrant des switches cisco (C3745 et C2691) sous GNS3.

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_20-17-29.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_20-17-29.png)

# Réalisation du schéma du TP sous GNS3

Ce qu'il faut savoir : 

- Les switches core1 et core2 sont des switches de C3745
- Les switches Access1 et Access2 sont des switches de C2691
- La machine est la machine linux rt64 intégrée à GNS3 depuis virtualbox
- Les PC sont des VPCS de GNS3.

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_15-34-38.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_15-34-38.png)

# Configuration vlan sur CISCO

## Configuration des switches

A cette étape, nous avions effectué les différentes configurations sur les switches comme décrits ci dessous. 

Sur les switches cores, nous avions :

- défini les vlans
- Activé le mode TRUNK sur chaque switch entre toutes les liaisons entre switch
- Configuré les interfaces vlan pour les  switches cores
- Configuré chaque pool dhcp sur les switches  cores
- Configuré le HSRP sur les switches cores
- Configuré le Spanning Tree sur le switches cores
- Configuré des liens backbones (double lien entre les switches cores.)
- Activé le service DHCP sur les switches cores
- Configuré le routage inter vlan

### Core1

```bash
vlan database
vlan 10 name filaire
vlan 20 name wifi
vlan 30 name voip
vlan 50 name mgmt
exit
configure terminal
interface fastEthernet 1/0
switchport mode trunk
switchport trunk native vlan 50
no shutdown
interface fastEthernet 1/1
switchport mode trunk
switchport trunk native vlan 50
no shutdown
interface fastEthernet 1/2
switchport mode trunk
switchport trunk native vlan 50
no shutdown
interface fastEthernet 1/14
switchport mode trunk
switchport trunk native vlan 50
no shutdown
interface fastEthernet 1/15
switchport mode trunk
switchport trunk native vlan 50
no shutdown
interface vlan10
ip address 10.0.1.252 255.255.255.0
no shutdown
interface vlan20
ip address 10.0.2.252 255.255.255.0
no shutdown
interface vlan30
ip address 10.0.3.252 255.255.255.0
no shutdown
interface vlan50
ip address 192.168.1.252 255.255.255.0
no shutdown
end
write
Configure terminal
ip dhcp pool filaire
network 10.0.1.0 255.255.255.0
default-router 10.0.1.254
dns-server 192.168.1.1
lease 0 6
ip dhcp pool wifi
network 10.0.2.0 255.255.255.0
default-router 10.0.2.254
dns-server 192.168.1.1
lease 0 6
ip dhcp pool voip
network 10.0.3.0 255.255.255.0
default-router 10.0.3.254
dns-server 192.168.1.1
lease 0 6
end
write
configure terminal
interface vlan 10
standby 10 ip 10.0.1.254
standby 10 priority 150
standby 10 preempt
standby 10 name filaire
interface vlan 20
standby 20 ip 10.0.2.254
standby 20 priority 150
standby 20 preempt
standby 20 name wifi
interface vlan 30
standby 30 ip 10.0.3.254
standby 30 priority 150
standby 30 preempt
standby 30 name voip
interface vlan 50
standby 50 ip 192.168.1.254
standby 50 priority 150
standby 50 preempt
standby 50 name mgmt
spanning-tree uplinkfast
spanning-tree backbonefast
spanning-tree vlan 10 priority 4096
spanning-tree vlan 20 priority 4096
spanning-tree vlan 30 priority 4096
spanning-tree vlan 50 priority 4096
end
write
configure terminal
interface f1/14
switchport mode trunk
switchport trunk native vlan 50
interface f1/15
switchport mode trunk
switchport trunk native vlan 50
interface port-channel 1
switchport mode trunk
switchport trunk native vlan 50
interface f1/14
channel-group 1 mode on
interface f1/15
channel-group 1 mode on
end
write
configure terminal
service dhcp
end
write
configure terminal 
ip routing 
end
write
```

### Core2

```bash
vlan database
vlan 10 name filaire
vlan 20 name wifi
vlan 30 name voip
vlan 50 name mgmt
exit
configure terminal
interface fastEthernet 1/0
switchport mode trunk
switchport trunk native vlan 50
no shutdown
interface fastEthernet 1/1
switchport mode trunk
switchport trunk native vlan 50
no shutdown
interface fastEthernet 1/2
switchport mode trunk
switchport trunk native vlan 50
no shutdown
interface fastEthernet 1/14
switchport mode trunk
switchport trunk native vlan 50
no shutdown
interface fastEthernet 1/15
switchport mode trunk
switchport trunk native vlan 50
no shutdown
interface vlan10
ip address 10.0.1.253 255.255.255.0
no shutdown
interface vlan20
ip address 10.0.2.253 255.255.255.0
no shutdown
interface vlan30
ip address 10.0.3.253 255.255.255.0
no shutdown
interface vlan50
ip address 192.168.1.253 255.255.255.0
no shutdown
end
write
Configure terminal
ip dhcp pool filaire
network 10.0.1.0 255.255.255.0
default-router 10.0.1.254
dns-server 192.168.1.1
lease 0 6
ip dhcp pool wifi
network 10.0.2.0 255.255.255.0
default-router 10.0.2.254
dns-server 192.168.1.1
lease 0 6
ip dhcp pool voip
network 10.0.3.0 255.255.255.0
default-router 10.0.3.254
dns-server 192.168.1.1
lease 0 6
end
write
configure terminal
interface vlan 10
standby 10 ip 10.0.1.254
standby 10 priority 50
standby 10 preempt
standby 10 name filaire
interface vlan 20
standby 20 ip 10.0.2.254
standby 20 priority 50
standby 20 preempt
standby 20 name wifi
interface vlan 30
standby 30 ip 10.0.3.254
standby 30 priority 50
standby 30 preempt
standby 30 name voip
interface vlan 50
standby 50 ip 192.168.1.254
standby 50 priority 50
standby 50 preempt
standby 50 name mgmt
spanning-tree uplinkfast
spanning-tree backbonefast
spanning-tree vlan 10 priority 32768
spanning-tree vlan 20 priority 32768
spanning-tree vlan 30 priority 32768
spanning-tree vlan 50 priority 32768
interface f1/14
switchport mode trunk
switchport trunk native vlan 50
interface f1/15
switchport mode trunk
switchport trunk native vlan 50
interface port-channel 1
switchport mode trunk
switchport trunk native vlan 50
interface f1/14
channel-group 1 mode on
interface f1/15
channel-group 1 mode on
end
write
configure terminal
service dhcp
end
write
```

Sur les switches cores, nous avions :

- défini les vlans
- Configuré chaque interface en mode access pour laisser passer le bon vlan sur les switches
access

### Access1

```bash
vlan database
vlan 10 name filaire
vlan 20 name wifi
vlan 30 name voip
vlan 50 name mgmt
exit
configure terminal
interface fastEthernet 1/1
switchport mode trunk
switchport trunk native vlan 50
no shutdown
interface fastEthernet 1/2
switchport mode trunk
switchport trunk native vlan 50
no shutdown
interface fastEthernet 1/11
switchport mode trunk
switchport trunk native vlan 50
no shutdown
interface fastEthernet 1/12
switchport mode trunk
switchport trunk native vlan 50
no shutdown
interface fastEthernet 1/13
switchport mode trunk
switchport trunk native vlan 50
no shutdown
interface fastEthernet 1/11
switchport mode access
switchport access vlan 10
no shutdown
interface fastEthernet 1/12
switchport mode access
switchport access vlan 20
no shutdown
interface fastEthernet 1/13
switchport mode access
switchport access vlan 30
no shutdown
end
write
```

### Access2

```bash
vlan database
vlan 10 name filaire
vlan 20 name wifi
vlan 30 name voip
vlan 50 name mgmt
exit
configure terminal
interface fastEthernet 1/1
switchport mode trunk
switchport trunk native vlan 50
no shutdown
interface fastEthernet 1/2
switchport mode trunk
switchport trunk native vlan 50
no shutdown
interface fastEthernet 1/11
switchport mode trunk
switchport trunk native vlan 50
no shutdown
interface fastEthernet 1/12
switchport mode trunk
switchport trunk native vlan 50
no shutdown
interface fastEthernet 1/13
switchport mode trunk
switchport trunk native vlan 50
no shutdown
interface fastEthernet 1/11
switchport mode access
switchport access vlan 10
no shutdown
interface fastEthernet 1/12
switchport mode access
switchport access vlan 20
no shutdown
interface fastEthernet 1/13
switchport mode access
switchport access vlan 30
no shutdown
end
write
```

## Vérification des configurations

A la fin des différentes configurations effectuées sur les différents switches, voici les résultats de nos différents tests pour nous assurer que toutes les configurations sont bonnes.

### Affichage des vlans et des interfaces

Pour afficher les vlans et les interfaces, nous avions exécuté la commande suivantes sur les différents switches 

```bash
show vlan-switch
```

- **Core1**

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-17-39.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-17-39.png)

- **Core2**

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-19-39.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-19-39.png)

- **Access1**

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-20-33.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-20-33.png)

- **Access2**

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-20-57.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-20-57.png)

### Affichage de l’état de chaque interface

Pour afficher l'état de chaque interface, voici la commande que nous avions utilisé : 

```bash
sho ip interface brief
```

- **Core1**

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-22-23.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-22-23.png)

- **Core2**

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-22-51.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-22-51.png)

### Test du DHCP

Pour tester le service dhcp de chaque pc, nous avions configuré chaque vpc pour prendre une adresse ip par dhcp comme suit :

```bash
ip dhcp
save
```

- fil1

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-25-16.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-25-16.png)

- wlan1

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-26-45.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-26-45.png)

- voip1

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-27-23.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-27-23.png)

- fil2

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-29-37.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-29-37.png)

- wlan2

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-30-44.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-30-44.png)

- voip2

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-30-58.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_16-30-58.png)

### Que faut -il faire pour qu’un pc dans le vlan 10 puisse communiquer avec un pc dans les autres vlans ?

Pour qu'un PC dans le vlan 10 puisse communiquer avec un PC dans les autres vlans, il faut activer le **routage inter vlan** sur les deux switches cores avec la commande suivante : 

```bash
configure terminal
ip routing
```

### Test de connectivité

- Dans le même vlan

**ping de fil1 à fil2**

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_17-07-43.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_17-07-43.png)

**ping de wlan2 à wlan1**

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_17-08-42.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_17-08-42.png)

- Entre vlan

**ping de fil1 à wlan1**

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_17-09-39.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_17-09-39.png)

**ping de voip2 à fil1**

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_17-10-30.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_17-10-30.png)

### Test de disponiblilité

- **Core 1 est éteint**

    ![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_17-14-29.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_17-14-29.png)

    **ping de fil1 à voip1**

    ![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_17-15-35.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_17-15-35.png)

# Configuration d’un serveur DNS

Nous avions continué avec le TP en configurant un serveur DNS sur la machine linux du schéma précédent (rt64). 

## Configuration des interfaces réseau de la machine linux

Nous avions configurer les deux interfaces connectées aux core1 et core2 en pont pour bénéficier de la redondance en éditant le fichier **`/etc/network/interfaces`** comme suit :

```bash
# The loopback network interface
auto lo
iface lo inet loopback
# The primary network interface
allow-hotplug enp0s3
iface enp0s3 inet dhcp
allow-hotplug enp0s8
iface enp0s8 inet manual
allow-hotplug enp0s9
iface enp0s9 inet manual
auto br0
iface br0 inet static
	address 192.168.1.1/24
	bridge_ports enp0s9
	bridge_ports enp0s8
	bridge_stp off
	bridge_fd 0
	bridge_maxwait 0
```

## Routes statiques sur la machine linux

La machine linux se trouve dans le vlan management avec l’adresse ip 192.168.1.1. Elle est
utilisée comme serveur DNS et n’est pas joignable par les pcs des autres vlans. Voici les routes statiques à ajouter sur ce serveur pour lui permettre la communication entre ce dernier et les autres vlans

```bash
ip route add 10.0.1.0/24 via 192.168.1.254
ip route add 10.0.2.0/24 via 192.168.1.254
ip route add 10.0.3.0/24 via 192.168.1.254
```

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_17-57-51.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_17-57-51.png)

Test de ping depuis le serveur DNS vers les vpcs

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_18-02-04.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_18-02-04.png)

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_18-02-26.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_18-02-26.png)

## Configuration des zones DNS

Le domaine internet que nous avions configuré est **ifri.ral**. Pour y arriver, nous avions ouvert le fichier de configuration de bind `/etc/bind/named.conf.local` ; puis ajouté la gestion des 2 zones suivantes :

- ifri.ral
- 1.168.192.in-addr.arpa

```bash
zone "ifri.ral" IN {
 type master;
 file "/etc/bind/db.ifri";
 };
zone "1.168.192.in-addr.arpa" IN {
 type master;
 file "/etc/bind/db.192.rev ";
 };
```

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_18-44-12.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_18-44-12.png)

Ensuite, nous avions modifié le fichier `/etc/bind/db.ifri` comme suit :

```bash
;
; BIND data file for local loopback interface
;
$TTL 604800
@ IN SOA rt.ifri.ral admin.ifri.ral (
				20210629 ; Serial
				604800 ; Refresh
				86400 ; Retry
				2419200 ; Expire
				604800 ) ; Negative Cache TTL
;
@ IN NS rt.ifri.ral.
rt IN A 192.168.1.1
core1 IN A 192.168.1.252
core1 IN A 192.168.1.253
web IN CNAME rt.ifri.ral.
www IN CNAME rt.ifri.ral.
ifri.ral. IN CNAME rt.ifri.ral.
```

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_18-46-32.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_18-46-32.png)

Nous avions aussi modifié le fichier `/etc/bind/db.192.rev` comme suit :

```bash
;
; BIND reverse data file for local loopback interface
;
$TTL 604800
@ IN SOA ns.ifri.ral admin.ifri.ral (
 20210629 ; Serial
 604800 ; Refresh
 86400 ; Retry
 2419200 ; Expire
 604800 ) ; Negative Cache TTL
;
@ IN NS rt.ifri.ral.
1 IN PTR rt.ifri.ral.
```

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_18-49-37.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_18-49-37.png)

## Tests

A la fin des configurations du serveur DNS, voici les tests pour nous assurer que tout marche bien.

La commande `/etc/init.d/bind9 restart` nous a permi  de rédémarrer le service bind9 et `/etc/init.d/bind9 status` pour afficher le status.

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_18-51-04.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_18-51-04.png)

La commande `named-checkzone ifri.ral /etc/bind/db.ifri.ral` nous a permi de vérifier que la configuration de notre  zone **ifri.ral** est correcte.

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_18-52-10.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_18-52-10.png)

Nous avions procédé à la même vérification pour la zone inverse avec la commande que voici : `named-checkzone 1.168.192.in-addr.arpa /etc/bind/db.192.rev`.

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_18-53-13.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_18-53-13.png)

L'utilitaire dig a été utilisé pour quelques vérifications comme ci dessous :

```bash
dig -x 192.168.1.1
```

![Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_18-54-38.png](Rapport%20TP%20-%20TCP%20IP%205da443fc9b53407c90736a44d5966e92/Capture_dcran_du_2021-06-29_18-54-38.png)

# **Fin du TP**

Réalisé par :

**Malick ALASSANE,**

**alkomalick@gmail.com**