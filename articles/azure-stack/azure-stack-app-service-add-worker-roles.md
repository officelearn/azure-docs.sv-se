---
title: Skala ut arbetsroller i App Services – Azure Stack | Microsoft Docs
description: Detaljerad vägledning för att skala Azure Stack App Services
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2018
ms.author: anwestg
ms.reviewer: sethm
ms.openlocfilehash: f4a3cd7d09c18506a85317db7a360b8de3a9630b
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077030"
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>App Service i Azure Stack: lägga till fler infrastruktur- eller worker-roller

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*  

Det här dokumentet innehåller anvisningar om hur du skalar App Service på Azure Stack-infrastruktur- och worker-roller. Den innehåller anvisningar för att skapa ytterligare worker-roller för att stödja program i alla storlekar.

> [!NOTE]
> Om din Azure Stack-miljön inte har fler än 96 GB RAM-minne kan det vara svårt att lägga till ytterligare kapacitet.

App Service i Azure Stack, som standard har stöd för kostnadsfria och delade arbetarnivåer. Om du vill lägga till andra arbetarnivåer, som du behöver lägga till flera worker-roller.

Om du inte är säker på vad som har distribuerats med App Service på Azure Stack-installation kan du granska ytterligare information i den [App Service i Azure Stack-översikt](azure-stack-app-service-overview.md).

Azure App Service i Azure Stack distribuerar alla roller med hjälp av Virtual Machine Scale Sets och drar därmed nytta av skalningsmöjligheterna med den här arbetsbelastningen. Därför sker alla skalning av arbetarnivåer via App-tjänstadministratör

> [!IMPORTANT]
> För närvarande går det inte att skala VM-skalningsuppsättningar i portalen som du identifierade i Azure Stack viktig information, därför använda PowerShell-exempel för att skala ut.
>
>

## <a name="add-additional-workers-with-powershell"></a>Lägg till fler arbeten med PowerShell

1. [Konfigurera Azure Stack-Admin-miljö i PowerShell](azure-stack-powershell-configure-admin.md)

2. Använd det här exemplet för att skala upp skalningsuppsättningen:
   ```powershell
   
    ##### Scale out the AppService Role instances ######
   
    # Set context to AzureStack admin.
    Login-AzureRmAccount -EnvironmentName AzureStackAdmin
                                                 
    ## Name of the Resource group where AppService is deployed.
    $AppServiceResourceGroupName = "AppService.local"

    ## Name of the ScaleSet : e.g. FrontEndsScaleSet, ManagementServersScaleSet, PublishersScaleSet , LargeWorkerTierScaleSet,      MediumWorkerTierScaleSet, SmallWorkerTierScaleSet, SharedWorkerTierScaleSet
    $ScaleSetName = "SharedWorkerTierScaleSet"

    ## TotalCapacity is sum of the instances needed at the end of operation. 
    ## e.g. if your VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  

    # Get current scale set
    $vmss = Get-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName

    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
   ```    

   > [!NOTE]
   > Det här steget kan ta flera timmar att slutföra beroende på typ av roll och hur många instanser.
   >
   >

3. Övervaka status för nya rollinstanserna i App Service-Administration, för att kontrollera status för en enskild rollinstans klickar du på vilken roll i listan.

## <a name="add-additional-workers-directly-within-the-app-service-resource-provider-admin"></a>Lägg till fler arbeten direkt i App Service-administratör Resource Provider.

1. Logga in på Azure Stack-administrationsportalen som tjänstadministratör.

2. Bläddra till **Apptjänster**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Klicka på **roller**. Här kan du se en analys på detaljnivå för alla App Service-roller som distribueras.

4. Högerklicka på raden i den typ som du vill skala och klicka sedan på **ScaleSet**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Klicka på **skalning**, väljer du antalet instanser som du vill skala till och klicka sedan på **spara**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. App Service i Azure Stack kommer nu lägga till ytterligare virtuella datorer, konfigurera dem, installera alla nödvändiga program och markera dem som redo när den här processen är klar. Den här processen kan ta cirka 80 minuter.

7. Du kan övervaka förloppet för beredskap för de nya rollerna genom att visa arbetare i den **roller** bladet.

## <a name="result"></a>Resultat

”Arbetarna” blir tillgängliga för användare att distribuera deras arbetsbelastning till dem när de är helt distribuerats och är redo. Nedan visas ett exempel på flera olika prisnivåer tillgängliga som standard. Om det finns inga tillgängliga arbeten för en viss arbetarnivå, är möjlighet att välja motsvarande prisnivån inte tillgänglig.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Om du vill skala ut Management klientdelen eller utgivare rollerna tillgång till skalar du ut motsvarande rolltypen. 
>
>

Om du vill skala ut Management, klient eller utgivare roller, följer du samma steg att välja rätt roll. Domänkontrollanter distribueras inte som Skalningsuppsättningar och därför två som ska distribueras vid installationen för alla distributioner av produktion.

### <a name="next-steps"></a>Nästa steg

[Konfigurera distributionskällor](azure-stack-app-service-configure-deployment-sources.md)
