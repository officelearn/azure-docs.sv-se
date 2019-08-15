---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b4b050befe160d69fbc44b7f0a2ebbbbad3d705a
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2019
ms.locfileid: "69015922"
---
Lokalt redundant lagring (LRS) ger minst 99,999999999% (11 nio) objekt hållbarhet under ett angivet år. LRS tillhandahåller detta objekts tålighet genom att replikera dina data till en lagrings skalnings enhet. Ett Data Center, som finns i den region där du skapade ditt lagrings konto, är värd för lagrings skalnings enheten. En skrivbegäran till ett LRS-lagrings konto returneras bara efter att data har skrivits till alla repliker. Varje replik finns i separata fel domäner och uppgraderings domäner inom en lagrings skalnings enhet.

En lagrings skalnings enhet är en samling rack för lagringsnoder. En feldomän (FD) är en grupp noder som representerar en fysisk felenhet. Tänk på en fel domän som noder som tillhör samma fysiska rack. En uppgraderings domän (UD) är en grupp noder som uppgraderas tillsammans under uppgraderingen av en tjänst. Replikerna sprids över UDs-och fd inom en lagrings skalnings enhet. Den här arkitekturen garanterar att dina data är tillgängliga om ett maskin varu haveri påverkar ett enda rack eller när noder uppgraderas under en tjänst uppgradering.

LRS är alternativet för replikering med lägst kostnad och ger den minsta hållbarhet jämfört med andra alternativ. Om en katastrof på data center nivå (till exempel Fire eller översvämning) inträffar kan alla repliker gå förlorade eller inte återställas. För att minska den här risken rekommenderar Microsoft att använda zon-redundant lagring (ZRS), Geo-redundant lagring (GRS) eller geo-Zone-redundant lagring (GZRS).

* Om ditt program lagrar data som enkelt kan rekonstrueras om data går förlorade, kan du välja LRS.
* Vissa program är begränsade till att replikera data endast inom ett land/en region på grund av data styrnings krav. I vissa fall kan de kopplade regionerna som data replikeras för GRS-konton finnas i ett annat land/en annan region. Mer information om kopplade regioner finns i Azure- [regioner](https://azure.microsoft.com/regions/).
