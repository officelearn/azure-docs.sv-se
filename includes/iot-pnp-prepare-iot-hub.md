---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: 3f3e7c54ec59c42ed102da682fc9aa077709ef3f
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262168"
---
## <a name="prepare-an-iot-hub"></a>Förbered en IoT-hubb

Du behöver också en Azure IoT-hubb i din Azure-prenumeration för att slutföra den här snabb starten. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. Om du inte har en IoT-hubb följer du [de här anvisningarna för att skapa en](../articles/iot-hub/iot-hub-create-using-cli.md).

> [!IMPORTANT]
> Under den offentliga för hands versionen är IoT Plug and Play-funktioner bara tillgängliga på IoT-hubbar som skapats i regionerna **Central USA**, **Nord Europa**och **Östra Japan** .

Om du använder Azure CLI lokalt loggar du först in på Azure-prenumerationen med hjälp av `az login`. Om du kör de här kommandona i Azure Cloud Shell är du inloggad automatiskt.

Om du använder Azure CLI lokalt bör `az`-versionen vara **2.0.73** eller senare. Azure Cloud Shell använder den senaste versionen. Använd kommandot `az --version` för att kontrol lera vilken version som är installerad på datorn.

Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI till din instans:

```azurecli-interactive
az extension add --name azure-iot
```

Kör följande kommando för att skapa enhets identiteten i din IoT-hubb. Ersätt plats hållarna **YourIoTHubName** och **YourDeviceID** med ditt eget _IoT Hub namn_ och ett valfritt _enhets-ID_ .

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Kör följande kommando för att hämta _enhets anslutnings strängen_ för enheten som du nyss registrerade (Anmärkning för användning senare):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```