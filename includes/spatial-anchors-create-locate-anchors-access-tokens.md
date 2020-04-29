---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "67187038"
---
### <a name="access-tokens"></a>Åtkomsttoken

Åtkomsttoken är en mer robust metod för att autentisera med de spatiala Ankarena i Azure. Särskilt när du förbereder ditt program för en produktions distribution. Sammanfattningen av den här metoden är att konfigurera en server dels tjänst som klient programmet kan autentisera på ett säkert sätt. Server dels tjänst gränssnitten med AAD vid körning och med Azures ankare för att skydda token för att begära en åtkomsttoken. Denna token levereras sedan till klient programmet och används i SDK för att autentisera med Azure spatiala ankare.
