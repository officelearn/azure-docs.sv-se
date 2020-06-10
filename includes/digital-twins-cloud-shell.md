---
author: baanders
description: inkludera fil för Azure Digitals dubbla – konfigurera Cloud Shell och IoT-tillägget
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 76480959d94b9e2a2e04857c85b7359105e73e4f
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84612895"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Konfigurera Cloud Shell-session

När du har öppnat ett Cloud Shell-fönster loggar du först in och anger gränssnitts kontexten till din prenumeration för den här sessionen. 

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

Om det här är första gången du har använt den här prenumerationen med Azure Digitals, kör du kommandot för att registrera dig för namn rymden Azure Digitals. (Om du inte är säker är det OK att köra det igen även om du har gjort det någon gång tidigare.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Kör sedan följande kommando i Cloud Shell-instansen för att lägga till Microsoft Azure IoT-tillägget för Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

> [!NOTE]
> I den här artikeln används den senaste versionen av Azure IoT-tillägget, som kallas `azure-iot` . Den äldre versionen kallas `azure-iot-cli-ext` . Du bör bara ha en version installerad i taget. Du kan använda kommandot `az extension list` för att validera de installerade tilläggen.
> Används `az extension remove --name azure-cli-iot-ext` för att ta bort den äldre versionen av tillägget.
> Använd `az extension add --name azure-iot` för att lägga till den nya versionen av tillägget. Om du vill se vilka tillägg du har installerat använder du `az extension list` .

> [!TIP]
> Du kan köra `az dt -h` för att se de översta Azure digitala dubbla kommandona.