---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: cce71736369c5e7c448ccf92f5b569edc30a3966
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753226"
---
## <a name="setting-up-authentication"></a>Ställa in autentisering

Om du vill få åtkomst till tjänsten, måste du ange en nyckel eller åtkomsttoken AAD-auth-token.

### <a name="account-keys"></a>Kontonycklar

Nycklar är en autentiseringsuppgift som tillåter programmet att autentisera med tjänsten Azure Spatial fästpunkter. Syftet med nycklar är att hjälpa dig att komma igång snabbt. Särskilt under ditt programs integrering med Azure Spatial ankare utvecklingsfas. Därför kan du använda nycklar genom att bädda in dem i ditt klientprogram under utvecklingen. När du går utöver rekommenderar vi starkt för att flytta till en autentiseringsmekanism som är produktionsnivån, stöds av åtkomsttoken eller AAD-baserade användarautentisering. Besök Spatial fästpunkter för Azure-konto för att få en Kontonyckel för utveckling och gå till fliken ”nycklar”.
