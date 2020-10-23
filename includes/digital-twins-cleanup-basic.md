---
author: baanders
description: inkludera fil för att rensa en enkel Azure Digital-instans
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 2e095a7b286a8860535d6b58fa93098735b30c87
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92372333"
---
Om du inte längre behöver resurserna som skapas i den här självstudien kan du ta bort dem genom att följa stegen nedan.

Med hjälp av [Azure Cloud Shell](https://shell.azure.com)kan du ta bort alla Azure-resurser i en resurs grupp med kommandot [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) . Detta tar bort resurs gruppen och Azures digitala dubbla instansen.

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. 

Öppna en Azure Cloud Shell och kör följande kommando för att ta bort resurs gruppen och allt den innehåller.

```azurecli
az group delete --name <your-resource-group>
```