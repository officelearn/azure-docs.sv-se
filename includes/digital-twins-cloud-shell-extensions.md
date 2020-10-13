---
author: baanders
description: ta med fil för Azure Digitals flätas – konfigurera senaste IoT-tillägget
ms.service: digital-twins
ms.topic: include
ms.date: 7/31/2020
ms.author: baanders
ms.openlocfilehash: 7e43ab6d419d6a08973864838c66aef0941d74f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606883"
---
Kör först det här kommandot för att se en lista över alla tillägg som du redan har installerat.

```azurecli
az extension list
```

Utdata är en matris med alla tillägg som du för närvarande har. Leta efter `"name"` fältet för varje List post om du vill se tilläggets namn.

Använd utdata för att avgöra vilka av följande kommandon som ska köras för installations programmet (du kan köra fler än en).
* Om listan innehåller `azure-iot` : du har redan tillägget. Kör det här kommandot för att kontrol lera att du har den senaste uppdateringen och att det inte finns några tillgängliga uppdateringar:

   ```azurecli
   az extension update --name azure-iot
   ```

* Om listan **inte** innehåller `azure-iot` : du måste installera tillägget. Använd det här kommandot:

    ```azurecli
    az extension add --name azure-iot
    ```

* Om listan innehåller `azure-iot-cli-ext` : det här är den äldre versionen av tillägget. Det går bara att installera en version av tillägget i taget, så du bör avinstallera det äldre tillägget. Använd det här kommandot:

   ```azurecli
   az extension remove --name azure-cli-iot-ext
   ```