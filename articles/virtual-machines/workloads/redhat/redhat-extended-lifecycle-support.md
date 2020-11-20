---
title: Utökat livscykelstöd för Red Hat Enterprise Linux
description: Läs mer om att lägga till stöd för utökade livscykler för Red Hat Enterprise
author: mathapli
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.reviewer: cynthn
ms.openlocfilehash: 71ff00dbf32ef32ead85be60cdbe53f23c8e35c7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957579"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Red Hat Enterprise Linux (RHEL) Extended Lifecycle support
Den här artikeln innehåller information om Extended Lifecycle-stöd för Red Hat Enterprise-avbildningar:
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Red Hat Enterprise Linux 6-livscykel
Från och med den 30 november 2020 kommer Red Hat Enterprise Linux 6 att uppnå slutet av underhålls fasen. Underhålls fasen följs av fasen Extended Life. Som Red Hat Enterprise Linux 6 över gångar från hela/underhålls faserna rekommenderar vi starkt att du uppgraderar till Red Hat Enterprise Linux 7 eller 8. Om kunderna måste stanna Red Hat Enterprise Linux 6 rekommenderar vi att du lägger till ett ELS-tillägg (Red Hat Enterprise Linux Extended Life Cycle support).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>Jag kör Red Hat Enterprise Linux 6 och kan inte migrera till en senare version för tillfället. Vilka alternativ har jag?
* Fortsätt att köra Red Hat Enterprise Linux 6 och Köp ELS-Add-On (Extended Life Cycle support) för att fortsätta få begränsad program varu underhåll och teknisk support (se avsnittet om uppgradering och pris information nedan).
* Migrera till Red Hat Enterprise Linux 7 eller 8 så snart du kan.

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>Vilken kostar det extra att använda Red Hat Enterprise Linux utökade ELS-tillägg (Support för livs cykel support)?

|Storlek på virtuell dator|Extra avgifts tidsram|Ytterligare belopp i kronor (USD)| Kommentarer|
|---|---|---|---|
| Små virtuella gäster (<= 4 kärnor) | Extra avgift per timme | TBC | |
|  | Månatlig ytterligare kostnad | TBC | För reserverade instanser |
|  | Årlig ytterligare kostnad | TBC | För reserverade instanser |
| Stora virtuella gäster (>4 kärnor) | Extra avgift per timme | TBC | |
|  | Månatlig ytterligare kostnad | TBC | För reserverade instanser |
|  | Årlig ytterligare kostnad | TBC | För reserverade instanser |

#### <a name="what-is-the-process-to-add-extended-life-cycle-support-els-repositories-to-continue-to-receive-software-maintenance-bug-and-security-fixes-and--support-for-red-hat-enterprise-linux-6"></a>Vad är processen för att lägga till ELS-databaser (Extended Life Cycle support) för att fortsätta att ta emot program varu underhåll (fel-och säkerhets korrigeringar) och stöd för Red Hat Enterprise Linux 6?

End to end-processen för att registrera sig för ELS kommer snart att vara tillgänglig här (senaste senast 30 november 2020).

## <a name="next-steps"></a>Nästa steg

* Om du vill visa en fullständig lista över RHEL-avbildningar i Azure, se [Red Hat Enterprise Linux (RHEL)-avbildningar som är tillgängliga i Azure](./redhat-imagelist.md).
* Mer information om Azure Red Hat-uppdateringen finns i [Red Hat-uppdaterings infrastruktur för virtuella datorer på begäran RHEL i Azure](./redhat-rhui.md).
* Om du vill veta mer om erbjudandet om RHEL BYOS kan du Red Hat Enterprise Linux läsa mer [i få guld avbildningar för prenumerationer i Azure](./byos.md).
* Information om Red Hat support-principer för alla versioner av RHEL finns i [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata).