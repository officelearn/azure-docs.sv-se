---
title: Skapa en IoT-hubb med Azure CLI | Microsoft-dokument
description: Lär dig hur du använder Azure CLI-kommandona för att skapa en resursgrupp och sedan skapa en IoT-hubb i resursgruppen. Lär dig också hur du tar bort navet.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e7253747d95917e34725cc7c6c034b1306f65e50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284725"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Skapa en IoT-hubb med Azure CLI

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Den här artikeln visar hur du skapar en IoT-hubb med Azure CLI.

## <a name="prerequisites"></a>Krav

För att slutföra den här how-to behöver du en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Logga in och ange ditt Azure-konto

Om du kör Azure CLI lokalt i stället för att använda Cloud Shell måste du logga in på ditt Azure-konto.

I kommandotolken kör du [inloggningskommandot](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli):

   ```azurecli
   az login
   ```

Följ instruktionerna för att autentisera med hjälp av koden och logga in på ditt Azure-konto via en webbläsare.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Använd Azure CLI för att skapa en resursgrupp och sedan lägga till en IoT-hubb.

1. När du skapar en IoT-hubb måste du skapa den i en resursgrupp. Använd en befintlig resursgrupp eller kör följande [kommando för att skapa en resursgrupp](https://docs.microsoft.com/cli/azure/resource):
    
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

2. Kör följande [kommando för att skapa en IoT-hubb](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) i resursgruppen med ett globalt unikt namn för IoT-hubben:
    
   ```azurecli
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Det föregående kommandot skapar en IoT-hubb i den S1-prisnivå som du faktureras för. Mer information finns i [Azure IoT Hub-priser](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>Ta bort en IoT-hubb

Du kan använda Azure CLI för att [ta bort en enskild resurs](https://docs.microsoft.com/cli/azure/resource), till exempel en IoT-hubb, eller ta bort en resursgrupp och alla dess resurser, inklusive alla IoT-hubbar.

Om du vill [ta bort en IoT-hubb](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete)kör du följande kommando:

```azurecli
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Om du vill [ta bort en resursgrupp](https://docs.microsoft.com/cli/azure/group#az-group-delete) och alla dess resurser kör du följande kommando:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder en IoT-hubb finns i följande artiklar:

* [Utvecklarhandledning för IoT Hub](iot-hub-devguide.md)
* [Använda Azure-portalen för att hantera IoT Hub](iot-hub-create-through-portal.md)
