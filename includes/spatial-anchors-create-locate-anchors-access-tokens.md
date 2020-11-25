---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: c9b4e0fdf95c2bb4888a3a11820f1785d8545bd4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993025"
---
### <a name="access-tokens"></a>Åtkomsttoken

Åtkomsttoken är en mer robust metod för att autentisera med de spatiala Ankarena i Azure. Särskilt när du förbereder ditt program för en produktions distribution. Sammanfattningen av den här metoden är att konfigurera en server dels tjänst som klient programmet kan autentisera på ett säkert sätt. Server dels tjänst gränssnitten med AAD vid körning och med Azures ankare för att skydda token för att begära en åtkomsttoken. Denna token levereras sedan till klient programmet och används i SDK för att autentisera med Azure spatiala ankare.
