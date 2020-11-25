---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 7210b05566f5cd6f3c56792bce0904b3c9b645da
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993047"
---
## <a name="set-up-authentication"></a>Konfigurera autentisering

För att få åtkomst till tjänsten måste du ange en konto nyckel, åtkomsttoken eller Azure Active Directory auth-token. Du kan också läsa mer om det här på [sidan Authentication Concept](../articles/spatial-anchors/concepts/authentication.md).

### <a name="account-keys"></a>Konto nycklar

Konto nycklar är en autentiseringsuppgift som gör att ditt program kan autentisera med tjänsten Azure spatiala ankare. Det avsedda syftet med konto nycklar är att hjälpa dig att komma igång snabbt. Särskilt under utvecklings fasen av ditt programs integrering med de spatiala Ankarena i Azure. Därför kan du använda konto nycklar genom att bädda in dem i dina klient program under utveckling. När du går vidare med utvecklingen rekommenderar vi starkt att du flyttar till en autentiseringsmekanism som är produktions nivå, stöds av åtkomsttoken eller Azure Active Directory användarautentisering. Om du vill ha en konto nyckel för utveckling kan du gå till kontot för ditt Azure-spatialdata och gå till fliken "nycklar".