---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4fe159660421113e0f0ac0586ae7e4a22d5bcda7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77474151"
---
### <a name="query-the-storage-queue"></a>Fråga om lagringskön

Du kan [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) använda kommandot för att visa lagringsköerna i ditt konto, som i följande exempel:

```azurecli-interactive
az storage queue list --output tsv
```

Utdata från det här `outqueue`kommandot innehåller en kö med namnet , vilket är kön som skapades när funktionen kördes.

Använd sedan [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) kommandot för att visa meddelandena i den här kön, som i det här exemplet:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Strängen som returneras ska vara samma som meddelandet som du skickade för att testa funktionen.

> [!NOTE]  
> I föregående exempel avkodas den returnerade strängen från base64. Detta beror på att kölagringsbindningarna skriver till och läs från Azure Storage som [base64-strängar](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding).