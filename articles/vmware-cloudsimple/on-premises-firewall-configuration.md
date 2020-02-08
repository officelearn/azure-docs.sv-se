---
title: Komma åt Azure VMware-lösningar (AVS) från lokala platser
description: Åtkomst till dina Azure VMware-lösningar (AVS) från ditt lokala nätverk via en brand vägg
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a4a9760b5c7a70c58a1afe1b14b781a35f2b9b18
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2020
ms.locfileid: "77082965"
---
# <a name="accessing-your-avs-private-cloud-environment-and-applications-from-on-premises"></a>Åtkomst till din moln miljö och dina program från molnet lokalt

En anslutning kan konfigureras från det lokala nätverket till AVS med Azure ExpressRoute eller VPN för plats till plats. Få åtkomst till din molnbaserade privata moln vCenter och alla arbets belastningar som du kör i det privata moln molnet med hjälp av anslutningen. Du kan styra vilka portar som ska öppnas på anslutningen med hjälp av en brand vägg i ditt lokala nätverk. I den här artikeln beskrivs några av de typiska programmens port krav. För andra program finns det information om Port krav i program dokumentationen.

## <a name="ports-required-for-accessing-vcenter"></a>Portar som krävs för åtkomst till vCenter

Portarna som definieras i tabellen nedan måste öppnas i den lokala brand väggen för att få åtkomst till ditt molns privata moln vCenter och NSX-T-chef. 

| Port       | Källa                           | Mål                      | Syfte                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | Lokala DNS-servrar          | DNS-servrar för molnets privata moln        | Krävs för att vidarebefordra DNS-sökning av *AZ.cloudsimple.io* till AVS-DNS-servrar från lokalt nätverk.     |
| 53 (UDP)   | DNS-servrar för molnets privata moln        | Lokala DNS-servrar          | Krävs för att vidarebefordra DNS-sökning efter lokala domän namn från molnets privata moln-vCenter till lokala DNS-servrar. |
| 80 (TCP)   | Lokalt nätverk              | Nätverk för hantering av privata moln i AVS | Krävs för omdirigering av vCenter-URL från *http* till *https*.                                                         |
| 443 (TCP)  | Lokalt nätverk              | Nätverk för hantering av privata moln i AVS | Krävs för åtkomst till vCenter och NSX-T Manager från det lokala nätverket.                                           |
| 8000 (TCP) | Lokalt nätverk              | Nätverk för hantering av privata moln i AVS | Krävs för vMotion av virtuella datorer från lokal plats till AVS-privat moln.                                          |
| 8000 (TCP) | Nätverk för hantering av privata moln i AVS | Lokalt nätverk              | Krävs för vMotion av virtuella datorer från molnets privata moln till lokalt.                                          |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>Portar som krävs för att använda lokala Active Directory som identitets källa

Om du vill konfigurera en lokal Active Directory som identitets källa i det privata molnet i AVS-molnet, måste portarna som definierats i tabellen öppnas. Mer information finns i [använda Azure AD som identitetsprovider för vCenter på det privata molnet i AVS-AVS-molnet](https://docs.azure.cloudsimple.com/azure-ad/) .

| Port         | Källa                           | Mål                                         | Syfte                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | DNS-servrar för molnets privata moln        | Lokala DNS-servrar                             | Krävs för att vidarebefordra DNS-sökning efter lokala Active Directory-domännamn från molnets privata moln-vCenter till lokala DNS-servrar.        |
| 389 (TCP/UDP) | Nätverk för hantering av privata moln i AVS | Lokala Active Directory-domänkontrollanter     | Krävs för LDAP-kommunikation från det privata molnet i Cloud vCenter-servern till Active Directory-domänkontrollanter för användarautentisering.              |
| 636 (TCP)     | Nätverk för hantering av privata moln i AVS | Lokala Active Directory-domänkontrollanter     | Krävs för säker LDAP-kommunikation (LDAPs) från molnets privata moln vCenter-Server till Active Directory-domänkontrollanter för användarautentisering. |
| 3268 (TCP)    | Nätverk för hantering av privata moln i AVS | Lokala Active Directory-globala katalog servrar | Krävs för LDAP-kommunikation i en distribution med flera domänkontrollanter.                                                                      |
| 3269 (TCP)    | Nätverk för hantering av privata moln i AVS | Lokala Active Directory-globala katalog servrar | Krävs för LDAP-kommunikation i en distribution av flera domänkontrollanter.                                                                     |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>Vanliga portar som krävs för att komma åt virtuella arbets belastnings datorer

Åtkomst till arbets belastnings datorer som körs på moln privata moln kräver att portar öppnas i den lokala brand väggen. I tabellen nedan visas några av de vanliga portarna som krävs och deras syfte. Information om alla programspecifika port krav finns i program dokumentationen.

| Port         | Källa                         | Mål                          | Syfte                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Lokalt nätverk            | Molnets privata moln arbets belastnings nätverk       | Säker shell-åtkomst till virtuella Linux-datorer som körs på ett privat moln moln.            |
| 3389 (TCP)    | Lokalt nätverk            | Molnets privata moln arbets belastnings nätverk       | Fjärr skrivbord till virtuella Windows-datorer som körs på ett privat AVS-moln.               |
| 80 (TCP)      | Lokalt nätverk            | Molnets privata moln arbets belastnings nätverk       | Åtkomst till alla webb servrar som distribueras på virtuella datorer som körs på ett privat AVS-moln.      |
| 443 (TCP)     | Lokalt nätverk            | Molnets privata moln arbets belastnings nätverk       | Åtkomst till alla säkra webb servrar som distribueras på virtuella datorer som körs på ett privat AVS-moln. |
| 389 (TCP/UDP) | Molnets privata moln arbets belastnings nätverk | Lokalt Active Directory-nätverk | Anslut virtuella datorer med Windows-arbetsbelastning till en lokal Active Directory-domän.     |
| 53 (UDP)      | Molnets privata moln arbets belastnings nätverk | Lokalt nätverk                  | Åtkomst till DNS-tjänsten för virtuella arbets belastnings datorer till lokala DNS-servrar.       |

## <a name="next-steps"></a>Nästa steg

* [Skapa och hantera VLAN och undernät](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
* [Ansluta till ett lokalt nätverk med hjälp av Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Konfigurera plats-till-plats-VPN från lokal plats](https://docs.azure.cloudsimple.com/vpn-gateway/)
