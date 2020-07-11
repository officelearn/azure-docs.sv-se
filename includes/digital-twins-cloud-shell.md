---
author: baanders
description: inkludera fil för Azure Digitals dubbla – konfigurera Cloud Shell och IoT-tillägget
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 6f472865c131b873f1ae0a21fa9ec55865fb2b29
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277819"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Konfigurera Cloud Shell-session

När du har öppnat ett Cloud Shell-fönster loggar du först in och anger gränssnitts kontexten till din prenumeration för den här sessionen. Kör de här kommandona i Cloud Shell:

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```
> [!TIP]
> Du kan också ange prenumerationen med hjälp av ditt prenumerations namn. Använd det här kommandot: 
> ```azurecli
> az account set --subscription "your-Azure-subscription-name"
> 
Om det här är första gången du har använt den här prenumerationen med Azure Digitals, kör du kommandot för att registrera dig för namn rymden Azure Digitals. (Om du inte är säker är det OK att köra det igen även om du har gjort det någon gång tidigare.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Härnäst ska du lägga till [**Microsoft Azure IoT-tillägget för Azure CLI**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) till din Cloud Shell, för att aktivera kommandon för att interagera med Azure Digitals och andra IoT-tjänster. 

Kör först det här kommandot för att se en lista över alla tillägg som du redan har installerat.

```azurecli-interactive
az extension list
```

I utdata letar du efter `"name"` fältet för varje post i listan för att se tilläggets namn.

Använd utdata för att avgöra vilka av följande kommandon som ska köras för installations programmet (du kan köra fler än en).
* Om listan innehåller `azure-iot` : du har redan tillägget. Kör det här kommandot för att kontrol lera att du har den senaste uppdateringen:

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