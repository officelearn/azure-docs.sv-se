---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 72616d5d79b4dbdb8e14463c706c1626eeb50e97
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632501"
---
## <a name="set-up-authentication"></a>Konfigurera autentisering

Om du vill få åtkomst till tjänsten, måste du ange en nyckel, åtkomst-token eller Azure Active Directory-autentiseringstoken.

### <a name="account-keys"></a>Kontonycklar

Nycklar är en autentiseringsuppgift som tillåter programmet att autentisera med tjänsten Azure Spatial fästpunkter. Syftet med nycklar är att hjälpa dig att komma igång snabbt. Särskilt under ditt programs integrering med Azure Spatial ankare utvecklingsfas. Därför kan du använda nycklar genom att bädda in dem i ditt klientprogram under utvecklingen. När du går utöver rekommenderar vi starkt för att flytta till en autentiseringsmekanism som är produktionsnivån, stöds av åtkomsttoken eller användarautentisering för Azure Active Directory. Besök Spatial fästpunkter för Azure-konto för att få en Kontonyckel för utveckling och gå till fliken ”nycklar”.
