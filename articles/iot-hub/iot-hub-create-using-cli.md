---
title: Skapa en IoT Hub med Azure CLI | Microsoft Docs
description: Så här skapar du en Azure IoT hub med Azure CLI.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: 95741b1a00c47468c7189e0103608c1dd7fa1d90
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046178"
---
# <a name="create-an-iot-hub-using-azure-cli"></a>Skapa en IoT hub med Azure CLI

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Den här artikeln visar hur du skapar en IoT hub med Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här anvisningen, behöver du en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Logga in och ange ditt Azure-konto

Om du kör Azure CLI lokalt istället för att använda Cloud Shell, måste du logga in på ditt Azure-konto.

I Kommandotolken, kör den [inloggningskommandot](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli):

    ```azurecli
    az login
    ```

Följ instruktionerna för att autentisera med hjälp av koden och logga in på ditt Azure-konto via en webbläsare.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Använda Azure CLI för att skapa en resursgrupp och Lägg sedan till en IoT-hubb.

1. När du skapar en IoT-hubb, skapar du den i en resursgrupp. Använd en befintlig resursgrupp eller kör följande [kommando för att skapa en resursgrupp](https://docs.microsoft.com/cli/azure/resource):
    
   ```azurecli
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > I föregående exempel skapas en resursgrupp i USA, västra. Du kan visa en lista över tillgängliga platser genom att köra det här kommandot: 
   >
   >``` bash
   >az account list-locations -o table
   >```
   >

2. Kör följande [kommando för att skapa en IoT-hubb](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) med hjälp av ett globalt unikt namn för din IoT-hubb i din resursgrupp:
    
   ```azurecli
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Föregående kommando skapar en IoT-hubb i S1-versionen som prisnivå som du faktureras. Mer information finns i [priser för Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>Ta bort en IoT-hubb

Du kan använda Azure CLI för att [ta bort en enskild resurs](https://docs.microsoft.com/cli/azure/resource), till exempel en IoT-hubb eller ta bort en resursgrupp och alla dess resurser, inklusive alla IoT-hubbar.

Att [ta bort en IoT-hubb](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete), kör du följande kommando:

```azurecli
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Att [ta bort en resursgrupp](https://docs.microsoft.com/cli/azure/group#az-group-delete) och alla dess resurser kör du följande kommando:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder en IoT-hubb finns i följande artiklar:

* [Utvecklarhandboken för IoT Hub](iot-hub-devguide.md)
* [Med Azure portal för att hantera IoT Hub](iot-hub-create-through-portal.md)
