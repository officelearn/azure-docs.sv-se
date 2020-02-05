---
title: Azure VMware-lösningar (AVS)-lokal anslutning med ExpressRoute
description: Beskriver hur du begär en lokal anslutning med ExpressRoute från AVS-regions nätverket
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 10a21faf2790b4c7a26d80e46bf44c8bffabf27f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019629"
---
# <a name="connect-from-on-premises-to-avs-using-expressroute"></a>Ansluta från lokalt till AVS med ExpressRoute

Om du redan har en Azure ExpressRoute-anslutning från en extern plats (till exempel lokalt) till Azure kan du ansluta den till din AVS-miljö. Du kan göra det via en Azure-funktion som gör att två ExpressRoute-kretsar kan ansluta till varandra. Den här metoden upprättar en säker, privat, hög bandbredds anslutning med låg latens mellan de två miljöerna.

[![lokal ExpressRoute-anslutning – Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Innan du börjar

Ett **/29** nätverks adress block krävs för att upprätta Global Reach anslutning lokalt. Adress utrymmet/29 används för överförings nätverk mellan ExpressRoute-kretsar. Överförings nätverket bör inte överlappa något av dina virtuella Azure-nätverk, lokala nätverk eller moln nätverk i molnet.

## <a name="prerequisites"></a>Krav

* En Azure ExpressRoute-krets krävs innan du kan upprätta anslutningen mellan kretsen och nätverk för molnets privata moln.
* En användare måste ha behörighet att skapa auktoriseringsarkiv på en ExpressRoute-krets.

## <a name="scenarios"></a>Scenarier

Genom att ansluta ditt lokala nätverk till ditt moln nätverk i molnet kan du använda det privata moln molnet på olika sätt, inklusive följande scenarier:

* Få åtkomst till ditt moln nätverk utan att skapa en VPN-anslutning från plats till plats.
* Använd din lokala Active Directory som identitets källa i ditt AVS-moln.
* Migrera virtuella datorer som körs lokalt till ditt moln privata moln.
* Använd ditt AVS-privata moln som en del av en katastrof återställnings lösning.
* Använd lokala resurser på dina virtuella datorer i din moln arbets belastning.

## <a name="connecting-expressroute-circuits"></a>Ansluta ExpressRoute-kretsar

För att upprätta ExpressRoute-anslutningen måste du skapa en auktorisering på din ExpressRoute-krets och ange auktoriseringsinformation för AVS.


### <a name="create-expressroute-authorization"></a>Skapa ExpressRoute-auktorisering

1. Logga in på Azure Portal.

2. Sök efter **ExpressRoute-krets** i det övre Sök fältet och klicka på **ExpressRoute-kretsar** under **tjänster**.
    [![ExpressRoute-kretsar](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Välj den ExpressRoute-krets som du vill ansluta till ditt AVS-nätverk.

4. På sidan ExpressRoute klickar du på **auktoriseringar**, anger ett namn för auktoriseringen och klickar på **Spara**.
    [![ExpressRoute krets Authorization](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Kopiera resurs-ID och verifierings nyckel genom att klicka på kopierings ikonen. Klistra in ID och nyckel i en textfil.
    [![ExpressRoute krets Authorization Copy](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **Resurs-ID** måste kopieras från användar gränssnittet och ska vara i formatet ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` när det har stöd.

6. File a Ticket med <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">stöd</a> för anslutningen som ska skapas.
    * Typ av problem: **teknisk**
    * Prenumeration: **prenumeration där AVS-tjänsten distribueras**
    * Tjänst: **VMware-lösningar (AVS)**
    * Problem typ: **tjänstbegäran**
    * Problem under typ: **skapa ExpressRoute-anslutning till lokalt**
    * Ange resurs-ID och den auktoriseringskod som du kopierade och sparade i informations fönstret.
    * Ange ett/29 nätverks adress utrymme för överförings nätverket.
    * Skickar du standard väg via ExpressRoute?
    * Ska trafikens privata moln trafik använda standard vägen som skickas via ExpressRoute?

    > [!IMPORTANT]
    > Genom att skicka standard väg kan du skicka all Internet trafik från molnets privata moln med din lokala Internet anslutning. Om du vill inaktivera standard vägen som kon figurer ATS i det privata moln molnet och använda den lokala anslutningens standard väg anger du informationen i support ärendet.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Azures nätverks anslutningar](cloudsimple-azure-network-connection.md)  
