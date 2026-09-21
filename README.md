# Projet Sysadmin — Infrastructure NovaTech

Projet de mise en place d’une infrastructure système virtualisée pour **NovaTech**, basée sur VirtualBox, OPNsense, RHEL, Ubuntu Server, Windows 10 et FreeIPA.

Le projet couvre la segmentation réseau, la sécurisation des flux, l’hébergement d’un serveur web en DMZ et la centralisation des identités avec DNS et Kerberos.

## Objectifs

- Déployer un pare-feu OPNsense avec trois zones : WAN, LAN et DMZ.
- Mettre en place des serveurs Linux et un poste client Windows.
- Héberger Apache dans une DMZ isolée.
- Centraliser les utilisateurs, groupes, DNS et l’authentification avec FreeIPA.
- Vérifier la connectivité, la résolution DNS et l’authentification Kerberos.

## Architecture réseau

```text
                         Internet
                            │
                     WAN — OPNsense
                     10.0.2.15 (DHCP)
                       ┌────┴────┐
          LAN 192.168.56.0/24   DMZ 192.168.57.0/24
          GW 192.168.56.254     GW 192.168.57.254
              │                         │
        srv-ad01 .56.10            srv-web01 .57.10
        FreeIPA / DNS               Apache / Web
        srv-backup01 .56.12
        poste-win01 .56.102
```

| Machine | Adresse | Zone | Rôle |
|---|---:|---|---|
| OPNsense | `192.168.56.254` / `192.168.57.254` | LAN / DMZ | Pare-feu et passerelles |
| `srv-ad01` | `192.168.56.10` | LAN | FreeIPA, DNS, Kerberos, Samba prévu |
| `srv-backup01` | `192.168.56.12` | LAN | Sauvegardes |
| `srv-web01` | `192.168.57.10` | DMZ | Serveur Apache |
| `poste-win01` | `192.168.56.102` | LAN | Client Windows 10 |

## Prérequis

- VirtualBox avec les réseaux Host-Only `vboxnet0` et `vboxnet1`.
- ISO OPNsense 26.1, RHEL 9.8, Ubuntu Server 24.04 et Windows 10.
- Ressources suffisantes pour exécuter plusieurs machines virtuelles simultanément.
- Accès administrateur sur l’hôte pour créer les réseaux virtuels.

Les réseaux VirtualBox attendus sont :

- `vboxnet0` : `192.168.56.1/24`, DHCP désactivé — LAN.
- `vboxnet1` : `192.168.57.1/24`, DHCP désactivé — DMZ.

## Déploiement recommandé

1. Créer les réseaux Host-Only et les machines virtuelles.
2. Installer et configurer OPNsense comme pare-feu et passerelle.
3. Configurer les adresses IP statiques des serveurs et du poste client.
4. Installer Apache sur `srv-web01` dans la DMZ.
5. Installer FreeIPA sur `srv-ad01`.
6. Créer les zones DNS, groupes et utilisateurs.
7. Rattacher les clients au domaine `novatech.local`.
8. Tester la connectivité, DNS, Kerberos et l’accès web.

## Documentation

- [Jour 1 — Infrastructure de base et pare-feu OPNsense](Jour1_Infrastructure_Base.md)
- [Jour 2 — Annuaire centralisé FreeIPA](Jour2_FreeIPA.md)
- [Document de référence du projet](Projet_Sysadmin_RedHat.pdf)
- [Captures d’écran](docs/)

## Services et accès

- Interface OPNsense : `https://192.168.56.254`
- Interface FreeIPA : `https://srv-ad01.novatech.local` ou `https://192.168.56.10`
- Serveur web : `http://192.168.57.10`
- Domaine : `novatech.local`
- Realm Kerberos : `NOVATECH.LOCAL`

## Vérifications rapides

Depuis un serveur ou le poste d’administration :

```bash
ping 192.168.56.254
ping 192.168.56.10
ping 192.168.57.10
nslookup srv-ad01.novatech.local
nslookup srv-web01.novatech.local
```

Sur `srv-ad01` :

```bash
kinit admin
klist
ipa user-find
ipa group-find
```

## Évolutions prévues

- Configurer les partages Samba et NFS.
- Mettre en place les permissions ACL.
- Finaliser l’intégration des clients au domaine.
- Ajouter la stratégie de sauvegarde et la supervision.

## Avertissement

Cette infrastructure est conçue pour un environnement de laboratoire. Les mots de passe, règles de pare-feu et paramètres réseau doivent être adaptés avant toute utilisation en production.

## Auteur

[Manoach HOSSOU DODO](https://github.com/hdmanoach)