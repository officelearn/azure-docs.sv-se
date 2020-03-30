---
title: Få tillgång till Azure VMware-lösning via CloudSimple från lokala
titleSuffix: Azure VMware Solution by CloudSimple
description: Komma åt din Azure VMware-lösning från CloudSimple från ditt lokala nätverk via en brandvägg
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: df4c51953c6f50e30ba61b993cdb35856fcb8e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77082965"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>Komma åt din CloudSimple Private Cloud-miljö och program från lokala

En anslutning kan konfigureras från lokalt nätverk till CloudSimple med Azure ExpressRoute eller Site-to-Site VPN.  Få tillgång till ditt CloudSimple Private Cloud vCenter och alla arbetsbelastningar som du kör på det privata molnet med anslutningen.  Du kan styra vilka portar som öppnas i anslutningen med hjälp av en brandvägg i det lokala nätverket.  I den här artikeln beskrivs några av de typiska kraven för programport.  För alla andra program, se ansökningsdokumentationen för portkrav.

## <a name="ports-required-for-accessing-vcenter"></a>Portar som krävs för åtkomst till vCenter

För att komma åt din Private Cloud vCenter och NSX-T-hanterare måste portar som definieras i tabellen nedan öppnas i den lokala brandväggen.  

| Port       | Källa                           | Mål                      | Syfte                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | Lokala DNS-servrar          | Privata DNS-servrar i molnet        | Krävs för att vidarebefordra DNS-sökning *av az.cloudsimple.io* till privata DNS-servrar i molnet från lokalt nätverk.       |
| 53 (UDP)   | Privata DNS-servrar i molnet        | Lokala DNS-servrar          | Krävs för att vidarebefordra DNS-sökning av lokala domännamn från Private Cloud vCenter till lokala DNS-servrar. |
| 80 (TCP)   | Lokalt nätverk              | Privata molnhanteringsnätverk | Krävs för att omdirigera vCenter-URL från *http* till *https*.                                                           |
| 443 (TCP)  | Lokalt nätverk              | Privata molnhanteringsnätverk | Krävs för att komma åt vCenter och NSX-T-hanteraren från lokalt nätverk.                                             |
| 8000 (TCP) | Lokalt nätverk              | Privata molnhanteringsnätverk | Krävs för vMotion av virtuella datorer från lokalt till privat moln.                                            |
| 8000 (TCP) | Privata molnhanteringsnätverk | Lokalt nätverk              | Krävs för vMotion av virtuella datorer från privat moln till lokalt.                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>Portar som krävs för att använda lokal active directory som identitetskälla

Om du vill konfigurera lokal active directory som en identitetskälla på Private Cloud vCenter måste portar som definierats i tabellen öppnas.  Se [Använda Azure AD som identitetsleverantör för vCenter på CloudSimple Private Cloud](https://docs.azure.cloudsimple.com/azure-ad/) för konfigurationssteg.

| Port         | Källa                           | Mål                                         | Syfte                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | Privata DNS-servrar i molnet        | Lokala DNS-servrar                             | Krävs för att vidarebefordra DNS-sökning av lokala active directory-domännamn från Private Cloud vCenter till lokala DNS-servrar.          |
| 389 (TCP/UDP) | Privata molnhanteringsnätverk | Lokala active directory-domänkontrollanter     | Krävs för LDAP-kommunikation från Private Cloud vCenter-server till active directory-domänkontrollanter för användarautentisering.                |
| 636 (TCP)     | Privata molnhanteringsnätverk | Lokala active directory-domänkontrollanter     | Krävs för säker LDAP-kommunikation (LDAPS) från Private Cloud vCenter-server till active directory-domänkontrollanter för användarautentisering. |
| 3268 (TCP)    | Privata molnhanteringsnätverk | Lokala globala katalogservrar i active directory | Krävs för LDAP-kommunikation i en distribution av flera domänkontrollanter.                                                                        |
| 3269 (TCP)    | Privata molnhanteringsnätverk | Lokala globala katalogservrar i active directory | Krävs för LDAPS-kommunikation i en distribution av flera domänkontrollanter.                                                                       |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>Vanliga portar som krävs för åtkomst till virtuella arbetsbelastningsdatorer

Åtkomst till virtuella arbetsbelastningsdatorer som körs på Private Cloud kräver att portar öppnas i din lokala brandvägg.  Tabellen nedan visar några av de gemensamma portar som krävs och deras syfte.  För alla programspecifika portkrav, se programdokumentationen.

| Port         | Källa                         | Mål                          | Syfte                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Lokalt nätverk            | Privata molnarbetsbelastningsnätverk       | Säker skalåtkomst till virtuella Linux-datorer som körs på Private Cloud.              |
| 3389 (TCP)    | Lokalt nätverk            | Privata molnarbetsbelastningsnätverk       | Fjärrskrivbord till virtuella windows-datorer som körs på Private Cloud.                 |
| 80 (TCP)      | Lokalt nätverk            | Privata molnarbetsbelastningsnätverk       | Få tillgång till alla webbservrar som distribueras på virtuella datorer som körs på Private Cloud.        |
| 443 (TCP)     | Lokalt nätverk            | Privata molnarbetsbelastningsnätverk       | Få tillgång till alla säkra webbservrar som distribueras på virtuella datorer som körs på Private Cloud. |
| 389 (TCP/UDP) | Privata molnarbetsbelastningsnätverk | Lokalt active directory-nätverk | Ansluta virtuella datorer för Windows-arbetsbelastning till lokal active directory-domän.       |
| 53 (UDP)      | Privata molnarbetsbelastningsnätverk | Lokalt nätverk                  | DNS-tjänståtkomst för virtuella arbetsbelastningsdatorer till lokala DNS-servrar.         |

## <a name="next-steps"></a>Nästa steg

* [Skapa och hantera VLAN och undernät](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
* [Ansluta till lokalt nätverk med Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Konfigurera plats-till-plats-VPN från lokalt](https://docs.azure.cloudsimple.com/vpn-gateway/)
