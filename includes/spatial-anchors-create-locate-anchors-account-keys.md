---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 72616d5d79b4dbdb8e14463c706c1626eeb50e97
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110726"
---
## <a name="set-up-authentication"></a>Konfigurera autentisering

Om du vill få åtkomst till tjänsten, måste du ange en nyckel, åtkomst-token eller Azure Active Directory-autentiseringstoken.

### <a name="account-keys"></a>Kontonycklar

Nycklar är en autentiseringsuppgift som tillåter programmet att autentisera med tjänsten Azure Spatial fästpunkter. Syftet med nycklar är att hjälpa dig att komma igång snabbt. Särskilt under ditt programs integrering med Azure Spatial ankare utvecklingsfas. Därför kan du använda nycklar genom att bädda in dem i ditt klientprogram under utvecklingen. När du går utöver rekommenderar vi starkt för att flytta till en autentiseringsmekanism som är produktionsnivån, stöds av åtkomsttoken eller användarautentisering för Azure Active Directory. Besök Spatial fästpunkter för Azure-konto för att få en Kontonyckel för utveckling och gå till fliken ”nycklar”.
