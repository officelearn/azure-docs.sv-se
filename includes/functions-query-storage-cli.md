---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4fe159660421113e0f0ac0586ae7e4a22d5bcda7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "77474151"
---
### <a name="query-the-storage-queue"></a>Fråga lagrings kön

Du kan använda [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) kommandot för att Visa lagrings köer i ditt konto, som i följande exempel:

```azurecli-interactive
az storage queue list --output tsv
```

Utdata från det här kommandot innehåller en kö med namnet `outqueue` , som är kön som skapades när funktionen kördes.

Använd sedan [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) kommandot för att visa meddelanden i den här kön, som i det här exemplet:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Den returnerade strängen ska vara samma som det meddelande som du skickade för att testa funktionen.

> [!NOTE]  
> I föregående exempel avkodas den returnerade strängen från base64. Detta beror på att Queue Storage-bindningarna skriver till och läser från Azure Storage som [base64-strängar](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding).