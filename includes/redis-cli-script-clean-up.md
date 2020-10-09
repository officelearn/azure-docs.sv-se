---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: e5166934c4f7e1ecc917d2a5afcbb26ea9a637a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "67187147"
---
## <a name="clean-up-deployment"></a>Rensa distribution 

När skript exemplet har körts kan du använda följande kommando för att ta bort resurs gruppen, Azure cache för Redis-instansen och relaterade resurser i resurs gruppen.

```azurecli
az group delete --name contosoGroup
```