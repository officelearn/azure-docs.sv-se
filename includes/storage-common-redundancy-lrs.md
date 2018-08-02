---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 58d81bd4c1ce4b3af91a335039f62df08b340576
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399999"
---
Lokalt redundant lagring (LRS) är utformad för att tillhandahålla minst 99,999999999% (11 9) objektshållbarhet under ett givet år genom att replikera dina data i en lagringsskalningsenhet. En lagringsskalningsenhet finns i ett datacenter i den region där du skapade ditt lagringskonto. En skrivbegäran till ett LRS-lagringskonto returnerar har bara när data har skrivits till alla repliker. De här replikeringarna som var och en finns i separata feldomäner och uppdateringsdomäner i en lagringsskalningsenhet.

En lagringsskalningsenhet är en samling av rack lagringsnoder. En feldomän (FD) är en grupp av noder som representerar en fysisk enhet med fel och kan anses noder som tillhör samma fysiska rack. En uppgraderingsdomän (UD) är en grupp av noder som uppgraderas tillsammans under en Serviceuppgradering av (distribution). Replikerna är fördelade mellan Uppdateringsdomäner och Feldomäner i en lagringsskalningsenhet. Den här arkitekturen garanterar att dina data är tillgänglig om ett maskinvarufel påverkar en enda rack eller när noderna uppgraderas under en distribution.

LRS är lägst kostnad replikeringsalternativ och erbjuder de lägsta tillförlitlighet jämfört med andra alternativ. Om en datacenter-nivå (till exempel fire eller överbelasta) redundansplats, vara alla repliker förlorad eller oåterkalleligt. Microsoft rekommenderar för att minska denna risk med zonredundant lagring (ZRS) eller geo-redundant lagring (GRS).

* Om ditt program lagrar data som rekonstrueras enkelt om dataförluster uppstår, kan du välja LRS.
* Vissa program är begränsade till att replikera data bara i ett land på grund av krav för styrning av data. I vissa fall kanske de länkade områdena som data ska replikeras för GRS-konton i ett annat land. Mer information om länkade regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/).
