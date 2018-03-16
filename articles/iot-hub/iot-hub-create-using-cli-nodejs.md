---
title: Skapa en IoT-hubb med Azure CLI (azure.js) | Microsoft Docs
description: "Så här skapar du en Azure IoT-hubb med plattformsoberoende Azure CLI (azure.js)."
services: iot-hub
documentationcenter: .net
author: BeatriceOltean
manager: timlt
editor: 
ms.assetid: 46a17831-650c-41d9-b228-445c5bb423d3
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: boltean
ms.openlocfilehash: 7ad0efeeacbdb5dd0185c6336e9bf0e4d7937c99
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Skapa en IoT-hubb med hjälp av Azure CLI

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduktion

Du kan använda Azure CLI (azure.js) för att skapa och hantera Azure IoT-hubbar programmässigt. Den här artikeln visar hur du använder Azure CLI (azure.js) för att skapa en IoT-hubb.

Du kan slutföra uppgiften med någon av följande CLI-versioner:

* Azure CLI (azure.js) – CLI för klassisk och resurs management distributionsmodellerna som beskrivs i den här artikeln.
* [Azure CLI 2.0 (az.py)](iot-hub-create-using-cli.md) – nästa generations CLI för resursdistributionsmodell för hantering.

För att kunna genomföra den här kursen behöver du följande:

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.
* [Azure CLI 0.10.4] [ lnk-CLI-install] eller senare. Om du redan har installerat Azure CLI, kan du verifiera den aktuella versionen i Kommandotolken med följande kommando:

```azurecli
azure --version
```

> [!NOTE]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Azure Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md). Azure CLI måste vara i läget Azure Resource Manager:
>
> ```azurecli
> azure config mode arm
> ```

## <a name="set-your-azure-account-and-subscription"></a>Konfigurera Azure-kontot och Azure-prenumerationen

1. I Kommandotolken inloggningen genom att skriva följande kommando:

   ```azurecli
    azure login
   ```

   Använda föreslagna webbläsare och kod för att autentisera.
1. Om du har flera Azure-prenumerationer, ger ansluta till Azure åtkomst till alla de Azure-prenumerationer som är kopplade till dina autentiseringsuppgifter. Du kan visa Azure-prenumerationer och identifiera vilket som är standard, med hjälp av kommandot:

   ```azurecli
    azure account list
   ```

   Att ställa in den prenumerationskontext som du vill köra resten av kommandon användas:

   ```azurecli
    azure account set <subscription name>
   ```

1. Om du inte har en resursgrupp kan du skapa en med namnet **exampleResourceGroup**:

   ```azurecli
    azure group create -n exampleResourceGroup -l westus
   ```

> [!TIP]
> Artikeln [använda Azure CLI för att hantera Azure-resurser och resursgrupper] [ lnk-CLI-arm] innehåller mer information om hur du använder Azure CLI för att hantera Azure-resurser.

## <a name="create-an-iot-hub"></a>Skapa en IoT-hubb

Obligatoriska parametrar:

```azurecli
azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>
```

* **resursgruppens namn**. Resursgruppens namn. Formatet är skiftlägeskänsligt alfanumerisk, understreck och bindestreck, 1-64-längd.
* **name**. Namnet på IoT-hubben ska skapas. Formatet är skiftlägeskänsligt alfanumeriska och bindestreck, 3-50 längd.
* **Plats**. Platsen (azure region/datacenter) att etablera IoT-hubben.
* **sku-name**. Namnet på sku, en av: [F1, S1, S2, S3]. Den senaste fullständiga listan finns prissättningssidan för IoT-hubb.
* **enheter**. Antal allokerade enheter. -Intervall: F1 [1-1]: S1, S2 [1 200]: S3 [1-10]. IoT-hubbenheter baseras på din totala meddelandemängd och antalet enheter som du vill ansluta till.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Du kan använda kommandot help i Kommandotolken om du vill se alla parametrar som är tillgängligt för att skapa:

```azurecli
azure iothub create -h
```

Enkelt exempel: Om du vill skapa en IoT-hubb med namnet **exampleIoTHubName** i resursgruppen **exampleResourceGroup**, kör du följande kommando:

```azurecli
azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [!NOTE]
> Den här Azure CLI-kommando skapar en S1 Standard IoT-hubb du debiteras. Du kan ta bort IoT-hubben **exampleIoTHubName** med hjälp av följande kommando:
>
> ```azurecli
> azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
> ```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utvecklar för IoT-hubb finns i följande artikel:

* [IoT-SDK][lnk-sdks]

Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Använda Azure portal för att hantera IoT-hubb][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]:../cli-install-nodejs.md
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-CLI-arm]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 
