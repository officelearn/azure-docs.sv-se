---
title: Starta, stoppa och ta bort ditt Azure våren Cloud-program | Microsoft Docs
description: Starta, stoppa och ta bort ditt Azure våren Cloud-program
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: f7f76644d13c20704d2c3bd908176ac452df2a20
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039086"
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

* Starta programmet:
    ```Azure CLI
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Så här stoppar du ditt program:
    ```Azure CLI
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Starta om programmet:
    ```Azure CLI
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Ta bort ditt program:
    ```Azure CLI
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
