---
title: Hantera IoT Central från Azure CLI | Microsoft Docs
description: Hantera IoT Central från Azure CLI.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/07/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 9e5d842cece316bc9c53e1e8583f40a0f222b91d
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2019
ms.locfileid: "56808723"
---
# <a name="manage-iot-central-from-azure-cli"></a>Hantera IoT Central från Azure CLI

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

I stället för att skapa och hantera IoT Central-program från IoT Central [Programhanterare](https://aka.ms/iotcentral) kan du använda [Azure CLI](/cli/azure/) att hantera dina program.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du vill köra Azure CLI på din lokala dator finns [installera Azure CLI](/cli/azure/install-azure-cli). När du kör Azure CLI lokalt måste du använda den **az-inloggning** kommando för att logga in på Azure innan du försöker kommandona i den här artikeln.

## <a name="create-an-application"></a>Skapa ett program

Använd den [az iotcentral app skapar](/cli/azure/iotcentral/app#az-iotcentral-app-create) kommando för att skapa en IoT Central-App i Azure-prenumerationen. Exempel:

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
  --sku S1 --template "iotc-demo@1.0.0" \
  --display-name "My Custom Display Name"
```

Dessa kommandon kan du först skapa en resursgrupp i östra USA för programmet. I följande tabell beskrivs de parametrar som används med den **az iotcentral app skapar** kommando:

| Parameter         | Beskrivning |
| ----------------- | ----------- |
| resource-group    | Den resursgrupp som innehåller programmet. Den här resursgruppen måste redan finnas i din prenumeration. |
| location          | Som standard använder det här kommandot platsen från resursgruppen. För närvarande kan du skapa en IoT Central-program i den **USA, östra**, **västra USA**, **Nordeuropa**, eller **Västeuropa** regioner. |
| namn              | Namnet på programmet i Azure-portalen. |
| subdomain         | Underdomänen i URL: en för programmet. I det här exemplet programmets URL är https://mysubdomain.azureiotcentral.com. |
| sku               | Det enda värdet som är för närvarande **S1** (standard-nivån). Se [priser för Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| mall          | Mall för program att använda. Mer information finns i följande tabell: |
| display-name      | Namnet på appen som visas i Användargränssnittet. |

**Programmallar**

| Mallens namn            | Beskrivning |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Skapar ett tomt program som du kan fylla med dina egna enhetsmallar och enheter. |
| iotc-demo@1.0.0          | Skapar ett program som innehåller en mall för enheter som redan har skapats för en kylande varuautomat. Använd den här mallen för att börja utforska Azure IoT Central. |
| iotc-devkit-sample@1.0.0 | Skapar ett program med enhetsmallar där du kan ansluta en MXChip- eller Raspberry Pi-enhet. Använd den här mallen om du utvecklar en enhet experimentera med någon av dessa enheter. |

## <a name="view-your-applications"></a>Visa dina program

Använd den [az iotcentral applista](/cli/azure/iotcentral/app#az-iotcentral-app-list) kommando för att lista med IoT Central-program och visa metadata.

## <a name="modify-an-application"></a>Ändra ett program

Använd den [az iotcentral appuppdatering](/cli/azure/iotcentral/app#az-iotcentral-app-update) kommando för att uppdatera metadata för ett program med IoT Central. Till exempel vill ändra visningsnamnet för ditt program:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Ta bort ett program

Använd den [az iotcentral app delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) kommando för att ta bort ett IoT Central-program. Exempel:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar Azure IoT Central program från Azure CLI, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Administrera ditt program](howto-administer.md)
