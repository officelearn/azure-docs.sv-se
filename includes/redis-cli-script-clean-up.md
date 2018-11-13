---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 23b92f7d1b1c3ef488e182a6443f275365a03d9a
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572742"
---
## <a name="clean-up-deployment"></a>Rensa distribution 

När exempelskriptet har körts kan användas följande kommando för att ta bort resursgruppen, Azure Redis Cache-instans och alla relaterade resurser i resursgruppen.

```azurecli
az group delete --name contosoGroup
```