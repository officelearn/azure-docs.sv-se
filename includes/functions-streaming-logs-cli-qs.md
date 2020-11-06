---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 9213bdd8e63fc1e8ab7bdba8ac7a569be0dfe6be
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93425150"
---
Kör följande kommando för att Visa nästan real tids [strömnings loggar](../articles/azure-functions/functions-run-local.md#enable-streaming-logs):

```console
func azure functionapp logstream <APP_NAME> 
```

I ett separat terminalfönster eller i webbläsaren, anropar du fjärrfunktionen igen. En utförlig logg över funktions körningen i Azure visas i terminalen. 