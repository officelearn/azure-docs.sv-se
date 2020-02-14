---
title: Hantera IoT Central från Azure CLI | Microsoft Docs
description: I den här artikeln beskrivs hur du skapar och hanterar ditt IoT Central program med CLI. Du kan visa, ändra och ta bort programmet med CLI.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: c44b7cd045547d01d1a31f949a42087e78e88b21
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198845"
---
# <a name="manage-iot-central-from-azure-cli"></a>Hantera IoT Central från Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

I stället för att skapa och hantera IoT Central-program på webbplatsen för [azure IoT Central Application Manager](https://aka.ms/iotcentral) kan du använda [Azure CLI](/cli/azure/) för att hantera dina program.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du föredrar att köra Azure CLI på den lokala datorn kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). När du kör Azure CLI lokalt använder du kommandot **AZ login** för att logga in på Azure innan du provar kommandona i den här artikeln.

> [!TIP]
> Om du behöver köra CLI-kommandon i en annan Azure-prenumeration kan du läsa mer i [ändra den aktiva prenumerationen](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription).

## <a name="create-an-application"></a>Skapa ett program

Använd kommandot [AZ iotcentral app Create](/cli/azure/iotcentral/app#az-iotcentral-app-create) för att skapa ett IoT Central-program i din Azure-prenumeration. Exempel:

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

Dessa kommandon skapar först en resurs grupp i regionen USA, östra för programmet. I följande tabell beskrivs de parametrar som används med kommandot **AZ iotcentral app Create** :

| Parameter         | Beskrivning |
| ----------------- | ----------- |
| resource-group    | Resurs gruppen som innehåller programmet. Den här resurs gruppen måste redan finnas i din prenumeration. |
| location          | Som standard använder detta kommando platsen från resurs gruppen. För närvarande kan du skapa ett IoT Central-program i **Australien**, **Asien och Stillahavsområdet**, **Europa**eller **USA** geografiska områden. |
| namn              | Namnet på programmet i Azure Portal. |
| under domän         | Under domänen i programmets URL. I exemplet är programmets URL https://mysubdomain.azureiotcentral.com. |
| sku               | För närvarande kan du använda antingen **ST1** eller **ST2**. Se [priser för Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| mall          | Den program mal len som ska användas. Mer information finns i följande tabell. |
| visnings namn      | Namnet på programmet som det visas i användar gränssnittet. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Visa dina program

Använd kommandot [AZ iotcentral app List](/cli/azure/iotcentral/app#az-iotcentral-app-list) för att visa en lista över IoT Central program och visa metadata.

## <a name="modify-an-application"></a>Ändra ett program

Använd kommandot [AZ iotcentral app Update](/cli/azure/iotcentral/app#az-iotcentral-app-update) för att uppdatera metadata för ett IoT Central-program. Om du till exempel vill ändra visnings namnet för ditt program:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Ta bort ett program

Använd kommandot [AZ iotcentral app Delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) för att ta bort ett IoT Central-program. Exempel:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar Azure IoT Central-program från Azure CLI rekommenderar vi följande steg:

> [!div class="nextstepaction"]
> [Administrera ditt program](howto-administer.md)
