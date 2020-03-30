---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: d88d166e001e71cabdabc3a3d344adc4da19aa8c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75453716"
---
## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med ytterligare IoT Plug and Play-artiklar kan du behålla och återanvända de resurser som du använde i den här snabbstarten. Annars kan du ta bort de resurser som du skapade i den här snabbstarten för att undvika ytterligare avgifter.

Du kan ta bort både hubben och den registrerade enheten samtidigt genom att ta bort hela resursgruppen med följande kommando för Azure CLI. (Använd inte detta om dessa resurser delar en resursgrupp med andra resurser som du har för olika ändamål.)

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```
Om du bara vill ta bort IoT-hubben kör du följande kommando med Azure CLI:

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Om du bara vill ta bort enhetsidentiteten som du registrerade med din IoT-hubb kör du följande kommando med Azure CLI:

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Du kanske också vill ta bort de klonade exempelfilerna från utvecklingsmaskinen.