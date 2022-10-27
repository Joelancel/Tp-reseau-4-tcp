# TP4 : TCP, UDP et services réseau


Dans ce TP on va explorer un peu les protocoles TCP et UDP. 

**La première partie est détente**, vous explorez TCP et UDP un peu, en vous servant de votre PC.

La seconde partie se déroule en environnement virtuel, avec des VMs. Les VMs vont nous permettre en place des services réseau, qui reposent sur TCP et UDP.  
**Le but est donc de commencer à mettre les mains de plus en plus du côté administration, et pas simple client.**

Dans cette seconde partie, vous étudierez donc :

- le protocole SSH (contrôle de machine à distance)
- le protocole DNS (résolution de noms)
  - essentiel au fonctionnement des réseaux modernes

![TCP UDP](./pics/tcp_udp.jpg)

# Sommaire

- [TP4 : TCP, UDP et services réseau](#tp4--tcp-udp-et-services-réseau)
- [Sommaire](#sommaire)
- [0. Prérequis](#0-prérequis)
- [I. First steps](#i-first-steps)
- [II. Mise en place](#ii-mise-en-place)
  - [1. SSH](#1-ssh)
  - [2. Routage](#2-routage)
- [III. DNS](#iii-dns)
  - [1. Présentation](#1-présentation)
  - [2. Setup](#2-setup)
  - [3. Test](#3-test)

# 0. Prérequis

➜ Pour ce TP, on va se servir de VMs Rocky Linux. On va en faire plusieurs, n'hésitez pas à diminuer la RAM (512Mo ou 1Go devraient suffire). Vous pouvez redescendre la mémoire vidéo aussi.  

➜ Si vous voyez un 🦈 c'est qu'il y a un PCAP à produire et à mettre dans votre dépôt git de rendu

➜ **L'emoji 🖥️ indique une VM à créer**. Pour chaque VM, vous déroulerez la checklist suivante :

- [x] Créer la machine (avec une carte host-only)
- [ ] Définir une IP statique à la VM
- [ ] Donner un hostname à la machine
- [ ] Vérifier que l'accès SSH fonctionnel
- [ ] Vérifier que le firewall est actif
- [ ] Remplir votre fichier `hosts`, celui de votre PC, pour accéder au VM avec un nom
- [ ] Dès que le routeur est en place, n'oubliez pas d'ajouter une route par défaut aux autres VM pour qu'elles aient internet

> Toutes les commandes pour réaliser ces opérations sont dans [le mémo Rocky](../../cours/memo/rocky_network.md). Aucune de ces étapes ne doit figurer dan le rendu, c'est juste la mise en place de votre environnement de travail.

# I. First steps

Faites-vous un petit top 5 des applications que vous utilisez sur votre PC souvent, des applications qui utilisent le réseau : un site que vous visitez souvent, un jeu en ligne, Spotify, j'sais po moi, n'importe.

🌞 **Déterminez, pour ces 5 applications, si c'est du TCP ou de l'UDP**

Dofus (cap dofus.pcapng) : 
- ip dst :  172.65.252.253 
- port dst :  5.5.5.5
- port src : 60506 

netstat -ano -p tcp -b : 
```
[Dofus.exe]
  TCP    10.33.17.65:60459      172.65.252.253:5555    ESTABLISHED     11868
```
Spotify (cap spotify.pcapng) : 
- ip dst : 35.186.224.47
- port dst : 443
-  port src : 53176

```
[Spotify.exe]
  TCP    192.168.1.16:53112     35.186.224.47:443
```

Discord (cap discord.pcapng) :
- ip dst :  162.159.130.234
- port dst : 443
- port src : 49187



```
[Discord.exe]
 TCP    192.168.1.16:60261     162.159.130.234:443     ESTABLISHED     
```

Riot client (cap riot.pcapng)
- ip dst : 104.18.156.37
- port dst : 443
- port src : 4088

```
[RiotClientServices.exe]
  TCP    192.168.1.16:55535     104.18.156.37:443      ESTABLISHED     4088
```
🌞 **Demandez l'avis à votre OS**
- Netstat :
```
  Proto  Adresse locale         Adresse distante       État
  TCP    127.0.0.1:61810        LAPTOP-DS0S1GKI:9010   SYN_SENT
  TCP    192.168.1.16:52509     lan:domain             ESTABLISHED
  TCP    192.168.1.16:55064     lan:domain             ESTABLISHED
  TCP    192.168.1.16:55651     cdn-185-199-108-154:https  ESTABLISHED
  TCP    192.168.1.16:55658     cdn-185-199-108-154:https  ESTABLISHED
  TCP    192.168.1.16:55841     lb-140-82-121-6-fra:https  ESTABLISHED
```
# II. Mise en place

## 1. SSH

🖥️ **Machine `node1.tp4.b1`**

- n'oubliez pas de dérouler la checklist (voir [les prérequis du TP](#0-prérequis))
- donnez lui l'adresse IP `10.4.1.11/24`

Connectez-vous en SSH à votre VM.

🌞 **Examinez le trafic dans Wireshark**
-  *3-Way Handshake* ( cap3way.pcapng ) 
-  **repérez du trafic SSH** (ssh.pcapng)
- **repérez le FIN FINACK à la fin d'une connexion** (fin de trafic.pcapng)
🌞 **Demandez aux OS**

netstats : 
  ```
Proto  Adresse locale         Adresse distante       État
  TCP    10.4.1.254:61193       10.4.1.11:ssh          ESTABLISHED
```
