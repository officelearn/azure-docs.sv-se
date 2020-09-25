---
title: Självstudie – lokala peer-miljöer i ett privat moln
description: Lär dig hur du skapar ExpressRoute Global Reach peering till ett privat moln i en Azure VMware-lösning.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 679887e1998a534001e72ddff7a1a184a84bd831
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91254741"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Självstudie: peer-lokala miljöer till ett privat moln

ExpressRoute Global Reach ansluter din lokala miljö till ditt privata moln i Azure VMware-lösningen. ExpressRoute Global Reach-anslutningen upprättas mellan det privata molnet ExpressRoute-kretsen och en befintlig ExpressRoute-anslutning till dina lokala miljöer. 

ExpressRoute-kretsen som du använder när du [konfigurerar Azure-till-Private Cloud Networking](tutorial-configure-networking.md) kräver att du skapar och använder auktoriseringsregler när du peer-koppla till ExpressRoute-gatewayer eller med andra ExpressRoute-kretsar som använder Global Reach. Du har redan använt en auktoriseringspost från ExpressRoute-kretsen och i den här självstudien skapar du en andra med en-peer med din lokala ExpressRoute-krets.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en andra auktoriseringsregel för _krets 2_, den privata molnets ExpressRoute-krets
> * Använd antingen [Azure Portal](#azure-portal-method) eller [Azure CLI i en Cloud Shell-metod](#azure-cli-in-a-cloud-shell-method) i prenumerationen på _krets 1_ för att aktivera lokal-till-privat-moln ExpressRoute Global Reach-peering


## <a name="before-you-begin"></a>Innan du börjar

Innan du aktiverar anslutning mellan två ExpressRoute-kretsar med ExpressRoute Global Reach bör du läsa dokumentationen om hur du [aktiverar anslutningar i olika Azure-prenumerationer](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  


## <a name="prerequisites"></a>Förutsättningar

1. Upprättad anslutning till och från ett privat moln i Azure VMware-lösningen med sin ExpressRoute-krets med en ExpressRoute-gateway i ett virtuellt Azure-nätverk (VNet), som är _krets 2_ från peering-procedurer.  
1. En separat, fungerande ExpressRoute-krets som används för att ansluta lokala miljöer till Azure, som är _krets 1_ från peering-procedurens perspektiv.
1. Ett/29 icke-överlappande [nätverks adress block](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) för ExpressRoute Global Reach-peering.

> [!TIP]
> I samband med dessa förutsättningar är din lokala ExpressRoute-krets _krets 1_, och din privata moln ExpressRoute-krets är i en annan prenumeration och har märkt _krets 2_. 


## <a name="create-an-expressroute-authorization-key-in-the-private-cloud"></a>Skapa en ExpressRoute-auktoriseringsregel i det privata molnet

1. I **översikten över**det privata molnet under hantera väljer du **anslutnings > ExpressRoute > begära en nyckel för autentisering**.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Välj anslutnings > ExpressRoute > begära en autentiseringsnyckel för att starta en ny begäran.":::

2. Ange namnet på verifierings nyckeln och välj **skapa**. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Klicka på skapa för att skapa en ny autentiseringsnyckel. ":::

   När den nya nyckeln har skapats visas den i listan över auktoriseringsregler för det privata molnet. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Bekräfta att den nya verifierings nyckeln visas i listan över nycklar för det privata molnet. ":::

3. Anteckna auktoriseringsregeln och ExpressRoute-ID, tillsammans med adress blocket/29. Du ska använda dem i nästa steg för att slutföra peer kopplingen. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Peer-privat moln till lokalt med hjälp av auktoriseringsregeln

Nu när du har skapat en auktoriseringsregel för ExpressRoute-kretsen för det privata molnet kan du koppla den till din lokala ExpressRoute-krets.  Peer koppling görs från den lokala ExpressRoute-kretsens perspektiv i antingen [Azure Portal](#azure-portal-method) eller med hjälp av [Azure CLI i en Cloud Shell](#azure-cli-in-a-cloud-shell-method). Med någon av metoderna kommer du att använda resurs-ID och verifierings nyckel för din privata moln ExpressRoute-krets, som du skapade i föregående steg, för att slutföra peer kopplingen.

### <a name="azure-portal-method"></a>Azure Portal metod

1. Logga in på [Azure Portal](https://portal.azure.com) med samma prenumeration som den lokala ExpressRoute-kretsen.

1. I **översikten över**det privata molnet under hantera väljer du **anslutnings > ExpressRoute Global Reach > Lägg till**.

   :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Från menyn väljer du anslutning, fliken ExpressRoute Global Reach och sedan Lägg till.":::

1. Du kan skapa en lokal moln anslutning genom att göra något av följande:

   - Välj ExpressRoute-kretsen i listan.
   - Om du har ett krets-ID kopierar du och klistrar in det.

1. Välj **Anslut**. Den nya anslutningen visas i listan lokal moln anslutning.  

>[!TIP]
>Du kan ta bort eller koppla från en anslutning från listan genom att välja **mer**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Koppla från eller ta bort en lokal anslutning":::

### <a name="azure-cli-in-a-cloud-shell-method"></a>Azure CLI i en Cloud Shell metod

Vi har förstärkt CLI- [kommandona](../expressroute/expressroute-howto-set-global-reach-cli.md) med detaljerad information och exempel som hjälper dig att konfigurera ExpressRoute-Global Reach peering mellan lokala miljöer till ett privat moln i Azure VMware-lösningen.  

> [!TIP]  
> För det kortfattat i Azure CLI-kommandot utdata kan de här instruktionerna använda ett [ `–query` argument för att köra en JMESPath-fråga för att endast visa de nödvändiga resultaten](https://docs.microsoft.com/cli/azure/query-azure-cli).


1. Logga in på Azure Portal med samma prenumeration som den lokala ExpressRoute-kretsen och öppna en Cloud Shell. Lämna gränssnittet som bash.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Logga in på Azure Portal och öppna en Cloud Shell.":::
 
2. På kommando raden anger du Azure CLI-kommandot för att skapa peering med hjälp av din specifika information och resurs-ID, auktoriseringskod och/29 CIDR-Adressblock. 

   Följande är ett exempel på det kommando som du kommer att använda och utdata som indikerar en lyckad peering. Exempel kommandot baseras på kommandot som används i [steg 3 i "aktivera anslutning mellan ExpressRoute-kretsar i olika Azure-prenumerationer"](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Skapa ExpressRoute Global Reach peering med Azure CLI-kommandot i en Cloud Shell.":::
 
   Nu bör du kunna ansluta från lokala miljöer till ditt privata moln via ExpressRoute Global Reach-peering.

> [!TIP]
> Du kan ta bort peering som du nyss skapade genom att följa anvisningarna [för att inaktivera anslutningen mellan dina lokala nätverk](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) .


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar en andra auktoriseringskod för ExpressRoute-kretsen för det privata molnet och aktiverat det lokala molnet ExpressRoute Global Reach peering. 

Fortsätt till nästa självstudie och lär dig hur du distribuerar och konfigurerar VMware HCX-lösningen för ditt privata moln i Azure VMware-lösningen.

> [!div class="nextstepaction"]
> [Distribuera och konfigurera VMware HCX](hybrid-cloud-extension-installation.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
