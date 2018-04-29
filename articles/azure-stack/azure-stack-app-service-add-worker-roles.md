---
title: Skala ut arbetsroller i Apptjänster - Azure Stack | Microsoft Docs
description: Detaljerade riktlinjer för att skala Azure Stack Apptjänster
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
ms.date: 03/08/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 3bbd39f99c72d92bd7969de52d893c892314fc99
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>Apptjänst Azure stacken: lägga till fler infrastruktur eller worker-roller

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*  

Det här dokumentet innehåller instruktioner om hur du skalar Apptjänst på Azure-stacken infrastruktur- och arbetsroller roller. Den innehåller anvisningar för att skapa ytterligare arbetsroller för program i alla storlekar.

> [!NOTE]
> Om miljön i Azure-stacken inte har fler än 96 GB RAM-minne kan det vara svårt att lägga till ytterligare kapacitet.

Apptjänst på Azure-stacken, som standard stöder lediga och delade worker nivåer. Om du vill lägga till andra worker-nivåer, som du behöver lägga till flera arbetsroller.

Om du inte är säker på vad som har distribuerats med App Service på Azure-stacken installation kan du granska ytterligare information i den [Apptjänst på Azure-stacken översikt](azure-stack-app-service-overview.md).

Azure Apptjänst Azure stacken distribuerar alla roller med hjälp av virtuella datorer och drar därmed nytta av funktioner för skalning av arbetsbelastningen. Därför sker alla skalning av worker-nivåer via App Service administratören.

> [!IMPORTANT]
> Det är för närvarande inte möjligt att skala skalningsuppsättningar i virtuella datorer i portalen som identifieras i Azure-stacken viktig information måste du därför använda PowerShell-exempel för att skala ut.
>
>

## <a name="add-additional-workers-with-powershell"></a>Lägg till ytterligare arbetare med PowerShell

1. [Konfigurera Azure Stack Admin-miljö i PowerShell](azure-stack-powershell-configure-admin.md)

2. Använd det här exemplet för att skala ut skaluppsättning:
   ```powershell
   
    ##### Scale out the AppService Role instances ######
   
    # Set context to AzureStack admin.
    Add-AzureRmAccount -EnvironmentName AzureStackAdmin
                                                 
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
   > Det här steget kan ta flera timmar att slutföra beroende på vilken typ av roll och antalet instanser.
   >
   >

3. Övervaka status för de nya rollinstanserna i App Service Administration klickar du på om du vill kontrollera status för en enskild rollinstans rolltypen i listan.

## <a name="add-additional-workers-directly-within-the-app-service-resource-provider-admin"></a>Lägg till ytterligare arbetare direkt i App Service-administratören Resource Provider.

1. Logga in på Azure Stack-administrationsportalen som tjänstadministratör.

2. Bläddra till **Apptjänster**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Klicka på **roller**. Här kan du se uppdelning av alla roller för App Service som distribueras.

4. Högerklicka på raden i vilken du vill skala och klicka sedan på **ScaleSet**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Klicka på **skalning**, Välj antalet instanser som du vill skala till och klicka sedan på **spara**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. Apptjänst Azure stacken ska nu lägga till ytterligare virtuella datorer, konfigurera dem, installera nödvändig programvara och markeras som redo när processen har slutförts. Den här processen kan ta ungefär 80 minuter.

7. Du kan övervaka förloppet för av nya roller genom att visa arbetare i den **roller** bladet.

## <a name="result"></a>Resultat

Arbetare blir tillgängliga för användare att distribuera arbetsbelastningen till dem när de är helt distribuerade och redo. Nedan visas ett exempel på tillgängliga flera prisnivåer som standard. Om det finns inga tillgängliga arbetare för en viss worker-nivå, är möjlighet att välja motsvarande prisnivån inte tillgänglig.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Om du vill skala ut Management lägga klientdelen eller utgivare roller till utskalningsdistribution motsvarande rolltyp av. 
>
>

Om du vill skala ut Management, klientdelen eller utgivare roller följer du samma steg att välja rätt roll. Domänkontrollanter distribueras inte som Skaluppsättningar och därför två bör distribueras vid installationen för alla Produktionsdistribution.

### <a name="next-steps"></a>Nästa steg

[Konfigurera distributionskällor](azure-stack-app-service-configure-deployment-sources.md)
