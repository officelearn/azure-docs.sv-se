---
title: 'Snabbstart: Skapa ett Azure Data Explorer-kluster och en databas med hjälp av PowerShell'
description: Lär dig hur du skapar ett Azure Data Explorer-kluster och en databas med hjälp av PowerShell
services: data-explorer
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/17/2019
ms.openlocfilehash: 650bdc5cdf99645bc2be6c8e85737dacd10a6b27
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287538"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Skapa ett Azure Data Explorer-kluster och en databas med hjälp av PowerShell

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  


Den här snabbstarten beskriver hur du skapar ett Azure Data Explorer-kluster och en databas med hjälp av PowerShell.

Du kan köra PowerShell-cmdlets och skript på Windows, Linux, eller i [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) att skapa och konfigurera [Azure Data Explorer](https://docs.microsoft.com/azure/kusto/ ).

The [**Az.Kusto**](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto ). Med Azure PowerShell och **Az.Kusto**, du kan utföra följande uppgifter:

## <a name="prerequisites"></a>Förutsättningar

Du behöver en Azure-prenumeration för att kunna utföra den här snabbstarten. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="configure-parameters"></a>Konfigurera parametrar

Följande steg krävs inte om du kör kommandon i Azure Cloud Shell. Om du kör CLI lokalt följer du de här stegen för att logga in i Azure och ange din aktuella prenumeration:

1. Kör följande kommandon för att logga in på Azure:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Konfigurera den prenumeration där du vill att klustret ska skapas.

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
3. Installera Az.Kusto-modulen på din enhet:
    
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

2. Kör följande kommando för att kontrollera om klustret har skapats:

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

2. Kör följande kommando för att se den databas som du skapade:

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

Du hittar fler Az.Kusto kommandon [ **här**](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto )

> [!div class="nextstepaction"]
> [Snabbstart: Mata in data med hjälp av Azure Data Explorer .NET Standard SDK (förhandsversion)](net-standard-ingest-data.md)
