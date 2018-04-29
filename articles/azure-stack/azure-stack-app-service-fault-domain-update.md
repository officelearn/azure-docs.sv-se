---
title: 'Apptjänst Azure stacken: Fault-domänen | Microsoft Docs'
description: Hur du distribuerar Azure App Service på Azure-stacken över feldomäner
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: anwestg
ms.openlocfilehash: 42adef66fb1b1141ab44aab3a1ccdaae022202b5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>Hur du distribuerar Azure App Service på Azure-stacken över feldomäner

*Gäller för: Azure Stack integrerat system*

Med uppdateringen 1802 stöder Azure stacken nu distribution av arbetsbelastningar över feldomäner, en funktion som är viktiga för hög tillgänglighet.

> [!IMPORTANT]
> Du måste har uppdaterat Azure Stack integrerade systemet 1802 för att kunna dra nytta av stöd för fel-domäner.  Det här dokumentet gäller bara för Apptjänst resource provider distributioner som har slutförts innan 1802 uppdateringen.  Om du har distribuerat Apptjänst Azure stacken när 1802 uppdatering har installerats på Azure-stacken, fördelade resursprovidern redan över feldomäner.
>
>

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>Balansera en Apptjänst-resursprovidern över feldomäner

Du måste utföra följande steg för varje skaluppsättning för att kunna distribuera skaluppsättningar distribuerat för resursprovidern Apptjänst.  Som standard är scaleset namn:

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

> [!NOTE]
> Om du har inga instanser distribuerade i några av worker-nivå skaluppsättningar behöver du inte balansera dessa skaluppsättningar.  Skala anger balanseras korrekt när du skala ut dem i framtiden.
>
>

1. Bläddra till Skaluppsättningar för virtuell dator i Azure Portal Stack-administratör.  Befintliga skaluppsättningar distribueras som en del av Apptjänst-distributionen visas med information om processinstans count.

    ![Azure App Service Skaluppsättningar som listas i virtuell dator skala anger UX][1]

2. Nästa skala ut varje uppsättning.  Till exempel om du har tre befintliga instanser i skaluppsättning måste du skalar upp till 6 så att de tre nya instanserna som ska etableras över feldomäner.
    a. [Konfigurera Azure Stack Admin-miljö i PowerShell](azure-stack-powershell-configure-admin.md) b. Använd det här exemplet för att skala ut skaluppsättning:
        ```powershell
                Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

                # Get current scale set
                $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

                # Set and update the capacity of your scale set
                $vmss.sku.capacity = 6
                Update-AzureRmVmss -ResourceGroupName "AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
        '''
> [!NOTE]
> Det här steget kan ta flera timmar att slutföra beroende på vilken typ av roll och antalet instanser.
>
>

3. Övervaka status för nya rollinstanserna i bladet App Service Administration roller.  Kontrollera status för en enskild rollinstans genom att klicka på vilken roll i listan

    ![Azure App Service på Azure-stacken roller][2]

4. En ny instanserna är i ett **klar** tillstånd, gå tillbaka till bladet Skaluppsättning för virtuell dator och **ta bort** gamla instanser.

5. Upprepa dessa steg för **varje** skaluppsättning för virtuell dator.

## <a name="next-steps"></a>Nästa steg

Du kan också prova att använda andra [plattform som en tjänst (PaaS) services](azure-stack-tools-paas-services.md).

* [SQL Server-resursprovidern](azure-stack-sql-resource-provider-deploy.md)
* [MySQL-resursprovidern](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
