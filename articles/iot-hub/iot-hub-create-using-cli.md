---
title: Skapa en IoT Hub med hjälp av Azure CLI | Microsoft Docs
description: Lär dig hur du använder Azure CLI-kommandon för att skapa en resurs grupp och sedan skapa en IoT-hubb i resurs gruppen. Lär dig också hur du tar bort hubben.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e7253747d95917e34725cc7c6c034b1306f65e50
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708084"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Skapa en IoT-hubb med Azure CLI

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Den här artikeln visar hur du skapar en IoT Hub med Azure CLI.

## <a name="prerequisites"></a>Krav

För att slutföra den här instruktionen behöver du en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Logga in och ange ditt Azure-konto

Om du kör Azure CLI lokalt i stället för att använda Cloud Shell måste du logga in på ditt Azure-konto.

I kommandotolken kör du [inloggningskommandot](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli):

   ```azurecli
   az login
   ```

Följ instruktionerna för att autentisera med hjälp av koden och logga in på ditt Azure-konto via en webbläsare.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Använd Azure CLI för att skapa en resurs grupp och Lägg sedan till en IoT-hubb.

1. När du skapar en IoT-hubb måste du skapa den i en resurs grupp. Använd en befintlig resursgrupp eller kör följande [kommando för att skapa en resursgrupp](https://docs.microsoft.com/cli/azure/resource):
    
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

2. Kör följande [kommando för att skapa en IoT-hubb](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) i resurs gruppen med ett globalt unikt namn för din IoT Hub:
    
   ```azurecli
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Föregående kommando skapar en IoT-hubb i pris nivån S1 som du faktureras för. Mer information finns i [priser för Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>Ta bort ett IoT Hub

Du kan använda Azure CLI för att [ta bort en enskild resurs](https://docs.microsoft.com/cli/azure/resource), till exempel en IoT-hubb eller ta bort en resurs grupp och alla dess resurser, inklusive IoT-hubbar.

Om du vill [ta bort en IoT-hubb](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete)kör du följande kommando:

```azurecli
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Om du vill [ta bort en resurs grupp](https://docs.microsoft.com/cli/azure/group#az-group-delete) och alla dess resurser kör du följande kommando:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder en IoT Hub finns i följande artiklar:

* [Guide för IoT Hub utvecklare](iot-hub-devguide.md)
* [Använda Azure Portal för att hantera IoT Hub](iot-hub-create-through-portal.md)
