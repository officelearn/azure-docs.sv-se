---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: afa3fea0c172134afb06abc63c90805a32c89639
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907767"
---
## <a name="set-up-authentication"></a>Konfigurera autentisering

Om du vill få åtkomst till tjänsten, måste du ange en nyckel eller åtkomsttoken AAD-auth-token.

### <a name="account-keys"></a>Kontonycklar

Nycklar är en autentiseringsuppgift som tillåter programmet att autentisera med tjänsten Azure Spatial fästpunkter. Syftet med nycklar är att hjälpa dig att komma igång snabbt. Särskilt under ditt programs integrering med Azure Spatial ankare utvecklingsfas. Därför kan du använda nycklar genom att bädda in dem i ditt klientprogram under utvecklingen. När du går utöver rekommenderar vi starkt för att flytta till en autentiseringsmekanism som är produktionsnivån, stöds av åtkomsttoken eller AAD-baserade användarautentisering. Besök Spatial fästpunkter för Azure-konto för att få en Kontonyckel för utveckling och gå till fliken ”nycklar”.
