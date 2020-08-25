---
title: Lokala peer-miljöer i ett privat moln
description: I den här självstudien om Azure VMware-lösning skapar du ExpressRoute Global Reach peering till ett privat moln i en Azure VMware-lösning.
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: db3f5988cb8c07d9b6e80f500ac6aff8f96dfded
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750445"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Självstudie: peer-lokala miljöer till ett privat moln

ExpressRoute Global Reach ansluter din lokala miljö till dina privata moln. ExpressRoute Global Reach-anslutningen upprättas mellan en privat Cloud ExpressRoute-krets och en befintlig ExpressRoute-anslutning till dina lokala miljöer.  Det finns instruktioner för att konfigurera ExpressRoute-Global Reach med Azure CLI och PowerShell, och vi har förstärkt [CLI-kommandona](../expressroute/expressroute-howto-set-global-reach-cli.md) med detaljerad information och exempel som hjälper dig att konfigurera ExpressRoute-Global Reach-peering mellan lokala miljöer med ett privat moln i Azure VMware-lösningen.   

Innan du aktiverar anslutning mellan två ExpressRoute-kretsar med ExpressRoute Global Reach bör du läsa dokumentationen om hur du [aktiverar anslutningar i olika Azure-prenumerationer](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  ExpressRoute-kretsen som du använder när du [konfigurerar Azure-till-Private Cloud Networking](tutorial-configure-networking.md) kräver att du skapar och använder auktoriseringsregler när du peer-koppla till ExpressRoute-gatewayer eller med andra ExpressRoute-kretsar som använder Global Reach. Du har redan använt en auktoriseringspost från ExpressRoute-kretsen och du skapar en andra som peer-dator med din lokala ExpressRoute-krets.

> [!TIP]
> I samband med dessa instruktioner är din lokala ExpressRoute-krets _krets 1_, och din privata moln ExpressRoute-krets är i en annan prenumeration och märkt _krets 2_. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Använd de befintliga anvisningarna för att hantera ExpressRoute-kretsar och ExpressRoute Global Reach peering
> * Skapa en auktoriseringsregel för _krets 2_, den privata molnets ExpressRoute-krets
> * Använd Azure CLI i en Cloud Shell i prenumerationen på _krets 1_ för att aktivera lokal-till-privat-moln ExpressRoute Global Reach-peering

## <a name="prerequisites"></a>Förutsättningar

Kraven för den här självstudien är:
- Ett privat moln med sin ExpressRoute-krets som är peer-kopplad med en ExpressRoute-gateway i ett virtuellt Azure-nätverk (VNet) – det här är _krets 2_ från perspektivet för peering-procedurer.
- En separat, fungerande ExpressRoute-krets som används för att ansluta lokala miljöer till Azure – det här är en _krets 1_ från de olika peering-procedurerna.
- Ett/29 icke-överlappande [nätverks adress block](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) för ExpressRoute Global Reach-peering.

## <a name="create-an-expressroute-authorization-key-in-the-azure-vmware-solution-private-cloud"></a>Skapa en ExpressRoute-auktoriseringspost i Azure VMware-lösningen privat moln

1. I **översikten över**det privata molnet under hantera väljer du **anslutnings > ExpressRoute > begära en nyckel för autentisering**.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Välj anslutnings > ExpressRoute > begära en autentiseringsnyckel för att starta en ny begäran.":::

2. Ange namnet på verifierings nyckeln och välj **skapa**. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Klicka på skapa för att skapa en ny autentiseringsnyckel. ":::

   När den nya nyckeln har skapats visas den i listan över auktoriseringsregler för det privata molnet. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Bekräfta att den nya verifierings nyckeln visas i listan över nycklar för det privata molnet. ":::

3. Anteckna auktoriseringsregeln och ExpressRoute-ID, tillsammans med adress blocket/29. Du ska använda dem i nästa steg för att slutföra peer kopplingen. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Peer-privat moln till lokalt med hjälp av auktoriseringsregeln

Nu när du har skapat en auktoriseringsregel för ExpressRoute-kretsen för det privata molnet kan du koppla den till din lokala ExpressRoute-krets.  Peer koppling görs från den lokala ExpressRoute-kretsens perspektiv med hjälp av Azure CLI i ett Cloud Shell och resurs-ID och en nyckel för din privata moln ExpressRoute-krets, som skapades i föregående steg.

> [!TIP]  
> För det kortfattat i Azure CLI-kommandot utdata kan de här instruktionerna använda ett [ `–query` argument för att köra en JMESPath-fråga för att endast visa de nödvändiga resultaten](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).


1. Logga in på Azure Portal med samma prenumeration som den lokala ExpressRoute-kretsen och öppna en Cloud Shell. Lämna gränssnittet som bash.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Logga in på Azure Portal och öppna en Cloud Shell.":::
 
2. På kommando raden anger du Azure CLI-kommandot för att skapa peering med hjälp av din specifika information och resurs-ID, auktoriseringskod och/29 CIDR-Adressblock. 

   Följande är ett exempel på det kommando som du kommer att använda och utdata som indikerar en lyckad peering. Exempel kommandot baseras på kommandot som används i [steg 3 i "aktivera anslutning mellan ExpressRoute-kretsar i olika Azure-prenumerationer"](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Skapa ExpressRoute Global Reach peering med Azure CLI-kommandot i en Cloud Shell.":::
 
   Nu bör du kunna ansluta från lokala miljöer till ditt privata moln via ExpressRoute Global Reach-peering.

> [!TIP]
> Du kan ta bort peering som du nyss skapade genom att följa anvisningarna [för att inaktivera anslutningen mellan dina lokala nätverk](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) .


## <a name="next-steps"></a>Nästa steg

Om du planerar att använda Hybrid Cloud Extension (HCX) för att migrera VM-arbetsbelastningar till ditt privata moln använder du proceduren [Installera HCX för Azure VMware-lösning](hybrid-cloud-extension-installation.md) .


<!-- LINKS - external-->

<!-- LINKS - internal -->
