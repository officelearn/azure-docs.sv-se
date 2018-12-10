---
title: 'Flytta kretsar från klassisk till Resource Manager - ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Den här sidan beskriver hur du flyttar en klassiska kretsen till Resource Manager-distributionsmodellen med hjälp av PowerShell.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: ganesr;cherylmc
ms.custom: seodec18
ms.openlocfilehash: fae5ad4a9045115c0e7a68b0164593f639824073
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141507"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>Flytta ExpressRoute-kretsar från klassisk till Resource Manager-distributionsmodellen med PowerShell

Om du vill använda en ExpressRoute-krets för både klassiska och Resource Manager-distributionsmodellen, måste du flytta kretsen till Resource Manager-distributionsmodellen. I följande avsnitt hjälper dig flytta din krets med hjälp av PowerShell.

## <a name="before-you-begin"></a>Innan du börjar

* Kontrollera att du har den senaste versionen av Azure PowerShell-moduler (minst version 1.0). Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).
* Se till att du har granskat den [krav](expressroute-prerequisites.md), [routningskrav](expressroute-routing.md), och [arbetsflöden](expressroute-workflows.md) innan du påbörjar konfigurationen.
* Granska informationen som tillhandahålls under [flytta en ExpressRoute-krets från klassisk till Resource Manager](expressroute-move.md). Se till att du förstår gränser och begränsningar.
* Kontrollera att kretsen är fullt fungerande i den klassiska distributionsmodellen.
* Kontrollera att du har en resursgrupp som har skapats i Resource Manager-distributionsmodellen.

## <a name="move-an-expressroute-circuit"></a>Flytta en ExpressRoute-krets

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Steg 1: Samla in information om krets från den klassiska distributionsmodellen

Logga in på den klassiska Azure-miljön och samla in tjänstnyckeln.

1. Logga in på ditt Azure-konto.

  ```powershell
  Add-AzureAccount
  ```

2. Välj lämplig Azure-prenumeration.

  ```powershell
  Select-AzureSubscription "<Enter Subscription Name here>"
  ```

3. Importera PowerShell-moduler för Azure och ExpressRoute.

  ```powershell
  Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
  Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'
  ```

4. Använd cmdleten nedan för att få för tjänsten för alla ExpressRoute-kretsar. Efter hämtning av nycklarna, kopiera den **tjänstnyckeln** för kretsen som du vill flytta till Resource Manager-distributionsmodellen.

  ```powershell
  Get-AzureDedicatedCircuit
  ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Steg 2: Logga in och skapa en resursgrupp

Logga in till Resource Manager-miljön och skapa en ny resursgrupp.

1. Logga in på din Azure Resource Manager-miljö.

  ```powershell
  Connect-AzureRmAccount
  ```

2. Välj lämplig Azure-prenumeration.

  ```powershell
  Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription
  ```

3. Ändra kodfragmentet nedan för att skapa en ny resursgrupp om du inte redan har en resursgrupp.

  ```powershell
  New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"
  ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Steg 3: Flytta ExpressRoute-kretsen till Resource Manager-distributionsmodellen

Du är nu redo att flytta ExpressRoute-kretsen från den klassiska distributionsmodellen till Resource Manager-distributionsmodellen. Innan du fortsätter bör du granska informationen i [flytta en ExpressRoute-krets från klassiskt till Resource Manager-distributionsmodellen](expressroute-move.md).

Om du vill flytta din krets, ändra och kör följande fragment:

```powershell
Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

En ExpressRoute-krets har inte begreppet som är kopplad till en region i klassiskt läge. Men i Resource Manager varje resurs måste mappas till en Azure-region. Den region som anges i cmdleten Move-AzureRmExpressRouteCircuit tekniskt sett vara valfri region. För organisationens, kan du vill välja en region som representerar nära din peeringplatsen.

> [!NOTE]
> När förflyttningen har slutförts kommer det nya namnet som visas i föregående cmdlet används för att adressera resursen. Kretsen i stort sett namn.
> 

## <a name="modify-circuit-access"></a>Ändra krets åtkomst

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>Så här aktiverar du ExpressRoute-krets åtkomst för båda distributionsmodellerna

När du har flyttat klassiska ExpressRoute-kretsen till Resource Manager-distributionsmodellen, kan du aktivera åtkomst till båda distributionsmodellerna. Kör följande cmdlets för att ge åtkomst till båda distributionsmodellerna:

1. Hämta information om krets.

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
  ```

2. Ange ”Tillåt klassiska åtgärder” till TRUE.

  ```powershell
  $ckt.AllowClassicOperations = $true
  ```

3. Uppdatera kretsen. När den här åtgärden har slutförts kommer du att kunna visa kretsen i den klassiska distributionsmodellen.

  ```powershell
  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

4. Kör följande cmdlet för att hämta information om ExpressRoute-kretsen. Du måste kunna se tjänstnyckeln anges.

  ```powershell
  get-azurededicatedcircuit
  ```

5. Du kan nu hantera länkar till ExpressRoute-krets med hjälp av kommandona modellen för klassisk distribution för klassiska virtuella nätverk och Resource Manager-kommandon för Resource Manager-VNets. I följande artiklar hjälper dig att hantera länkar till ExpressRoute-krets:

    * [Länka ditt virtuella nätverk till ExpressRoute-kretsen i Resource Manager-distributionsmodellen](expressroute-howto-linkvnet-arm.md)
    * [Länka ditt virtuella nätverk till ExpressRoute-kretsen i den klassiska distributionsmodellen](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>Inaktivera ExpressRoute-krets åtkomst till den klassiska distributionsmodellen

Kör följande cmdlets för att inaktivera åtkomst till den klassiska distributionsmodellen.

1. Få information om ExpressRoute-kretsen.

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
  ```

2. Ange ”Tillåt klassiska åtgärder” till FALSE.

  ```powershell
  $ckt.AllowClassicOperations = $false
  ```

3. Uppdatera kretsen. När den här åtgärden har slutförts, kan du inte visa kretsen i den klassiska distributionsmodellen.

  ```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

## <a name="next-steps"></a>Nästa steg

* [Skapa och ändra routning för ExpressRoute-krets](expressroute-howto-routing-arm.md)
* [Länka ditt virtuella nätverk till ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)
