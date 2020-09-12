---
title: Starta, stoppa och ta bort ditt Azure våren Cloud-program | Microsoft Docs
description: Starta, stoppa och ta bort ditt Azure våren Cloud-program
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 17dd83ba7df469e12e0569e670d71b9068bb2a9f
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297526"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Starta, stoppa och ta bort ditt Azure våren Cloud-program

I den här guiden beskrivs hur du ändrar ett programs tillstånd i Azure våren-molnet med hjälp av antingen Azure Portal eller Azure CLI.

## <a name="using-the-azure-portal"></a>Använda Azure Portal

När du har distribuerat ett program kan du starta, stoppa och ta bort det med hjälp av Azure Portal.

1. Gå till din Azure våren Cloud Service-instans i Azure Portal.
1. Välj fliken **instrument panel för program** .
1. Välj det program vars tillstånd du vill ändra.
1. På sidan **Översikt** för programmet väljer du **Starta/stoppa**, **starta om**eller **ta bort**.

## <a name="using-the-azure-cli"></a>Använda Azure CLI

> [!NOTE]
> Du kan använda valfria parametrar och konfigurera standardvärden med Azure CLI. Läs mer om Azure CLI genom att läsa [vår referens dokumentation](/cli/azure/ext/spring-cloud/spring-cloud?view=azure-cli-latest).  

Installera först moln tillägget Azure våren för Azure CLI enligt följande:

```azurecli
az extension add --name spring-cloud
```

Välj sedan någon av följande Azure CLI-åtgärder:

* Starta programmet:

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Så här stoppar du ditt program:

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Starta om programmet:

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Ta bort ditt program:

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
