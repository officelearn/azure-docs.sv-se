---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: e5166934c4f7e1ecc917d2a5afcbb26ea9a637a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67187147"
---
## <a name="clean-up-deployment"></a>Rensa distribution 

När skriptexemplet har körts kan kommandot follow användas för att ta bort resursgruppen, Azure Cache för Redis-instansen och eventuella relaterade resurser i resursgruppen.

```azurecli
az group delete --name contosoGroup
```