---
title: 'App Service i Azure Stack: Fel domän Update | Microsoft Docs'
description: Hur du distribuera om Azure App Service i Azure Stack via feldomäner
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
ms.date: 09/05/2018
ms.author: anwestg
ms.lastreviewed: 09/05/2018
ms.openlocfilehash: 249e4b466e9be567111aaa22b40ca3e5dadb6cac
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246389"
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>Hur du distribuera om Azure App Service i Azure Stack via feldomäner

*Gäller för: Integrerade Azure Stack-system*

Med den 1802 uppdateringen stöder Azure Stack nu distribution av arbetsbelastningar över feldomäner, en funktion som är viktiga för hög tillgänglighet.

>[!IMPORTANT]
>Om du vill dra nytta av domänstöd för fel, måste du uppdatera integrerade Azure Stack-system till 1802. Det här dokumentet gäller endast för App Service resource provider-distributioner som har slutförts innan du 1802 uppdateringen. Om du har distribuerat App Service i Azure Stack när 1802 uppdateringen har tillämpats på Azure Stack resursprovidern redan har distribuerats över feldomäner.

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>Balansera om en App Service-resursprovider över feldomäner

Du måste utföra stegen i den här artikeln för varje skalningsuppsättning för att distribuera om skalningsuppsättningar distribueras för App Service-resursprovidern. Som standard är de skala namn:

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

>[!NOTE]
> Om du inte har distribuerade instanser i några av skalningsuppsättningar för worker-nivå, behöver du inte balansera om dessa skalningsuppsättningar. Vilka skalningsuppsättningar balanseras korrekt när du skalar ut dem i framtiden.

Följ dessa steg om du vill skala ut vilka skalningsuppsättningar:

1. Logga in på Azure Stack-Administratörsportalen.
1. Välj **Alla tjänster**.
2. I den **COMPUTE** kategori, väljer **VM-skalningsuppsättningar**. Befintliga skalningsuppsättningar distribueras som en del av App Service-distributionen visas med information om processinstans antal. Följande skärmdump visar ett exempel på skalningsuppsättningar.

      ![Azure App Service Skalningsuppsättningar som anges i Virtual Machine Scale Sets UX][1]

1. Skala ut varje uppsättning. Till exempel om du har tre befintliga instanser i skalningsuppsättningen skalar du ut till 6 så att de tre nya instanserna distribueras över feldomäner. Följande PowerShell-exempel som visar dig för att skala ut skalningsuppsättningen.

   ```powershell
   Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

   # Get current scale set
   $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

   # Set and update the capacity of your scale set
   $vmss.sku.capacity = 6
   Update-AzureRmVmss -ResourceGroupName AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
   ```

   >[!NOTE]
   >Det här steget kan ta flera timmar att slutföra beroende på typ av roll och hur många instanser.

1. I **App Service-administratörsroller**, övervaka status för de nya rollinstanserna. Du kan kontrollera status för en rollinstans kan du välja vilken roll i listan

    ![Azure App Service i Azure Stack-roller][2]

1. När statusen för de nya rollinstanserna är **redo**, går tillbaka till **Virtual Machine Scale Sets** och **ta bort** gamla rollinstanserna.

1. Upprepa dessa steg för **varje** virtual machine scale Sets.

## <a name="next-steps"></a>Nästa steg

Du kan också prova att använda andra [plattform som en tjänst (PaaS) tjänster](azure-stack-tools-paas-services.md).

* [SQL Server-resursleverantör](azure-stack-sql-resource-provider-deploy.md)
* [MySQL-resursprovider](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
