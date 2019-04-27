---
title: Migrera en Azure CDN-profil från Verizon Standard till Premium för Verizon | Microsoft Docs
description: Mer information om att migrera en profil från Verizon Standard till Premium för Verizon.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: 7768dde424aedc295b53512db50c9dfc9db9ab8c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635643"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Migrera en Azure CDN-profil från Standard Verizon till Premium Verizon

När du skapar en profil för Azure Content Delivery Network (CDN) att hantera dina slutpunkter, erbjuder Azure CDN fyra olika produkter som du kan välja bland. Information om olika produkter och deras tillgängliga funktioner finns i [jämför Azure CDN produktfunktioner](cdn-features.md).

Om du har skapar en **Azure CDN Standard från Verizon** profil och använder den för att hantera dina CDN-slutpunkter, har du möjlighet att uppgradera den till en **Azure CDN Premium från Verizon** profil. När du uppgraderar kommer dina CDN-slutpunkter och alla dina data att bevaras. 

> [!IMPORTANT]
> När du har uppgraderat till en **Azure CDN Premium från Verizon** profilen du inte senare konvertera den tillbaka till en **Azure CDN Standard från Verizon** profil.
> 

Så här uppgraderar du en **Azure CDN Standard från Verizon** profil, kontakta [Microsoft Support](https://azure.microsoft.com/support/options/).

## <a name="profile-comparison"></a>Jämförelse av profil
**Azure CDN Premium från Verizon** profiler har följande viktiga skillnader från **Azure CDN Standard från Verizon** profiler:
- För vissa Azure CDN funktioner som [komprimering](cdn-improve-performance.md), [cachelagringsregler](cdn-caching-rules.md), och [geofiltrering](cdn-restrict-access-by-country.md), du kan inte använda Azure CDN-gränssnittet, måste du använda Verizon-portalen via den **Hantera** knappen.
- API: Till skillnad från med Standard Verizon, du kan inte använda API: et till att styra de funktioner som kan nås från Premium Verizon-portalen. Du kan dock använda API: et för att kontrollera andra vanliga funktioner, till exempel skapa/ta bort en slutpunkt, rensa/läser in cachelagrade tillgångar och aktivering/inaktivering av en anpassad domän.
- Prissättning: Premium Verizon har en annan prisstrukturen för överföring av data än Standard Verizon. Mer information finns i [prissättningen för CDN](https://azure.microsoft.com/pricing/details/cdn/).

**Azure CDN Premium från Verizon** profiler har följande ytterligare funktioner:
- [Tokenautentisering](cdn-token-auth.md): Tillåter användare att skaffa och använda en token för att hämta säkra resurser.
- [Regelmotor](cdn-rules-engine.md): Kan du anpassa hur HTTP-begäranden ska hanteras.
- Avancerade analysverktyg:
   - [Detaljerad HTTP-analys](cdn-advanced-http-reports.md)
   - [Edge-prestandaanalys](cdn-edge-performance.md)
   - [Analys i realtid](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Nästa steg
Läs mer om regelmotorn i [Azure CDN regelmotor – referens](cdn-rules-engine-reference.md).

