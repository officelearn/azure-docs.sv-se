---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/12/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 32032f729283cb3f2a786412b563fdee88ba4c8a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729653"
---
Lokalt redundant lagring (LRS) innehåller minst 99,999999999% (11 nines) objektshållbarhet under ett givet år. LRS får det här objektet tillförlitlighet genom att replikera dina data till en lagringsskalningsenhet. Ett datacenter, i den region där du skapade ditt storage-konto är värd för lagringsskalningsenhet. En skrivbegäran till ett LRS-lagringskonto returnerar har bara när data skrivs till alla repliker. Varje replik finns i separata feldomäner och uppgraderingsdomäner i en lagringsskalningsenhet.

En lagringsskalningsenhet är en samling av rack lagringsnoder. En feldomän (FD) är en grupp av noder som representerar en fysisk enhet med fel. Tänk på en feldomän som noder som tillhör samma fysiska rack. En uppgraderingsdomän (UD) är en grupp av noder som uppgraderas tillsammans under en Serviceuppgradering av (distribution). Replikerna är fördelade mellan Uppdateringsdomäner och Feldomäner i en lagringsskalningsenhet. Den här arkitekturen säkerställer att dina data finns tillgänglig om ett maskinvarufel påverkar en enda rack eller när noderna uppgraderas vid en uppgradering av tjänsten.

LRS är billigaste replikeringsalternativ och erbjuder de lägsta tillförlitlighet jämfört med andra alternativ. Om en datacenter-nivå (till exempel fire eller överbelasta) redundansplats, vara alla repliker förlorad eller oåterkalleligt. Microsoft rekommenderar för att minska denna risk med zonredundant lagring (ZRS) eller geo-redundant lagring (GRS).

* Om ditt program lagrar data som rekonstrueras enkelt om dataförluster uppstår, kan du välja LRS.
* Vissa program är begränsade till att replikera data bara i ett land på grund av krav för styrning av data. I vissa fall kanske de länkade områdena för vilket data replikeras för GRS-konton i ett annat land. Mer information om länkade regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/).
