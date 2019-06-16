---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: e5166934c4f7e1ecc917d2a5afcbb26ea9a637a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66132763"
---
## <a name="clean-up-deployment"></a>Rensa distribution 

När exempelskriptet har körts kan användas följande kommando för att ta bort resursgruppen, Azure Cache för Redis-instans och alla relaterade resurser i resursgruppen.

```azurecli
az group delete --name contosoGroup
```