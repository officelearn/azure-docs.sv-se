---
title: Skapa ett Azure Datautforskaren kluster & DB med PowerShell
description: Lär dig hur du skapar ett Azure Datautforskaren-kluster och-databas med hjälp av PowerShell
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 690c3e281e65f54f240c70f7a6e5038f54102c99
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560600"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Skapa ett Azure Datautforskaren-kluster och-databas med hjälp av PowerShell

> [!div class="op_single_selector"]
> * [Portalen](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM-mall](create-cluster-database-resource-manager.md)  

Azure Data Explorer är en snabb, fullständigt hanterad dataanalystjänst för realtidsanalys av stora mängder data som strömmar från program, webbplatser, IoT-enheter med mera. För att använda Azure Data Explorer skapar du först ett kluster och skapar en eller flera databaser i klustret. Sedan matar du in (läser in) data i databasen så att du kan köra frågor mot den. I den här artikeln skapar du ett kluster och en databas med hjälp av PowerShell. Du kan köra PowerShell-cmdlets och skript i Windows, Linux eller i [Azure Cloud Shell](../cloud-shell/overview.md) med [AZ. Kusto](/powershell/module/az.kusto/?view=azps-1.4.0#kusto) för att skapa och konfigurera Azure datautforskaren-kluster och-databaser.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda Azure CLI lokalt, kräver den här artikeln Azure CLI version 2.0.4 eller senare. Kör `az --version` för att kontrollera vilken version du har. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-parameters"></a>Konfigurera parametrar

Följande steg krävs inte om du kör kommandon i Azure Cloud Shell. Om du kör CLI lokalt följer du steg 1 & 2 för att logga in på Azure och ange din aktuella prenumeration:

1. Kör följande kommandon för att logga in på Azure:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Ange den prenumeration där du vill att klustret ska skapas:

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
1. När du kör Azure CLI lokalt eller i Azure Cloud Shell måste du installera modulen AZ. Kusto på din enhet:

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
    Get-AzKustoCluster -Name mykustocluster -ResourceGroupName testrg
    ```

Om resultatet innehåller `provisioningState` med värdet `Succeeded` har klustret skapats.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Skapa databasen i Azure Data Explorer-klustret

1. Skapa databasen med hjälp av följande kommando:

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**Inställning** | **Föreslaget värde** | **Fältbeskrivning**|
   |---|---|---|
   | ClusterName | *mykustocluster* | Namnet på det kluster där databasen ska skapas.|
   | Namn | *mykustodatabase* | Namn på databasen.|
   | ResourceGroupName | *testrg* | Namnet på resursgruppen där klustret kommer att skapas. |
   | SoftDeletePeriod | *3650:00:00:00* | Hur lång tid data ska behållas för att vara tillgängliga för frågor. |
   | HotCachePeriod | *3650:00:00:00* | Hur lång tid data ska behållas i cache. |

1. Kör följande kommando för att se den databas som du skapade:

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster -ResourceGroupName testrg -Name mykustodatabase
    ```

Nu har du ett kluster och en databas.

## <a name="clean-up-resources"></a>Rensa resurser

* Behåll de resurser du har skapat om du planerar att följa våra andra artiklar.
* Ta bort klustret om du vill rensa resurser. När du tar bort ett kluster, raderas också alla databaser i den. Använd följande kommando för att ta bort klustret:

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>Nästa steg

* [Ytterligare AZ. Kusto-kommandon](/powershell/module/az.kusto/?view=azps-1.7.0#kusto)
* [Mata in data med Azure Datautforskaren .NET standard SDK (för hands version)](net-standard-ingest-data.md)
