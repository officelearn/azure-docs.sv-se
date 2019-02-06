---
title: Skapa ett Azure Data Explorer-kluster och en databas med CLI
description: Den här artikeln beskriver hur du skapar ett Azure Data Explorer-kluster och en databas med hjälp av Azure CLI
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: howto
ms.date: 1/31/2019
ms.openlocfilehash: 8c035524adebcb131872c700280201aaac07c52b
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747971"
---
# <a name="create-an-azure-data-explorer-cluster-and-a-database-using-cli"></a>Skapa ett Azure Data Explorer-kluster och en databas med CLI

Den här artikeln beskriver hur du skapar ett Azure Data Explorer-kluster och en databas med hjälp av Azure CLI.

## <a name="whats-the-difference-between-the-management-plane-and-data-plane-apis"></a>Vad är skillnaden mellan Hanteringsplanet och dataplanet API: er

Det finns två olika API-bibliotek, hantering och dataplaner API: er.
Management-API: er används för att hantera resurser, till exempel skapa ett kluster, skapa en databas, ta bort en dataanslutning, ändra antalet instanser antal osv. Dataplanet API: er används för att interagera med data, köra frågor, mata in data osv.

## <a name="configure-the-cli-parameters"></a>Konfigurera CLI-parametrar

Logga in på ditt konto

```Bash
az login
```

Ange prenumerationen där du vill ska ingå i klustret som ska skapas.

```Bash
az account set --subscription "your_subscription"
```

## <a name="create-the-azure-data-explorer-cluster"></a>Skapa Azure Data Explorer-kluster

Skapa klustret med följande kommando.

```Bash
az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
```

Ange följande värden

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | namn | *azureclitest* | Önskat namn på klustret.|
    | sku | *D13_v2* | SKU: N som ska användas för klustret. |
    | resource-group | *testrg* | Resursgruppens namn där klustret skapas. |
    | | |

Om du vill, finns det flera valfria parametrar som du kan använda, till exempel kapaciteten för klustret osv.

Om du vill kontrollera om klustret har skapats, kan du köra

```Bash
az kusto cluster show --name azureclitest --resource-group testrg
```

Om resultatet innehåller ”provisioningState” med värdet ”Succeeded”, det innebär att klustret har skapats.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Skapa en databas i Azure Data Explorer-kluster

Skapa en databas med hjälp av följande kommando.

```Bash
az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
```

Ange följande värden

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | cluster-name | *azureclitest* | Namnet på klustret där den ska skapas.|
    | namn | *clidatabase* | Önskat namn för din databas.|
    | resource-group | *testrg* | Resursgruppens namn där klustret skapas. |
    | soft-delete-period | *3650:00:00:00* | Hur länge data bör hållas så att den är tillgängliga för frågor. |
    | hot-cache-period | *3650:00:00:00* | Hur länge data ska behållas i cachen. |
    | | |

Du kan se den databas du skapade genom att köra

```Bash
az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
```

Det är allt som nu har du ett kluster och en databas.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att följa våra andra snabbstarter och självstudier kan du spara alla resurser som du skapade.

När du tar bort ett kluster, raderas också alla databaser i den. Använd den nedan kommando för att ta bort ditt kluster.

```Bash
az kusto cluster delete --name azureclitest --resource-group testrg
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabbstart: Mata in data från Event Hub i Azure Data Explorer](ingest-data-event-hub.md)
