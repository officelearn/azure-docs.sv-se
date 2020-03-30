---
title: Azure VMware Solution by CloudSimple – Lokal anslutning med ExpressRoute
description: Beskriver hur du begär en lokal anslutning med ExpressRoute från CloudSimple-regionnätverket
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0dd5ede110255b6e53bbc397e683e66b3beffc65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019629"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>Ansluta från lokalt till CloudSimple med ExpressRoute

Om du redan har en Azure ExpressRoute-anslutning från en extern plats (till exempel lokal) till Azure kan du ansluta den till din CloudSimple-miljö. Du kan göra det via en Azure-funktion som gör att två ExpressRoute-kretsar kan ansluta till varandra. Den här metoden upprättar en säker, privat, hög bandbredd, låg latens anslutning mellan de två miljöerna.

[![Lokal ExpressRoute-anslutning - Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Innan du börjar

Ett **/29-nätverksadressblock** krävs för att upprätta Global Reach-anslutning från lokala.  Adressutrymmet /29 används för transitnätverk mellan ExpressRoute-kretsar.  Transitnätverket bör inte överlappa något av dina virtuella Azure-nätverk, lokala nätverk eller CloudSimple Private Cloud-nätverk.

## <a name="prerequisites"></a>Krav

* En Azure ExpressRoute-krets krävs innan du kan upprätta anslutningen mellan kretsen och CloudSimple Private Cloud-nätverken.
* En användare krävs med behörighet för att skapa auktoriseringsnycklar på en ExpressRoute-krets.

## <a name="scenarios"></a>Scenarier

Genom att ansluta ditt lokala nätverk till ditt privata molnnätverk kan du använda det privata molnet på olika sätt, inklusive följande scenarier:

* Få tillgång till ditt privata molnnätverk utan att skapa en VPN-anslutning från plats till plats.
* Använd din lokala Active Directory som identitetskälla i ditt privata moln.
* Migrera virtuella datorer som körs lokalt till ditt privata moln.
* Använd ditt privata moln som en del av en katastrofåterställningslösning.
* Förbruka lokala resurser på virtuella datorer med privat molnarbetsbelastning.

## <a name="connecting-expressroute-circuits"></a>Ansluta ExpressRoute-kretsar

Om du vill upprätta ExpressRoute-anslutningen måste du skapa en auktorisering på din ExpressRoute-krets och tillhandahålla auktoriseringsinformationen till CloudSimple.


### <a name="create-expressroute-authorization"></a>Skapa ExpressRoute-auktorisering

1. Logga in på Azure Portal.

2. Sök efter **ExpressRoute-krets** i det övre sökfältet och klicka på **ExpressRoute-kretsar** under **Tjänster**.
    [![ExpressRoute kretsar](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Välj den ExpressRoute-krets som du tänker ansluta till cloudsimple-nätverket.

4. Klicka på **Auktoriseringar**på sidan ExpressRoute, ange ett namn för auktoriseringen och klicka på **Spara**.
    [![Tillstånd för ExpressRoute-krets](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Kopiera resurs-ID och auktoriseringsnyckel genom att klicka på kopieringsikonen. Klistra in ID och knapp i en textfil.
    [![ExpressRoute-kopia av kretsauktorisering](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **Resurs-ID** måste kopieras från användargränssnittet och ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` ska vara i det format som du anger det för att stödja.

6. Lämna in en biljett <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">med support</a> för anslutningen som ska skapas.
    * Typ av problem: **Teknisk**
    * Prenumeration: **Prenumeration där CloudSimple-tjänsten distribueras**
    * Tjänst: **VMware-lösning från CloudSimple**
    * Problemtyp: **Servicebegäran**
    * Problemundertyp: **Skapa ExpressRoute-anslutning till lokala**
    * Ange den resurs-ID och auktoriseringsnyckel som du kopierade och sparade i informationsfönstret.
    * Ange ett /29-nätverksadressutrymme för transitnätverk.
    * Skickar du standardrutt via ExpressRoute?
    * Ska den privata molntrafiken använda standardrutten som skickas via ExpressRoute?

    > [!IMPORTANT]
    > Genom att skicka standardrutten kan du skicka all internettrafik från Private Cloud med din lokala internetanslutning.  Om du vill inaktivera standardvägen som konfigurerats i det privata molnet och använda standardflödet för lokal anslutning anger du informationen i supportbiljetten.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Azure-nätverksanslutningar](cloudsimple-azure-network-connection.md)  
