---
author: baanders
description: ta med fil för att rensa en enkel Azure Digital-förekomst och app-registrering
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: e8f167555434338416381aaf8978a3d551686fb2
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/16/2020
ms.locfileid: "90606388"
---
Om du inte längre behöver resurserna som skapas i den här självstudien kan du ta bort dem genom att följa stegen nedan.

Med hjälp av [Azure Cloud Shell](https://shell.azure.com)kan du ta bort alla Azure-resurser i en resurs grupp med kommandot [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) . Detta tar bort resurs gruppen och Azures digitala dubbla instansen.

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. 

Öppna en Azure Cloud Shell och kör följande kommando för att ta bort resurs gruppen och allt den innehåller.

```azurecli
az group delete --name <your-resource-group>
```

Ta sedan bort den Azure Active Directory app-registrering som du skapade för din klient app med det här kommandot:

```azurecli
az ad app delete --id <your-application-ID>
```