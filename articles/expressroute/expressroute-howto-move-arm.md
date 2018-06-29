---
title: 'Flytta ExpressRoute-kretsar från classic till Resource Manager: PowerShell: Azure | Microsoft Docs'
description: Den här sidan beskrivs hur du flyttar en klassiska krets till Resource Manager-distributionsmodellen med hjälp av PowerShell.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 08152836-23e7-42d1-9a56-8306b341cd91
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 20914eec070452186295f6d87a85ea0675ebaf4c
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060094"
---
# <a name="move-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model-using-powershell"></a>Flytta ExpressRoute-kretsar från klassiskt till Resource Manager-distributionsmodellen med hjälp av PowerShell

Om du vill använda en ExpressRoute-krets för både klassiskt och Resource Manager distributionsmodellerna, måste du flytta kretsen till Resource Manager-distributionsmodellen. I följande avsnitt hjälpa dig att flytta kretsen med hjälp av PowerShell.

## <a name="before-you-begin"></a>Innan du börjar

* Kontrollera att du har den senaste versionen av Azure PowerShell-moduler (minst version 1.0). Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).
* Kontrollera att du har granskat den [krav](expressroute-prerequisites.md), [routningskrav](expressroute-routing.md), och [arbetsflöden](expressroute-workflows.md) innan du börjar konfigurera.
* Granska informationen som ges enligt [flytta en ExpressRoute-krets från klassisk till Resource Manager](expressroute-move.md). Kontrollera att du förstår de gränser och begränsningar.
* Kontrollera att kretsen är fullt fungerande i den klassiska distributionsmodellen.
* Se till att du har en resursgrupp som skapades i Resource Manager-distributionsmodellen.

## <a name="move-an-expressroute-circuit"></a>Flytta en ExpressRoute-krets

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Steg 1: Samla in krets information från den klassiska distributionsmodellen

Logga in på den klassiska Azure-miljön och samla in nyckeln för tjänsten.

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

4. Använd cmdlet nedan för att hämta för tjänsten för alla ExpressRoute-kretsar. Efter att du hämtar nycklarna, kopiera den **tjänstnyckeln** beroendestruktur som du vill flytta till Resource Manager-distributionsmodellen.

  ```powershell
  Get-AzureDedicatedCircuit
  ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Steg 2: Logga in och skapa en resursgrupp

Logga in i Resource Manager-miljön och skapa en ny resursgrupp.

1. Logga in på Azure Resource Manager-miljö.

  ```powershell
  Connect-AzureRmAccount
  ```

2. Välj lämplig Azure-prenumeration.

  ```powershell
  Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription
  ```

3. Ändra fragment nedan för att skapa en ny resursgrupp om du inte redan har en resursgrupp.

  ```powershell
  New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"
  ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Steg 3: Flytta ExpressRoute-kretsen till Resource Manager-distributionsmodellen

Du är nu redo att flytta ExpressRoute-krets från den klassiska distributionsmodellen till Resource Manager-distributionsmodellen. Innan du fortsätter bör du granska informationen i [flyttar en ExpressRoute-krets från klassiskt till Resource Manager-distributionsmodellen](expressroute-move.md).

Flytta kretsen, ändra och köra följande utdrag:

```powershell
Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

> [!NOTE]
> En ExpressRoute-kretsen har inte begreppet är knutna till en region i klassiskt läge. I Resource Manager (ARM), måste alla resurser som ska mappas till en Azure-region. Den region som anges i cmdleten Move-AzureRmExpressRouteCircuit kan tekniskt vara en region. Organisera, kan du vill välja en region som representerar din peeringplatsen noggrant.
> 

> [!NOTE]
> När övergången är klar, används det nya namnet som visas i föregående cmdlet för att adressera resursen. Kretsen namnges i praktiken.
> 

## <a name="modify-circuit-access"></a>Ändra krets åtkomst

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>Att aktivera åtkomst för ExpressRoute-krets för båda distributionsmodellerna

När du flyttar din klassiska ExpressRoute-krets till Resource Manager-distributionsmodellen, kan du aktivera åtkomst till båda distributionsmodellerna. Kör följande cmdlets för att ge åtkomst till båda distributionsmodellerna:

1. Hämta information om krets.

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
  ```

2. Ange ”Tillåt klassiska Operations” true.

  ```powershell
  $ckt.AllowClassicOperations = $true
  ```

3. Uppdatera kretsen. När den här åtgärden har slutförts, kommer du att kunna visa kretsen i den klassiska distributionsmodellen.

  ```powershell
  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

4. Kör följande cmdlet för att få information om ExpressRoute-kretsen. Du måste kunna se tjänstnyckeln anges.

  ```powershell
  get-azurededicatedcircuit
  ```

5. Du kan nu hantera länkar till ExpressRoute-kretsen med kommandona klassisk distribution modellen för klassiska Vnet och Resource Manager-kommandon för Resource Manager VNets. I följande artiklar hjälper dig att hantera länkar till ExpressRoute-kretsen:

    * [Länka ditt virtuella nätverk till ExpressRoute-krets i Resource Manager-distributionsmodellen](expressroute-howto-linkvnet-arm.md)
    * [Länka ditt virtuella nätverk till ExpressRoute-krets i den klassiska distributionsmodellen](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>Inaktivera ExpressRoute-kretsen åtkomst till den klassiska distributionsmodellen

Kör följande cmdlets för att inaktivera åtkomst till den klassiska distributionsmodellen.

1. Hämta information om ExpressRoute-kretsen.

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
  ```

2. Ange ”Tillåt klassiska Operations” till FALSE.

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
