---
title: Migrera Azure CDN-profil från Verizon Standard till Verizon Premium
description: Läs mer om information om hur du migrerar en profil från Verizon Standard till Verizon Premium.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: 8ab66117be4f05550b00defafc883108646be283
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083061"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Migrera en Azure CDN-profil från Standard Verizon till Premium Verizon

När du skapar en CDN-profil (Azure Content Delivery Network) för att hantera dina slutpunkter erbjuder Azure CDN fyra olika produkter som du kan välja mellan. Information om de olika produkterna och deras tillgängliga funktioner finns i [Jämför Azure CDN-produktfunktioner](cdn-features.md).

Om du har skapat en **Azure CDN Standard från Verizon-profil** och använder den för att hantera cdn-slutpunkterna har du möjlighet att uppgradera den till en **Azure CDN Premium från** Verizon-profil. När du uppgraderar bevaras CDN-slutpunkterna och alla dina data. 

> [!IMPORTANT]
> När du har uppgraderat till en **Azure CDN Premium från Verizon-profilen** kan du inte senare konvertera den tillbaka till en **Azure CDN Standard från** Verizon-profil.
> 

Om du vill uppgradera en **Azure CDN Standard från Verizon-profil** kontaktar du [Microsoft Support](https://azure.microsoft.com/support/options/).

## <a name="profile-comparison"></a>Jämförelse av profil
**Azure CDN Premium från Verizon-profiler** har följande viktiga skillnader från **Azure CDN Standard från** Verizon-profiler:
- För vissa Azure CDN-funktioner som [komprimering,](cdn-improve-performance.md) [cachelagringsregler](cdn-caching-rules.md)och [geofiltrering](cdn-restrict-access-by-country.md), du kan inte använda Azure CDN-gränssnittet, måste du använda Verizon-portalen via **knappen Hantera.**
- API: Till skillnad från Standard Verizon kan du inte använda API:et för att styra de funktioner som används från Premium Verizon-portalen. Du kan dock använda API:et för att styra andra vanliga funktioner, till exempel skapa/ta bort en slutpunkt, rensa/läsa in cachelagrade tillgångar och aktivera/inaktivera en anpassad domän.
- Prissättning: Premium Verizon har en annan prisstruktur för dataöverföringar än Standard Verizon. Mer information finns i [priser för Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

**Azure CDN Premium från Verizon-profiler** har följande ytterligare funktioner:
- [Tokenautentisering](cdn-token-auth.md): Tillåter användare att hämta och använda en token för att hämta säkra resurser.
- [Regelmotor:](cdn-rules-engine.md)Gör att du kan anpassa hur HTTP-begäranden hanteras.
- Avancerade analysverktyg:
   - [Detaljerad HTTP-analys](cdn-advanced-http-reports.md)
   - [Analys av prestanda kant](cdn-edge-performance.md)
   - [Analys i realtid](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Nästa steg
Mer information om regelmotorn finns i [Azure CDN regler motorreferens](cdn-rules-engine-reference.md).

