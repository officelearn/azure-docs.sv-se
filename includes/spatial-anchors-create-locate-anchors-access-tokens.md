---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232715"
---
### <a name="access-tokens"></a>Åtkomsttoken

Åtkomsttoken är en mer stabil metod för att autentisera med Azure Spatial fästpunkter. Särskilt när du förbereder ditt program för en Produktionsdistribution. Sammanfattning av den här metoden är att ställa in en backend-tjänst som ditt klientprogram på ett säkert sätt kan autentisera med. Backend-tjänst gränssnitten med AAD vid körning och Azure Spatial ankare Secure Token Service att begära en åtkomst-Token. Denna token är sedan levereras till klientprogrammet och används i SDK för att autentisera med Azure Spatial fästpunkter.
