---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 9bd213b63b69a25fb2530cd8f6659abf5357616a
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694636"
---
## <a name="set-up-authentication"></a>Konfigurera autentisering

För att få åtkomst till tjänsten måste du ange en konto nyckel, åtkomsttoken eller Azure Active Directory auth-token. Du kan också läsa mer om det här på [sidan Authentication Concept](/azure/spatial-anchors/concepts/authentication).

### <a name="account-keys"></a>Konto nycklar

Konto nycklar är en autentiseringsuppgift som gör att ditt program kan autentisera med tjänsten Azure spatiala ankare. Det avsedda syftet med konto nycklar är att hjälpa dig att komma igång snabbt. Särskilt under utvecklings fasen av ditt programs integrering med de spatiala Ankarena i Azure. Därför kan du använda konto nycklar genom att bädda in dem i dina klient program under utveckling. När du går vidare med utvecklingen rekommenderar vi starkt att du flyttar till en autentiseringsmekanism som är produktions nivå, stöds av åtkomsttoken eller Azure Active Directory användarautentisering. Om du vill ha en konto nyckel för utveckling kan du gå till kontot för ditt Azure-spatialdata och gå till fliken "nycklar".
