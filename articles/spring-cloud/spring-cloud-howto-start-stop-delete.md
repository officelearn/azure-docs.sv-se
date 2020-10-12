---
title: Starta, stoppa och ta bort ditt Azure våren Cloud-program | Microsoft Docs
description: Starta, stoppa och ta bort ditt Azure våren Cloud-program
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 479daa9a94247ada30c54a9c5df3471035765087
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90908277"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Starta, stoppa och ta bort ditt Azure våren Cloud-program

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

I den här guiden beskrivs hur du ändrar ett programs tillstånd i Azure våren-molnet med hjälp av antingen Azure Portal eller Azure CLI.

## <a name="using-the-azure-portal"></a>Använda Azure Portal

När du har distribuerat ett program kan du starta, stoppa och ta bort det med hjälp av Azure Portal.

1. Gå till din Azure våren Cloud Service-instans i Azure Portal.
1. Välj fliken **instrument panel för program** .
1. Välj det program vars tillstånd du vill ändra.
1. På sidan **Översikt** för programmet väljer du **Starta/stoppa**, **starta om**eller **ta bort**.

## <a name="using-the-azure-cli"></a>Använda Azure CLI

> [!NOTE]
> Du kan använda valfria parametrar och konfigurera standardvärden med Azure CLI. Läs mer om Azure CLI genom att läsa [vår referens dokumentation](/cli/azure/ext/spring-cloud/spring-cloud?view=azure-cli-latest&preserve-view=true).  

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
