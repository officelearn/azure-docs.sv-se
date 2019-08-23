---
title: Azure VMware-lösning av CloudSimple-on-premises-anslutning med ExpressRoute
description: Beskriver hur du begär en lokal anslutning med ExpressRoute från CloudSimple regions nätverk
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ee359b76072da3caee9ae1f5fab3d0fc28d25c0e
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972692"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>Ansluta från den lokala platsen till CloudSimple med ExpressRoute

Om du redan har en Azure ExpressRoute-anslutning från en extern plats (till exempel lokalt) till Azure kan du ansluta den till din CloudSimple-miljö. Du kan göra det via en Azure-funktion som gör att två ExpressRoute-kretsar kan ansluta till varandra. Den här metoden upprättar en säker, privat, hög bandbredds anslutning med låg latens mellan de två miljöerna.

[![Anslutning till lokalt ExpressRoute – Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="prerequisites"></a>Förutsättningar

* En Azure ExpressRoute-krets krävs innan du kan upprätta anslutningen mellan kretsen och CloudSimple privata moln nätverk.
* En användare måste ha behörighet att skapa auktoriseringsarkiv på en ExpressRoute-krets.

## <a name="scenarios"></a>Scenarier

Genom att ansluta ditt lokala nätverk till ditt privata moln nätverk kan du använda det privata molnet på olika sätt, inklusive följande:

* Få åtkomst till ditt privata moln nätverk utan att skapa en plats-till-plats-VPN-anslutning.
* Använd din lokala Active Directory som identitets källa i ditt privata moln.
* Migrera virtuella datorer som körs lokalt till ditt privata moln.
* Använd ditt privata moln som en del av en katastrof återställnings lösning.
* Använd lokala resurser på dina virtuella datorer med privat moln arbets belastning.

## <a name="connecting-expressroute-circuits"></a>Ansluta ExpressRoute-kretsar

För att upprätta ExpressRoute-anslutningen måste du skapa en auktorisering på din ExpressRoute-krets och ange auktoriseringsinformation till CloudSimple.

### <a name="create-expressroute-authorization"></a>Skapa ExpressRoute-auktorisering

1. Logga in på Azure Portal.

2. Sök efter **ExpressRoute-krets** i det övre Sök fältet och klicka på **ExpressRoute-kretsar** under **tjänster**.
    [![ExpressRoute-kretsar](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Välj den ExpressRoute-krets som du vill ansluta till ditt CloudSimple-nätverk.

4. På sidan ExpressRoute klickar du på **auktoriseringar**, anger ett namn för auktoriseringen och klickar på **Spara**.
    [![Auktorisering av ExpressRoute-krets](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Kopiera resurs-ID och verifierings nyckel genom att klicka på kopierings ikonen. Klistra in ID och nyckel i en textfil.
    [![ExpressRoute krets-auktorisering kopia](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **Resurs-ID** måste kopieras från användar gränssnittet och ska vara i formatet ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` när du ger det stöd.

6. File a Ticket med <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">stöd</a> för anslutningen som ska skapas.
    * Typ av problem: **Produkt**
    * Prenumeration: **Prenumeration där CloudSimple-tjänsten har distribuerats**
    * Tjänst: **VMware-lösning av CloudSimple**
    * Problem typ: **Tjänstbegäran**
    * Problem under typ: **Skapa ExpressRoute-anslutning till lokalt**
    * Ange resurs-ID och den auktoriseringskod som du kopierade och sparade i informations fönstret.
