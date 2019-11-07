---
title: Starta, stoppa och ta bort ditt Azure våren Cloud-program | Microsoft Docs
description: Starta, stoppa och ta bort ditt Azure våren Cloud-program
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 9f537ab425428728137e04713e434d8dc09e065a
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607726"
---
# <a name="how-to-start-stop-and-delete-your-azure-spring-cloud-application"></a>Starta, stoppa och ta bort ditt Azure våren Cloud-program

Den här guiden förklarar hur du ändrar status för ett program i Azure våren-molnet med hjälp av antingen Azure Portal eller CLI.

## <a name="using-the-azure-portal"></a>Använda Azure Portal

När du har distribuerat ett program kan du **Starta**, **stoppa**och **ta bort** det med hjälp av Azure Portal.

1. Gå till din Azure våren Cloud Service-instans i Azure Portal.
1. Välj fliken **instrument panel för program** .
1. Välj det program vars tillstånd du vill ändra.
2. På sidan **Översikt** för programmet hittar du knappar för att **Starta/stoppa**, **starta om**och **ta bort** programmet.

## <a name="using-the-azure-cli"></a>Använda Azure CLI

> [!NOTE]
> Du kan använda valfria parametrar och konfigurera standardvärden med Azure CLI. Läs mer om i vår [referens dokumentation](spring-cloud-cli-reference.md).  

Installera våren Cloud-tillägget för Azure CLI:

```azurecli
az extension add --name spring-cloud
```

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
