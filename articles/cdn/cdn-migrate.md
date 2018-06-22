---
title: Migrera en Azure CDN-profil från Verizon Standard till Verizon Premium | Microsoft Docs
description: Läs mer om information om att migrera en profil från Verizon Standard till Verizon Premium.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: v-deasim
ms.custom: ''
ms.openlocfilehash: 615f35c602f9a086bdc5c32b04a97efa368411a3
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308133"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Migrera en Azure CDN-profil från Standard Verizon till Premium Verizon

Azure Content Delivery Network (CDN) erbjuder fyra olika produkter, som erbjuder olika funktioner: 
- **Azure CDN Standard från Microsoft**
- **Azure CDN Standard från Akamai**
- **Azure CDN Standard från Verizon**
- **Azure CDN Premium från Verizon**.

Om du använder en **Azure CDN Standard från Verizon** profilen för att hantera dina CDN-slutpunkter, har du möjlighet att uppgradera den till en **Azure CDN Premium från Verizon** profil och bevara alla dina data.

Så här uppgraderar du en **Azure CDN Standard från Verizon** profil, kontakta [Microsoft-supporten](https://azure.microsoft.com/support/options/).

> [!IMPORTANT]
> När du har uppgraderat till en **Azure CDN Premium från Verizon** -profil kan du inte senare konvertera den tillbaka till en **Azure CDN Standard från Verizon** profil.
> 

## <a name="profile-comparison"></a>Jämförelse av profil
**Azure CDN Premium från Verizon** profiler har följande viktiga skillnader från **Azure CDN Standard från Verizon** profiler:
- För vissa Azure CDN funktioner som [komprimering](cdn-improve-performance.md), cachelagring regler och [geo filtrering](cdn-restrict-access-by-country.md), du kan inte använda Azure CDN-gränssnittet, måste du använda Verizon portalen via den **hantera**knappen.
- API: Till skillnad från Standard Verizon du kan inte använda API: N till att styra de funktioner som kan nås från Premium Verizon portal. Du kan dock använda API: et för att styra andra vanliga funktioner, till exempel skapa/ta bort en slutpunkt, rensa/inläsning cachelagrade tillgångar och aktivering/inaktivering av en anpassad domän.
- Priser: Premium Verizon har en annan prisnivå struktur för dataöverföringar än Standard Verizon. Mer information finns i [innehållsleveransnätverk priser](https://azure.microsoft.com/pricing/details/cdn/).

**Azure CDN Premium från Verizon** profiler har följande funktioner:
- [Tokenautentisering](cdn-token-auth.md): användarna kan hämta och använda en token för att hämta säkra resurser.
- [Regelmotor](cdn-rules-engine.md): kan du anpassa hur HTTP-begäranden ska hanteras.
- Avancerade analyser verktyg:
   - [Detaljerad HTTP analytics](cdn-advanced-http-reports.md)
   - [Edge prestanda analytics](cdn-edge-performance.md)
   - [Realtidsanalys](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Nästa steg
En detaljerad jämförelse av funktioner i Azure CDN finns [Azure CDN produktfunktioner](Compare Azure CDN product features).

Mer information om regler motorn finns [Azure CDN regler motorn referens](cdn-rules-engine-reference.md).

