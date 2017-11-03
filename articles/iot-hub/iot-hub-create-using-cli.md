---
title: Skapa en IoT-hubb med Azure CLI (az.py) | Microsoft Docs
description: "Så här skapar du en Azure IoT-hubb med plattformsoberoende Azure CLI 2.0 (az.py)."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: dobett
ms.openlocfilehash: 161089159999a4a63a39b059e69a08b7a9297445
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-iot-hub-using-the-azure-cli-20"></a>Skapa en IoT-hubb som använder Azure CLI 2.0

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduktion

Du kan använda Azure CLI 2.0 (az.py) för att skapa och hantera Azure IoT-hubbar programmässigt. Den här artikeln visar hur du använder Azure CLI 2.0 (az.py) för att skapa en IoT-hubb.

Du kan slutföra uppgiften med någon av följande CLI-versioner:

* [Azure CLI (azure.js)](iot-hub-create-using-cli-nodejs.md) – CLI för distributionsmodeller för hantering av klassisk och resurs.
* Azure CLI 2.0 (az.py) - nästa generations CLI för hantering av resursdistributionsmodell som beskrivs i den här artikeln.

För att kunna genomföra den här kursen behöver du följande:

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.
* [Azure CLI 2.0][lnk-CLI-install].

## <a name="sign-in-and-set-your-azure-account"></a>Logga in och ange ditt Azure-konto

Logga in på ditt Azure-konto och välja din prenumeration.

1. Vid Kommandotolken kör du den [inloggningen kommandot][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Följ instruktionerna för att autentisera med hjälp av koden och logga in på ditt Azure-konto via en webbläsare.

2. Om du har flera Azure-prenumerationer, ger logga in på Azure åtkomst till alla Azure konton som är associerade med dina autentiseringsuppgifter. Använd följande [kommando för att visa en lista med Azure-konton] [ lnk-az-account-command] som du kan använda:
    
    ```azurecli
    az account list 
    ```

    Använd följande kommando för att välja prenumeration som du vill använda för att köra kommandona för att skapa din IoT-hubb. Du kan använda prenumerationsnamn eller ID från utdata från det föregående kommandot:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="create-an-iot-hub"></a>Skapa en IoT-hubb

Använda Azure CLI för att skapa en resursgrupp och Lägg sedan till en IoT-hubb.

1. När du skapar en IoT-hubb skapar du den i en resursgrupp. Använd en befintlig resursgrupp eller kör följande [kommando för att skapa en resursgrupp][lnk-az-resource-command]:
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > Exemplet ovan skapar resursgruppen västra USA-plats. Du kan visa en lista över tillgängliga platser genom att köra kommandot `az account list-locations -o table`.
    >
    >

2. Kör följande [kommando för att skapa en IoT-hubb] [ lnk-az-iot-command] med hjälp av ett globalt unikt namn för din IoT-hubb i din resursgrupp:
    
    ```azurecli
    az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1
    ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


> [!NOTE]
> Föregående kommando skapar en IoT-hubb i S1 prisnivå du debiteras. Mer information finns i [Azure IoT Hub-priser][lnk-iot-pricing].
>
>

## <a name="remove-an-iot-hub"></a>Ta bort en IoT-hubb

Du kan använda Azure CLI till [ta bort en enskild resurs][lnk-az-resource-command], till exempel en IoT-hubb eller ta bort en resursgrupp och alla dess resurser, inklusive alla IoT-hubbar.

Ta bort en IoT-hubb genom att köra följande kommando:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

Ta bort en resursgrupp och alla dess resurser genom att köra följande kommando:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Nästa steg
Mer information om hur du utvecklar för IoT-hubb finns i följande artiklar:

* [Utvecklarhandbok för IoT-hubb][lnk-devguide]

Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Använda Azure portal för att hantera IoT-hubb][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 
