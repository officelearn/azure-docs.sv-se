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
ms.date: 06/29/2018
ms.author: anwestg
ms.openlocfilehash: ce57e153dcab6a386150ebefe1ecb4a018514247
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130378"
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>Hur du distribuerar Azure App Service på Azure-stacken över feldomäner

*Gäller för: Azure Stack integrerat system*

Med uppdateringen 1802 stöder Azure stacken nu distribution av arbetsbelastningar över feldomäner, en funktion som är viktiga för hög tillgänglighet.

>[!IMPORTANT]
>Om du vill dra nytta av domänstöd för fel, måste du uppdatera din Azure-stacken integrerat system till 1802. Det här dokumentet gäller bara för Apptjänst resource provider distributioner som har slutförts innan 1802 uppdateringen. Om du har distribuerat Apptjänst Azure stacken när 1802 uppdatering har installerats på Azure-stacken är redan resursprovidern fördelad över feldomäner.

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>Balansera en Apptjänst-resursprovidern över feldomäner

Du måste utföra stegen i den här artikeln för varje skaluppsättning för att distribuera skaluppsättningar distribuerat för resursprovidern Apptjänst. Som standard är scaleset namn:

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

>[!NOTE]
> Om du inte har instanser distribuerade i några av worker-nivå skalningsuppsättningar, behöver du inte balansera dessa skaluppsättningar. Skala anger balanseras korrekt när du skala ut dem i framtiden.

Följ dessa steg om du vill skala ut skala anger:

1. Logga in på Azure Stack-Administratörsportalen.
2. Välj **fler tjänster**.
3. Välj under beräkning, **skalningsuppsättningar i virtuella**. Befintliga skaluppsättningar distribueras som en del av Apptjänst-distributionen visas med information om processinstans count. Följande skärmbild visar ett exempel på skaluppsättningar.

      ![Azure App Service Skaluppsättningar som listas i virtuell dator skala anger UX][1]

4. Skala ut varje uppsättning. Till exempel om du har tre befintliga instanser i skaluppsättning måste du skalar upp till 6 så att de tre nya instanserna distribueras över feldomäner. Följande PowerShell-exempel som visar ut för att skala ut skaluppsättning.

   ```powershell
   Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

   # Get current scale set
   $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

   # Set and update the capacity of your scale set
   $vmss.sku.capacity = 6
   Update-AzureRmVmss -ResourceGroupName AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
   ```

   >[!NOTE]
   >Det här steget kan ta flera timmar att slutföra beroende på typ av roll och antalet instanser.

5. I **App Service Administration roller**, övervaka status för de nya rollinstanserna. Om du vill kontrollera status för en rollinstans, väljer du vilken roll i listan

    ![Azure App Service på Azure-stacken roller][2]

6. När statusen för de nya rollinstanserna är **klar**, gå tillbaka till **Skaluppsättning för virtuell dator** och **ta bort** gamla rollinstanser.

7. Upprepa dessa steg för **varje** skaluppsättning för virtuell dator.

## <a name="next-steps"></a>Nästa steg

Du kan också prova att använda andra [plattform som en tjänst (PaaS) services](azure-stack-tools-paas-services.md).

* [SQL Server-resursprovidern](azure-stack-sql-resource-provider-deploy.md)
* [MySQL-resursprovidern](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
