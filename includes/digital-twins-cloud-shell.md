---
author: baanders
description: inkludera fil för Azure Digitals dubbla – konfigurera Cloud Shell och IoT-tillägget
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: b7c91d648c06970d53799c6ff505919dea17b3c0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87032244"
---
För att börja arbeta med Azure Digitals dubbla i ett öppet [Azure Cloud Shells](https://shell.azure.com) fönster, är det första att göra-loggen att logga in och ange gränssnitts kontexten till din prenumeration för den här sessionen. Kör de här kommandona i Cloud Shell:

```azurecli
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> Du kan också använda ditt prenumerations namn i stället för ID: t i kommandot ovan. 

Om det här är första gången du har använt den här prenumerationen med Azure Digitals, kör du kommandot för att registrera dig för namn rymden Azure Digitals. (Om du inte är säker är det OK att köra det igen även om du har gjort det någon gång tidigare.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Härnäst ska du lägga till [**Microsoft Azure IoT-tillägget för Azure CLI**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) till din Cloud Shell, för att aktivera kommandon för att interagera med Azure Digitals och andra IoT-tjänster. 

Kör först det här kommandot för att se en lista över alla tillägg som du redan har installerat.

```azurecli-interactive
az extension list
```

Utdata är en matris med alla tillägg som du för närvarande har. Leta efter `"name"` fältet för varje List post om du vill se tilläggets namn.

Använd utdata för att avgöra vilka av följande kommandon som ska köras för installations programmet (du kan köra fler än en).
* Om listan innehåller `azure-iot` : du har redan tillägget. Kör det här kommandot för att kontrol lera att du har den senaste uppdateringen och att det inte finns några tillgängliga uppdateringar:

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* Om listan **inte** innehåller `azure-iot` : du måste installera tillägget. Använd det här kommandot:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* Om listan innehåller `azure-iot-cli-ext` : det här är den äldre versionen av tillägget. Det går bara att installera en version av tillägget i taget, så du bör avinstallera det äldre tillägget. Använd det här kommandot:

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```

Nu är du redo att arbeta med Azure Digitals dubbla i Cloud Shell.

Du kan kontrol lera detta genom `az dt -h` att köra när som helst för att se en lista över de Azure Digital-kommandon som är tillgängliga på den översta nivån.