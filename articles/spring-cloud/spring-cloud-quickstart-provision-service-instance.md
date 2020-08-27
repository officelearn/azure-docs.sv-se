---
title: Snabb start – etablera moln tjänsten Azure våren
description: Beskriver hur du skapar en Azure våren Cloud-tjänstinstans för att distribuera appar.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: c91237e3a14c60e477f58be0bf62f634b462960b
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951920"
---
# <a name="quickstart-provision-azure-spring-cloud-service"></a>Snabb start: etablera Azure våren Cloud service

Du kan skapa ett Azure våren-moln med hjälp av Azure Portal eller Azure CLI.  Båda metoderna beskrivs i följande procedurer.
## <a name="prerequisites"></a>Förutsättningar

* [Installera JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Registrera dig för en Azure-prenumeration](https://azure.microsoft.com/free/)
* Valfritt [Installera Azure CLI-versionen 2.0.67 eller högre](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) och installera Azure våren Cloud-tillägget med kommandot: `az extension add --name spring-cloud`
* Valfritt [Installera Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) och [inloggning](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="provision-an-instance-of-azure-spring-cloud"></a>Etablera en instans av Azure våren Cloud

#### <a name="portal"></a>[Portal](#tab/Azure-portal)

Följande procedur skapar en instans av Azure våren Cloud med hjälp av Azure Portal.

1. Öppna [Azure Portal](https://ms.portal.azure.com/)på en ny flik. 

2. Sök efter **Azure våren Cloud**i den översta sökrutan.

3. Välj **Azure våren Cloud** från resultaten.

    ![ASC-ikon start](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. På sidan Azure våren Cloud klickar du på **+ Lägg till**.

    ![ASC-ikon Lägg till](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Fyll i formuläret på sidan Azure våren Cloud **create** .  Beakta följande rikt linjer:
    - **Prenumeration**: Välj den prenumeration som du vill fakturera för den här resursen.
    - **Resurs grupp**: skapa nya resurs grupper för nya resurser är en bra metod. Observera att detta kommer att användas i senare steg som **\<resource group name\>** .
    - **Tjänst information/namn**: ange **\<service instance name\>** .  Namnet måste vara mellan 4 och 32 tecken långt och får bara innehålla gemena bokstäver, siffror och bindestreck.  Det första tecknet i tjänst namnet måste vara en bokstav och det sista tecknet måste vara en bokstav eller en siffra.
    - **Plats**: Välj platsen för din tjänst instans.

    ![ASC-portalen börjar](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Klicka på **Granska och skapa**.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

I följande procedur används Azure CLI-tillägget för att etablera en instans av Azure våren Cloud.

1. Logga in på Azure CLI och välj din aktiva prenumeration. Se till att välja den aktiva prenumeration som är vit listas för Azure våren Cloud

    ```azurecli
    az login
    az account list -o table
    az account set --subscription <Name or ID of subscription, skip if you only have 1 subscription>
    ```

1. Förbered ett namn för din Azure våren Cloud-tjänst.  Namnet måste vara mellan 4 och 32 tecken långt och får bara innehålla gemena bokstäver, siffror och bindestreck.  Det första tecknet i tjänst namnet måste vara en bokstav och det sista tecknet måste vara en bokstav eller en siffra.

1. Skapa en resurs grupp som innehåller din Azure våren Cloud-tjänst.

    ```azurecli
    az group create --location eastus --name <resource group name>
    ```

    Lär dig mer om [Azures resurs grupper](../azure-resource-manager/management/overview.md).

1. Öppna ett Azure CLI-fönster och kör följande kommandon för att etablera en instans av Azure våren Cloud.

    ```azurecli
    az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    Tjänst instansen tar cirka fem minuter att distribuera.
---

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Konfigurera konfigurations Server](spring-cloud-quickstart-setup-config-server.md)


