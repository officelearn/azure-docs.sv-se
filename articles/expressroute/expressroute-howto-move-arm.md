---
title: 'Azure ExpressRoute: Flytta klassiska kretsar till Resource Manager'
description: På den här sidan beskrivs hur du flyttar en klassisk krets till Resurshanterarens distributionsmodell med PowerShell.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: cherylmc
ms.openlocfilehash: 4e49a3bc803733f5e78207fa3573c93395924d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74080158"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>Flytta ExpressRoute-kretsar från klassisk till Resurshanterarens distributionsmodell med PowerShell

Om du vill använda en ExpressRoute-krets för distributionsmodellerna för både den klassiska och Resource Manager måste du flytta kretsen till Resurshanterarens distributionsmodell. Följande avsnitt hjälper dig att flytta kretsen med PowerShell.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

* Kontrollera att du har installerat både de klassiska och Az Azure PowerShell-modulerna lokalt på datorn. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).
* Kontrollera att du har granskat [förutsättningarna,](expressroute-prerequisites.md) [routningskraven](expressroute-routing.md)och [arbetsflödena](expressroute-workflows.md) innan du börjar konfigurera.
* Granska informationen som finns under [Flytta en ExpressRoute-krets från klassisk till Resurshanteraren](expressroute-move.md). Se till att du till fullo förstår gränserna och begränsningarna.
* Kontrollera att kretsen är i full drift i den klassiska distributionsmodellen.
* Kontrollera att du har en resursgrupp som har skapats i resurshanterarens distributionsmodell.

## <a name="move-an-expressroute-circuit"></a>Flytta en ExpressRoute-krets

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Steg 1: Samla in kretsinformation från den klassiska distributionsmodellen

Logga in på den klassiska Azure-miljön och samla in tjänstnyckeln.

1. Logga in på ditt Azure-konto.

   ```powershell
   Add-AzureAccount
   ```

2. Välj lämplig Azure-prenumeration.

   ```powershell
   Select-AzureSubscription "<Enter Subscription Name here>"
   ```

3. Importera PowerShell-modulerna för Azure och ExpressRoute.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
   ```

4. Använd cmdleten nedan för att hämta serviceknapparna för alla dina ExpressRoute-kretsar. När du har hämtat nycklarna kopierar du **servicenyckeln** för den krets som du vill flytta till resurshanterarens distributionsmodell.

   ```powershell
   Get-AzureDedicatedCircuit
   ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Steg 2: Logga in och skapa en resursgrupp

Logga in i Resource Manager-miljön och skapa en ny resursgrupp.

1. Logga in på din Azure Resource Manager-miljö.

   ```powershell
   Connect-AzAccount
   ```

2. Välj lämplig Azure-prenumeration.

   ```powershell
   Get-AzSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzSubscription
   ```

3. Ändra kodavsnittet nedan för att skapa en ny resursgrupp om du inte redan har en resursgrupp.

   ```powershell
   New-AzResourceGroup -Name "DemoRG" -Location "West US"
   ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Steg 3: Flytta ExpressRoute-kretsen till resurshanterarens distributionsmodell

Du är nu redo att flytta din ExpressRoute-krets från den klassiska distributionsmodellen till Resurshanterarens distributionsmodell. Innan du fortsätter bör du granska informationen i [Flytta en ExpressRoute-krets från den klassiska till resurshanterarens distributionsmodell](expressroute-move.md).

Om du vill flytta kretsen ändrar och kör du följande kodavsnitt:

```powershell
Move-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

I klassiskt läge har en ExpressRoute-krets inte begreppet att vara knuten till en region. I Resource Manager måste dock varje resurs mappas till en Azure-region. Den region som anges i Move-AzExpressRouteCircuit cmdlet kan tekniskt sett vara vilken region som helst. I organisationssyfte kanske du vill välja en region som representerar din peering-plats.

> [!NOTE]
> När flytten är klar används det nya namnet som visas i föregående cmdlet för att adressera resursen. Kretsen kommer i huvudsak att döpas om.
> 

## <a name="modify-circuit-access"></a>Ändra åtkomst till krets

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>Så här aktiverar du ExpressRoute-kretsåtkomst för båda distributionsmodellerna

När du har flyttat den klassiska ExpressRoute-kretsen till Resurshanterarens distributionsmodell kan du aktivera åtkomst till båda distributionsmodellerna. Kör följande cmdlets för att aktivera åtkomst till båda distributionsmodellerna:

1. Hämta kretsdetaljerna.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Ställ in "Tillåt klassiska operationer" på SANT.

   ```powershell
   $ckt.AllowClassicOperations = $true
   ```

3. Uppdatera kretsen. När den här åtgärden har slutförts kan du visa kretsen i den klassiska distributionsmodellen.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

4. Kör följande cmdlet för att få information om ExpressRoute-kretsen. Du måste kunna se servicenyckeln i listan.

   ```powershell
   get-azurededicatedcircuit
   ```

5. Du kan nu hantera länkar till ExpressRoute-kretsen med hjälp av de klassiska distributionsmodellkommandona för klassiska virtuella nätverk och Resource Manager-kommandona för Resource Manager-virtuella nätverk. Följande artiklar hjälper dig att hantera länkar till ExpressRoute-kretsen:

    * [Länka ditt virtuella nätverk till expressroutekretsen i resurshanterarens distributionsmodell](expressroute-howto-linkvnet-arm.md)
    * [Länka ditt virtuella nätverk till din ExpressRoute-krets i den klassiska distributionsmodellen](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>Så här inaktiverar du ExpressRoute-kretsåtkomst till den klassiska distributionsmodellen

Kör följande cmdlets för att inaktivera åtkomst till den klassiska distributionsmodellen.

1. Få information om ExpressRoute-kretsen.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Ställ in "Tillåt klassiska operationer" till FALSKT.

   ```powershell
   $ckt.AllowClassicOperations = $false
   ```

3. Uppdatera kretsen. När den här åtgärden har slutförts kan du inte visa kretsen i den klassiska distributionsmodellen.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

## <a name="next-steps"></a>Nästa steg

* [Skapa och ändra routning för din ExpressRoute-krets](expressroute-howto-routing-arm.md)
* [Länka ditt virtuella nätverk till din ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)
