---
title: 'Snabbstart: Skapa ett Azure Data Explorer-kluster och en databas med CLI'
description: I den här snabbstarten lär du dig att skapa ett Azure Data Explorer-kluster och en databas med Azure CLI
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: quickstart
ms.date: 2/4/2019
ms.openlocfilehash: 9e0ae547df34594674dc03702310a1537717a4ed
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881124"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-using-cli"></a>Skapa ett Azure Data Explorer-kluster och en databas med CLI

Snabbstarten beskriver hur du skapar ett Azure Data Explorer-kluster och en databas med Azure CLI.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver en Azure-prenumeration för att kunna utföra den här snabbstarten. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda Azure CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att kontrollera vilken version du har. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="configure-the-cli-parameters"></a>Konfigurera CLI-parametrarna

Följande steg krävs inte om du kör kommandon i Cloud Shell. Om du kör CLI lokalt måste du utföra följande steg för att logga in i Azure och ange din aktuella prenumeration:

1. Kör följande kommandon för att logga in på Azure:

    ```azurecli-interactive
    az login
    ```

2. Konfigurera prenumerationen där du vill att klustret ska skapas. Ersätt `MyAzureSub` med namnet på den Azure-prenumeration som du vill använda:

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Skapa Azure Data Explorer-klustret

1. Skapa klustret med följande kommando:

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**Inställning** | **Föreslaget värde** | **Fältbeskrivning**|
   |---|---|---|
   | namn | *azureclitest* | Önskat namn på klustret.|
   | sku | *D13_v2* | SKU:n som ska användas för klustret. |
   | resource-group | *testrg* | Namnet på resursgruppen där klustret kommer att skapas. |

    Det finns ytterligare parametrar som du kan använda, till exempel kapaciteten för klustret.

2. Kör följande kommando för att kontrollera om klustret har skapats:

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Om resultatet innehåller ”provisioningState” med värdet ”Succeeded” har klustret skapats.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Skapa databasen i Azure Data Explorer-klustret

1. Skapa databasen med följande kommando:

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
    ```

   |**Inställning** | **Föreslaget värde** | **Fältbeskrivning**|
   |---|---|---|
   | cluster-name | *azureclitest* | Namnet på klustret där databasen ska skapas.|
   | namn | *clidatabase* | Önskat namn på databasen.|
   | resource-group | *testrg* | Namnet på resursgruppen där klustret kommer att skapas. |
   | soft-delete-period | *3650:00:00:00* | Hur lång tid data ska behållas för att vara tillgänglig för frågor. |
   | hot-cache-period | *3650:00:00:00* | Hur länge data ska behållas i cacheminnet. |

2. Kör följande kommando för att se databasen som du skapade:

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Nu har du ett kluster och en databas.

## <a name="clean-up-resources"></a>Rensa resurser

* Om du planerar att följa våra andra snabbstarter och självstudier kan du spara alla resurser som du skapade.
* Ta bort klustret om du vill rensa resurser. När du tar bort ett kluster, raderas också alla databaser i den. Använd kommandot nedan för att ta bort klustret:

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabbstart: Mata in data med hjälp av Python-biblioteket](python-ingest-data.md) i Azure Data Explorer
