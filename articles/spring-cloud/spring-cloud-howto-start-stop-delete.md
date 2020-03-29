---
title: Starta, stoppa och ta bort ditt Azure Spring Cloud-program | Microsoft-dokument
description: Så här startar, stoppar och tar du bort ditt Azure Spring Cloud-program
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: daa549e248668add54530e90174134c4e0059b3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276828"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Starta, stoppa och ta bort ditt Azure Spring Cloud-program

Den här guiden förklarar hur du ändrar ett programs tillstånd i Azure Spring Cloud med hjälp av antingen Azure-portalen eller Azure CLI.

## <a name="using-the-azure-portal"></a>Använda Azure Portal

När du har distribuerat ett program kan du starta, stoppa och ta bort det med hjälp av Azure-portalen.

1. Gå till din Azure Spring Cloud-tjänstinstans i Azure-portalen.
1. Välj fliken **Instrumentpanel för program.**
1. Markera det program vars tillstånd du vill ändra.
1. På sidan **Översikt** för det programmet väljer du **Start/Stopp,** **Starta om**eller Ta **bort**.

## <a name="using-the-azure-cli"></a>Använda Azure CLI

> [!NOTE]
> Du kan använda valfria parametrar och konfigurera standardvärden med Azure CLI. Läs mer om Azure CLI genom att läsa [vår referensdokumentation](spring-cloud-cli-reference.md).  

Installera först Azure Spring Cloud-tillägget för Azure CLI enligt följande:

```azurecli
az extension add --name spring-cloud
```

Välj sedan någon av dessa Azure CLI-åtgärder:

* Så här startar du programmet:

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Så här stoppar du programmet:

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Så här startar du om programmet:

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Så här tar du bort programmet:

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
