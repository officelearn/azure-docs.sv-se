---
title: Snabb start – skapa en Azure Databricks arbets yta med PowerShell
description: Den här snabb starten visar hur du använder PowerShell för att skapa en Azure Databricks-arbetsyta.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 06/02/2020
ms.custom: mvc
ms.openlocfilehash: 445832119a113f92dc1da51eb5b5e77dff176fa3
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485732"
---
# <a name="quickstart-create-an-azure-databricks-workspace-using-powershell"></a>Snabb start: skapa en Azure Databricks-arbetsyta med hjälp av PowerShell

I den här snabb starten beskrivs hur du använder PowerShell för att skapa en Azure Databricks-arbetsyta. Du kan använda PowerShell för att skapa och hantera Azure-resurser interaktivt eller i skript.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt](https://azure.microsoft.com/free/) konto innan du börjar.

Om du väljer att använda PowerShell lokalt kräver den här artikeln att du installerar AZ PowerShell-modulen och ansluter till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) . Mer information om hur du installerar AZ PowerShell-modulen finns i [installera Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Även om PowerShell-modulen AZ. Databricks är i för hands version måste du installera den separat från AZ PowerShell-modulen med hjälp av följande kommando: `Install-Module -Name Az.Databricks -AllowPrerelease` .
> När AZ. Databricks PowerShell-modulen är allmänt tillgänglig blir den en del av framtida versioner av AZ PowerShell-modulen som är tillgängliga internt inifrån Azure Cloud Shell.

Om det här är första gången du använder Azure Databricks måste du registrera Resource-providern för **Microsoft. Databricks** .

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Databricks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du har flera Azure-prenumerationer väljer du lämplig prenumeration där resurserna ska faktureras. Välj ett angivet prenumerations-ID med cmdleten [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resurs grupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) med cmdlet: en [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp.

I följande exempel skapas en resurs grupp med namnet **myresourcegroup** i regionen **USA, västra 2** .

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus2
```

## <a name="create-an-azure-databricks-workspace"></a>Skapa en Azure Databricks-arbetsyta

I det här avsnittet skapar du en Azure Databricks-arbetsyta med hjälp av PowerShell.

```azurepowershell-interactive
New-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup -Location westus2 -ManagedResourceGroupName databricks-group -Sku standard
```

Ange följande värden:

|       **Egenskap**       |                                                                                **Beskrivning**                                                                                 |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Namn                     | Ange ett namn för Databricks-arbetsytan                                                                                                                                   |
| ResourceGroupName        | Ange ett befintligt resurs grupp namn                                                                                                                                        |
| Location                 | Välj **USA, västra 2**. För andra tillgängliga regioner, se [Azure-tjänster tillgängliga per region](https://azure.microsoft.com/regions/services/)                                     |
| ManagedResourceGroupName | Ange om du vill skapa en ny hanterad resurs grupp eller Använd en befintlig.                                                                                        |
| Sku                      | Välj mellan **standard**, **Premium**eller **utvärdering**. Mer information om dessa nivåer finns i Databricks- [priser](https://azure.microsoft.com/pricing/details/databricks/) |

Det tar några minuter att skapa arbetsytan. När den här processen är färdig läggs ditt användar konto automatiskt till som administratörs användare i arbets ytan.

När en distribution av arbets ytan Miss lyckas skapas arbets ytan fortfarande i ett felaktigt tillstånd. Ta bort den misslyckade arbets ytan och skapa en ny arbets yta som löser distributions felen. När du tar bort den misslyckade arbets ytan raderas även den hanterade resurs gruppen och alla resurser som har distribuerats.

## <a name="determine-the-provisioning-state-of-a-databricks-workspace"></a>Fastställa etablerings statusen för en Databricks-arbetsyta

Du kan använda cmdleten för att avgöra om en Databricks-arbetsyta har skapats `Get-AzDatabricksWorkspace` .

```azurepowershell-interactive
Get-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup |
  Select-Object -Property Name, SkuName, Location, ProvisioningState
```

```Output
Name            SkuName   Location  ProvisioningState
----            -------   --------  -----------------
mydatabricksws  standard  westus2   Succeeded
```

## <a name="clean-up-resources"></a>Rensa resurser

Om resurserna som skapas i den här snabb starten inte behövs för en annan snabb start eller självstudier kan du ta bort dem genom att köra följande exempel.

> [!CAUTION]
> I följande exempel tas den angivna resurs gruppen och alla resurser som ingår i den bort.
> Om resurser utanför omfånget för den här snabb starten finns i den angivna resurs gruppen kommer de också att tas bort.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Om du bara vill ta bort den server som skapats i den här snabb starten utan att ta bort resurs gruppen använder du `Remove-AzDatabricksWorkspace` cmdleten.

```azurepowershell-interactive
Remove-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa ett Spark-kluster i Databricks](quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks)
