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
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
ms.locfileid: "30928717"
---
Lokalt redundant lagring (LRS) är utformad att ge minst 99.999999999% (11 9's) hållbarhet objekt under ett visst år genom att replikera data inom en lagringsenhet för skalan. En skalningsenhet för lagring finns i ett datacenter i den region där du skapade ditt lagringskonto. Write-förfrågan till ett LRS-lagringskonto returnerar har endast efter att data har skrivits till alla repliker. De här replikeringarna varje finns i separata feldomäner och uppdatera domäner inom en lagringsenhet för skalan.

En skalningsenhet för lagring är en samling av rack lagringsnoder. En feldomän (FD) är en uppsättning noder som representerar en fysisk enhet till felet och kan anses noder som tillhör samma fysiska rack. En uppgraderingsdomän (UD) är en uppsättning noder som uppgraderas tillsammans under service-uppgraderingen (distribution). Replikerna sprids över UDs och FDs inom en lagringsenhet för skalan. Den här arkitekturen garanterar att dina data är tillgänglig om ett maskinvarufel påverkar ett enda rack eller när noder uppgraderas under en distribution.

LRS är lägst kostnad replikeringsalternativ och är minst hållbarhet jämfört med andra alternativ. Om en datacenter-nivå för katastrofåterställning (till exempel brand eller översvämning) visas kan alla repliker saknas eller vara oåterkalleligt. För att minska denna risk Microsoft rekommenderar att du använder zonredundant lagring (ZRS) eller geo-redundant lagring (GRS).

* Om ditt program lagrar data som enkelt kan rekonstrueras om dataförluster uppstår, kan du välja LRS.
* Vissa program är begränsade till att replikera data bara inom ett land på grund av kraven för styrning. I vissa fall kanske parad regioner som data ska replikeras för GRS-konton i ett annat land. Mer information om parad regioner finns [Azure-regioner](https://azure.microsoft.com/regions/).
