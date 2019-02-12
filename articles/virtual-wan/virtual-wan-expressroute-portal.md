---
title: Använda Azure Virtual WAN för att skapa ExpressRoute-anslutningar till Azure och lokala miljöer | Microsoft Docs
description: I den här självstudien lär du dig att använda Azure Virtual WAN för att skapa ExpressRoute-anslutningar till Azure och lokala miljöer.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/5/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 17d80d07f9b272b0dcb7449404d5d6626e72ce65
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692898"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan-preview"></a>Självstudier: Skapa en ExpressRoute-association med hjälp av Azure Virtual WAN (förhandsversion)

Den här självstudien visar hur du använder Virtual WAN för att ansluta dina resurser i Azure via en ExpressRoute-krets och en association. Mer information om virtuella WAN-nätverk finns i [översikten om virtuellt WAN](virtual-wan-about.md)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett vWAN
> * Skapa en hubb
> * Hitta och associera en krets med hubben
> * Associera kretsen med en hubb
> * Ansluta ett virtuellt nätverk till en hubb
> * Visa virtuellt WAN
> * Visa information om resurshälsa
> * Övervaka en anslutning

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="register"></a>Registrera funktionen

Du måste registrera din prenumeration i förhandsversionen innan du kan konfigurera Virtual WAN. I annat fall kan du inte arbeta med virtuellt WAN i portalen. Skicka ett e-postmeddelande till **azurevirtualwan@microsoft.com** med prenumerations-ID:t. Du får ett e-postmeddelande tillbaka när din prenumeration har registrerats.

**Överväganden för förhandsversion:**

* Regional tillgänglighet: Västra centrala USA
* ExpressRoute-kretsen måste vara aktiverad i ett land som stöder [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#where-is-expressroute-global-reach-supported)

## <a name="vnet"></a>1. Skapa ett virtuellt nätverk

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="openvwan"></a>2. Skapa ett virtuellt WAN

Öppna en webbläsare, navigera till [Azure-portalen (förhandsversion)](https://aka.ms/azurevirtualwanpreviewfeatures) och logga in med ditt Azure-konto.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

### <a name="getting-started-page"></a>Sida för att komma igång

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-gettingstarted-include.md)]

## <a name="hub"></a>3. Skapa en hubb

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="hub"></a>4. Hitta och associera en krets med hubben

1. Välj ditt vWAN. Under **Virtual WAN architecture** (Virtuell WAN-arkitektur) väljer du **ExpressRoute Circuits** (ExpressRoute-kretsar)
2. Om ExpressRoute-kretsen är i samma prenumeration som ditt vWAN klickar du på **Välj ExpressRoute-krets** från din prenumeration 
3. Använd listrutan och välj den ExpressRoute som du vill associera med hubben.
4. Om ExpressRoute-kretsen inte är i samma prenumeration eller om du har fått [en auktoriseringsnyckel och ett peer-ID](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md) väljer du **Find a circuit redeeming an authorization key** (Hitta en krets som löser in en auktoriseringsnyckel)
5. Ange följande uppgifter:
* **Auktoriseringsnyckel** – genereras av kretsägaren enligt beskrivningen ovan
* **Peer-kretsens URI** – krets-URI som tillhandahålls av kretsägaren och är den unika identifieraren för kretsen
* **Routningsvikt** - [Routningsvikt](../expressroute/expressroute-optimize-routing.md) gör att du kan föredra vissa vägar när flera kretsar från olika peeringplatser är anslutna till samma hubb
6. Klicka på **Hitta krets** och välj kretsen om den hittas
7. Välj 1 eller flera hubbar i listrutan och klicka på **Spara**

## <a name="vnet"></a>5. Ansluta ett virtuellt nätverk till en hubb

I det här steget skapar du peeringanslutningen mellan hubben och ett virtuellt nätverk. Upprepa de här stegen för varje virtuellt nätverk du vill ansluta.

1. På sidan för det virtuella WAN-nätverket klickar du på **virtuell nätverksanslutning**.
2. På sidan för virtuell nätverksanslutning klickar du på **+Lägg till anslutning**.
3. Fyll i följande fält på sidan **Lägg till anslutning**:

    * **Anslutningsnamn** – Namnge anslutningen.
    * **Hubbar** – Välj den hubb du vill koppla till anslutningen.
    * **Prenumeration** – Kontrollera prenumerationen.
    * **Virtuellt nätverk** – Välj det virtuella nätverk du vill ansluta till hubben. Det virtuella nätverket får inte ha någon befintlig gateway för virtuellt nätverk.


## <a name="viewwan"></a>6. Visa virtuellt WAN

1. Navigera till det virtuella WAN-nätverket.
2. Varje punkt på kartan på sidan Översikt motsvarar en hubb. För muspekaren över en punkt så visas en sammanfattning av hubbens hälsotillstånd.
3. I avsnittet om hubbar och anslutningar kan du visa hubbstatus, plats, region, VPN-anslutningsstatus och byte in och ut.

## <a name="viewhealth"></a>7. Visa resurshälsa

1. Navigera till ditt WAN.
2. Öppna WAN-sidan. I avsnittet **SUPPORT + felsökning** klickar du på **Hälsa** och visar resursen.

## <a name="connectmon"></a>8. Övervaka en anslutning

Skapa en anslutning för att övervaka kommunikation mellan en virtuell Azure-dator och en fjärrplats. Information om hur du ställer in en anslutningsövervakare finns i dokumentationen om att [övervaka nätverkskommunikation](~/articles/network-watcher/connection-monitor.md). Källfältet är IP för den virtuella datorn i Azure och mål-IP är plats-IP-adressen.

## <a name="cleanup"></a>9. Rensa resurser

Du kan använda [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen och alla resurser den innehåller när du inte längre behöver dem. Ersätt myResourceGroup med namnet på resursgruppen och kör följande PowerShell-kommando:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett vWAN
> * Skapa en hubb
> * Hitta och associera en krets med hubben
> * Associera kretsen med en hubb
> * Ansluta ett virtuellt nätverk till en hubb
> * Visa virtuellt WAN
> * Visa information om resurshälsa
> * Övervaka en anslutning

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).
