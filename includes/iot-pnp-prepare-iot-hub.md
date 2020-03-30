---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: a3340eb9e53afa83c35109bad7d22f81413dd644
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80234125"
---
## <a name="prepare-an-iot-hub"></a>Förbereda en IoT-hubb

Du behöver också en Azure IoT-hubb i din Azure-prenumeration för att slutföra den här snabbstarten. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar. Om du inte har en IoT-hubb följer du [dessa instruktioner för att skapa en](../articles/iot-hub/iot-hub-create-using-cli.md).

Om du använder Azure CLI lokalt loggar du först in `az login`på din Azure-prenumeration med . Om du kör dessa kommandon i Azure Cloud Shell loggas du in automatiskt.

Om du använder Azure CLI lokalt `az` bör versionen vara **2.0.73** eller senare. Azure Cloud Shell använder den senaste versionen. Använd `az --version` kommandot för att kontrollera den version som är installerad på datorn.

Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI i din instans:

```azurecli-interactive
az extension add --name azure-iot
```

Kör följande kommando för att skapa enhetsidentiteten i IoT-hubben. Ersätt **platshållarna YourIoTHubName** och **YourDeviceID** med ditt eget _IoT Hub-namn_ och ett _enhets-ID_ som du väljer.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Kör följande kommando för att hämta _enhetsanslutningssträngen_ för den enhet du just registrerade (observera för användning senare):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```
