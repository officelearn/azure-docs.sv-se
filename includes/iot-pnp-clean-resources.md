---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: d88d166e001e71cabdabc3a3d344adc4da19aa8c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453716"
---
## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med ytterligare IoT Plug and Play-artiklar kan du behålla och återanvända de resurser som du använde i den här snabb starten. Annars kan du ta bort de resurser du skapade i den här snabb starten för att undvika ytterligare kostnader.

Du kan ta bort både Hub och den registrerade enheten samtidigt genom att ta bort hela resurs gruppen med följande kommando för Azure CLI. (Använd inte detta, men om dessa resurser delar en resurs grupp med andra resurser som du har för olika syfte.)

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```
Om du bara vill ta bort IoT-hubben kör du följande kommando med hjälp av Azure CLI:

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Om du bara vill ta bort enhets identiteten som du registrerade i IoT Hub kör du följande kommando med hjälp av Azure CLI:

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Du kanske också vill ta bort de klonade exempelfilerna från utvecklings datorn.