---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/17/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 3ac72360db5fb3d075b4caa3208f372f22441c7b
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755765"
---
## <a name="prepare-an-iot-hub"></a>Förbered en IoT-hubb

Du behöver en Azure IoT-hubb i din Azure-prenumeration för att kunna slutföra stegen i den här artikeln. Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Om du använder Azure CLI lokalt loggar du först in på Azure-prenumerationen med `az login` . Om du kör de här kommandona i Azure Cloud Shell är du inloggad automatiskt.

Om du använder Azure CLI lokalt `az` ska versionen vara **2.8.0** eller senare. Azure Cloud Shell använder den senaste versionen. Använd `az --version` kommandot för att kontrol lera vilken version som är installerad på datorn.

Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI till din instans:

```azurecli-interactive
az extension add --name azure-iot
```

Om du inte redan har en IoT-hubb att använda, kör du följande kommandon för att skapa en resurs grupp och en IoT-hubb på den kostnads fria nivån i din prenumeration. Ersätt `<YourIoTHubName>` med ett namn på hubben som du väljer:

```azurecli-interactive
az group create --name my-pnp-resourcegroup \
    --location centralus
az iot hub create --name <YourIoTHubName> \
    --resource-group my-pnp-resourcegroup --sku F1
```

> [!NOTE]
> IoT Plug and Play är för närvarande tillgängligt på IoT-hubbar som skapats i de centrala regionerna USA, norra Europa och Östra Japan. IoT Plug and Play-support ingår inte i IoT-hubbar på Basic-nivå.

Kör följande kommando för att skapa enhets identiteten i din IoT-hubb. Ersätt `<YourIoTHubName>` `<YourDeviceID>` plats hållarna och med ditt eget _IoT Hub namn_ och ett valfritt _enhets-ID_ .

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```
