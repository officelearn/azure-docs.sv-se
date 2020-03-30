---
title: Hantera IoT Central från Azure CLI | Microsoft-dokument
description: I den här artikeln beskrivs hur du skapar och hanterar ditt IoT Central-program med CLI. Du kan visa, ändra och ta bort programmet med CLI.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: df24a2dc6e9bd058a2f8b1355b8760653ed3128a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365530"
---
# <a name="manage-iot-central-from-azure-cli"></a>Hantera IoT Central från Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

I stället för att skapa och hantera IoT Central-program på Azure [IoT Central application manager-webbplatsen](https://aka.ms/iotcentral) kan du använda [Azure CLI](/cli/azure/) för att hantera dina program.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du föredrar att köra Azure CLI på din lokala dator läser du [Installera Azure CLI](/cli/azure/install-azure-cli). När du kör Azure CLI lokalt använder du kommandot **az login** för att logga in på Azure innan du försöker med kommandona i den här artikeln.

> [!TIP]
> Om du behöver köra dina CLI-kommandon i en annan Azure-prenumeration läser du [Ändra den aktiva prenumerationen](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription).

## <a name="install-the-extension"></a>Installera tillägget

Kommandona i den här artikeln är en del av **azure-iot CLI-tillägget.** Kör följande kommando för att installera tillägget:

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="create-an-application"></a>Skapa ett program

Använd kommandot [az iotcentral app create](/cli/azure/iotcentral/app#az-iotcentral-app-create) för att skapa ett IoT Central-program i din Azure-prenumeration. Ett exempel:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iotcentral app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview@1.0.0" \
  --display-name "My Custom Display Name"
```

Dessa kommandon skapar först en resursgrupp i regionen östra USA för programmet. I följande tabell beskrivs de parametrar som används med kommandot **az iotcentral app create:**

| Parameter         | Beskrivning |
| ----------------- | ----------- |
| resource-group    | Resursgruppen som innehåller programmet. Den här resursgruppen måste redan finnas i din prenumeration. |
| location          | Som standard använder det här kommandot platsen från resursgruppen. För närvarande kan du skapa en IoT Central ansökan i **Australien,** **Asien och Stillahavsområdet**, **Europa**, **USA,** **Storbritannien**och **Japan** geografiska områden. |
| namn              | Namnet på programmet i Azure-portalen. |
| Underdomän         | Underdomänen i url:en för programmet. I exemplet är `https://mysubdomain.azureiotcentral.com`programmets URL . |
| sku               | För närvarande kan du använda antingen **ST1** eller **ST2**. Se [Azure IoT Central prissättning](https://azure.microsoft.com/pricing/details/iot-central/). |
| mall          | Programmallen som ska användas. Se tabellen nedan för mer information. |
| visningsnamn      | Namnet på programmet som visas i användargränssnittet. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Visa dina program

Använd kommandot [az iotcentral app list](/cli/azure/iotcentral/app#az-iotcentral-app-list) för att lista dina IoT Central-program och visa metadata.

## <a name="modify-an-application"></a>Ändra ett program

Använd kommandot [az iotcentral app update](/cli/azure/iotcentral/app#az-iotcentral-app-update) för att uppdatera metadata för ett IoT Central-program. Om du till exempel vill ändra visningsnamnet för ditt program:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Ta bort ett program

Använd kommandot [az iotcentral app delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) för att ta bort ett IoT Central-program. Ett exempel:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar Azure IoT Central-program från Azure CLI, här är det föreslagna nästa steget:

> [!div class="nextstepaction"]
> [Administrera ditt program](howto-administer.md)
