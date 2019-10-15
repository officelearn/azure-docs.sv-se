---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: ca576290ea511dc54b89ecebef72ca2a42e9169f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329545"
---
### <a name="query-the-storage-queue"></a>Fråga lagrings kön

Du kan använda kommandot [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) om du vill visa lagrings köer i ditt konto, som i följande exempel:

```azurecli-interactive
az storage queue list --output tsv
```

Utdata från det här kommandot innehåller en kö med namnet `outqueue`, som är den kö som skapades när funktionen kördes.

Använd sedan kommandot [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) för att visa meddelanden i den här kön, som i det här exemplet:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Den returnerade strängen ska vara samma som det meddelande som du skickade för att testa funktionen.

> [!NOTE]  
> I föregående exempel avkodas den returnerade strängen från base64. Detta beror på att Queue Storage-bindningarna skriver till och läser från Azure Storage som [base64-strängar](../articles/azure-functions/functions-bindings-storage-queue.md#encoding).