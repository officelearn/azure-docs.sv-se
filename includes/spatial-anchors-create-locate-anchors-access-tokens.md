---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "67187038"
---
### <a name="access-tokens"></a>Åtkomsttoken

Åtkomsttoken är en mer robust metod för att autentisera med Azure Spatial Anchors. Särskilt när du förbereder ditt program för en produktionsdistribution. Sammanfattningen av den här metoden är att ställa in en backend-tjänst som klientprogrammet kan autentisera säkert med. Din backend-tjänst gränssnitt med AAD vid körning och med Azure Spatial Anchors Secure Token Service för att begära en åtkomsttoken. Den här token levereras sedan till klientprogrammet och används i SDK för att autentisera med Azure Spatial Anchors.
