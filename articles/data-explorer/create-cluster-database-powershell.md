---
title: 'Snabbstart: Skapa ett Azure Data Explorer-kluster och en databas med hjälp av PowerShell'
description: Lär dig hur du skapar ett Azure Data Explorer-kluster och en databas med hjälp av PowerShell
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/25/2019
ms.openlocfilehash: 553564be494b4175cba937b583d49ad84a8d0e66
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526528"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Skapa ett Azure Data Explorer-kluster och en databas med hjälp av PowerShell

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Azure Data Explorer är en snabb, fullständigt hanterad dataanalystjänst för realtidsanalys av stora mängder data som strömmar från program, webbplatser, IoT-enheter med mera. För att använda Azure Data Explorer skapar du först ett kluster och skapar en eller flera databaser i klustret. Sedan matar du in (läser in) data i databasen så att du kan köra frågor mot den. I den här snabbstarten skapar du ett kluster och en databas med hjälp av Powershell. Du kan köra PowerShell-cmdlets och skript på Windows, Linux, eller i [Azure Cloud Shell](../cloud-shell/overview.md) med [Az.Kusto](/powershell/module/az.kusto/?view=azps-1.4.0#kusto) att skapa och konfigurera Azure Data Explorer-kluster och databaser.

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda Azure CLI kräver den här snabbstarten Azure CLI version 2.0.4 eller senare. Kör `az --version` för att kontrollera vilken version du har. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-parameters"></a>Konfigurera parametrar

Följande steg krävs inte om du kör kommandon i Azure Cloud Shell. Om du kör CLI lokalt måste du följa steg 1 och 2 logga in på Azure och ange din aktuella prenumeration:

1. Kör följande kommandon för att logga in på Azure:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Ange prenumerationen där du vill att klustret ska skapas:

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
1. När du kör Azure CLI lokalt eller i Azure Cloud Shell kan behöva du installera modulen Az.Kusto på din enhet:
    
    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto  
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Skapa Azure Data Explorer-klustret

1. Skapa klustret med hjälp av följande kommando:

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**Inställning** | **Föreslaget värde** | **Fältbeskrivning**|
   |---|---|---|
   | Namn | *mykustocluster* | Önskat namn på klustret.|
   | Sku | *D13_v2* | Den SKU som ska användas för klustret. |
   | ResourceGroupName | *testrg* | Namnet på resursgruppen där klustret kommer att skapas. |

    Det finns ytterligare parametrar som du kan använda, till exempel kapaciteten för klustret.

1. Kör följande kommando för att kontrollera om klustret har skapats:

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster --ResourceGroupName testrg
    ```

Om resultatet innehåller `provisioningState` med värdet `Succeeded` har klustret skapats.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Skapa databasen i Azure Data Explorer-klustret

1. Skapa databasen med hjälp av följande kommando:

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**Inställning** | **Föreslaget värde** | **Fältbeskrivning**|
   |---|---|---|
   | Klusternamn | *mykustocluster* | Namnet på det kluster där databasen ska skapas.|
   | Namn | *mykustodatabase* | Namn på databasen.|
   | ResourceGroupName | *testrg* | Namnet på resursgruppen där klustret kommer att skapas. |
   | SoftDeletePeriod | *3650:00:00:00* | Hur lång tid data ska behållas för att vara tillgängliga för frågor. |
   | HotCachePeriod | *3650:00:00:00* | Hur lång tid data ska behållas i cache. |

1. Kör följande kommando för att se den databas som du skapade:

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster --ResourceGroupName testrg -Name mykustodatabase
    ```

Nu har du ett kluster och en databas.

## <a name="clean-up-resources"></a>Rensa resurser

* Om du planerar att följa våra andra snabbstarter och självstudier kan du spara alla resurser som du skapade.
* Ta bort klustret om du vill rensa resurser. När du tar bort ett kluster, raderas också alla databaser i den. Använd följande kommando för att ta bort klustret:

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>Nästa steg

* [Ytterligare Az.Kusto kommandon](/powershell/module/az.kusto/?view=azps-1.7.0#kusto)
* [Snabbstart: Mata in data med hjälp av Azure Data Explorer .NET Standard SDK (förhandsversion)](net-standard-ingest-data.md)
