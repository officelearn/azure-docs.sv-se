---
title: StorSimple-redundans, katastrofåterställning för enheter i 8000-serien | Microsoft Docs
description: Lär dig hur du växlar över din StorSimple-enhet till själva, en annan fysisk enhet eller en molninstallation.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 079a2f153f257040d1899a33c9e255d633e526ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576380"
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Redundans och disaster recovery för din enhet i StorSimple 8000-serien

## <a name="overview"></a>Översikt

Den här artikeln beskriver funktionen enheten växling vid fel för StorSimple 8000-serieenheter och hur den här funktionen kan användas för att återställa StorSimple-enheter om en olycka inträffar. StorSimple använder redundansväxling av enhet för att migrera data från en källenhet i datacenter till en annan målenhet. Riktlinjerna i den här artikeln gäller för fysiska enheter i StorSimple 8000-serien och molninstallationer som kör programvaruversioner uppdatering 3 och senare.

StorSimple använder den **enheter** bladet för att starta enheten failover-funktionen i händelse av en katastrof. Det här bladet visar en lista över alla StorSimple-enheter som är anslutna till din StorSimple Device Manager-tjänsten.

![Enhetsbladet](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Haveriberedskap (DR) och redundansväxling av enhet

I ett scenario med disaster recovery (DR), den primära enheten slutar fungera. StorSimple använder den primära enheten som _källa_ och flyttar den kopplade molnet till en annan _target_ enhet. Den här processen kallas den *redundans*. Följande bild illustrerar växling vid fel.

![Vad som händer i redundansväxling av enhet?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

Målenheten för redundans kan vara en fysisk enhet eller även en molninstallation. Målenheten kan finnas i samma eller en annan geografisk plats än källenheten.

Under redundansen, kan du välja volymbehållare för migrering. StorSimple sedan ändrar ägarskap för de här volymbehållarna från källenheten till målenheten. När volymbehållarna ändra ägarskapet för StorSimple att tar bort de här behållarna från källenheten. När borttagningen är klar kan växla du tillbaka målenheten. _Återställning efter fel_ överför ägarskap tillbaka till den ursprungliga källenheten.

### <a name="cloud-snapshot-used-during-device-failover"></a>Ögonblicksbild av molndata används under redundansväxling av enhet

Efter en DR används den senaste säkerhetskopian av molnet för att återställa data till målenheten. Läs mer på molnögonblicksbilder [använda StorSimple Device Manager-tjänsten för att göra en manuell säkerhetskopia](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup).

På StorSimple 8000-serien är principer för säkerhetskopiering associerade med säkerhetskopior. Om det finns flera principer för säkerhetskopiering för samma volym, väljer principen för säkerhetskopiering med det största antalet volymer med StorSimple. StorSimple använder sedan den senaste säkerhetskopian från den valda säkerhetskopieringsprincipen för att återställa data på målenheten.

Anta att det finns två principer för säkerhetskopiering, *defaultPol* och *customPol*:

* *defaultPol*: En volym *vol1*, körs varje dag kl. 10:30 PM.
* *customPol*: Fyra volymer, *vol1*, *vol2*, *vol3*, *vol4*, körs varje dag med start kl. 10:00.

I det här fallet StorSimple prioriterar för kraschkonsekvens och använder *customPol* eftersom den har flera volymer. Den senaste säkerhetskopian från den här principen används för att återställa data. Mer information om hur du skapar och hanterar principer för säkerhetskopiering, går du till [använda StorSimple Device Manager-tjänsten för att hantera principer för säkerhetskopiering](storsimple-8000-manage-backup-policies-u2.md).

## <a name="common-considerations-for-device-failover"></a>Vanliga överväganden för redundansväxling av enhet

Innan du växlar över en enhet kan du granska följande information:

* Alla volymer i volymbehållarna måste vara offline innan en redundansväxling av enhet startar. I en oplanerad redundans kommer StotSimple volymer automatiskt går offline. Men om du utför en planerad redundansväxling (om du vill testa DR), måste du utföra alla volymer offline.
* Endast de volymbehållare som har en tillhörande ögonblicksbild av molndata visas för Katastrofåterställning. Det måste finnas minst en volymcontainer med en tillhörande molnögonblicksbild att återställa data.
* Om det finns ögonblicksbilder av molnet som sträcker sig över flera volymbehållare, redundansväxlar StorSimple dessa volymbehållare som en uppsättning. I sällsynta fall om det finns lokala ögonblicksbilder som sträcker sig över flera volymbehållare men inte gör det kopplade molnögonblicksbilder, StorSimple misslyckas över lokala ögonblicksbilder och lokala data går förlorade efter DR.
* Tillgängliga målenheter för Katastrofåterställning är enheter som har tillräckligt med utrymme för de valda volymcontainrarna. Alla enheter som inte har tillräckligt med utrymme visas inte som målenheter.
* När du har en DR (under en begränsad tid), kan data åtkomst prestanda påverkas avsevärt, när enheten behöver komma åt data från molnet och lagra den lokalt.

#### <a name="device-failover-across-software-versions"></a>Redundansväxling av enhet över programvaruversioner

En StorSimple Device Manager-tjänsten i en distribution kan ha flera enheter, både fysiska och moln, alla körs olika programvaruversioner.

Använd följande tabell för att avgöra om du växlar över eller växla över till en annan enhet som kör en annan programvaruversion och hur volymtyper beter sig under en Katastrofåterställning.

#### <a name="failover-and-failback-across-software-versions"></a>Redundans och återställning efter fel mellan olika programvaruversioner

| Växla över / Redundansväxla från | Fysisk enhet | Molninstallation |
| --- | --- | --- |
| Uppdatering 3 för att uppdatera 4 |Nivåindelade volymer över nivåer misslyckas. <br></br>Lokalt fixerade volymer redundansväxla som lokalt fixerade. <br></br> Efter en växling vid fel när du skapar en ögonblicksbild på enheten uppdatering 4 [termisk karta-baserade spårning](storsimple-update4-release-notes.md#whats-new-in-update-4) aktiveras. |Lokalt fixerade volymer redundansväxlas som nivåindelade. |
| Uppdatering 4 för att uppdatera 3 |Nivåindelade volymer över nivåer misslyckas. <br></br>Lokalt fixerade volymer redundansväxla som lokalt fixerade. <br></br> Används för att återställa-säkerhetskopieringarna behåller den termiska kartan metadata. <br></br>Termisk karta-baserade spårning är inte tillgänglig i Update 3 efter en återställning efter fel. |Lokalt fixerade volymer redundansväxlas som nivåindelade. |


## <a name="device-failover-scenarios"></a>Enheten redundansscenarier

Om en katastrof kan du växla över din StorSimple-enhet:

* [Till en fysisk enhet](storsimple-8000-device-failover-physical-device.md).
* [Till sig själv](storsimple-8000-device-failover-same-device.md).
* [Till en molninstallation](storsimple-8000-device-failover-cloud-appliance.md).

Föregående artiklarna innehåller detaljerade anvisningar för var och en av de ovanstående redundans-fall.


## <a name="failback"></a>Återställning efter fel

StorSimple stöder också återställning efter fel för uppdatering 3 och senare versioner. Återställning efter fel är bara omvänt redundans, målet blir källan och ursprungliga källenheten under redundansen nu blir målenheten. 

StorSimple igen synkroniserar data tillbaka till den primära platsen, stoppar läsning/skrivning och programaktivitet under återställningen och övergångar tillbaka till den ursprungliga platsen.

När en redundansväxling har slutförts, utför StorSimple följande åtgärder:

* StorSimple rensar de volymbehållare som har redundansväxlats från källenheten.
* StorSimple initierar ett bakgrundsjobb per volymbehållare (redundansväxlats) på källenheten. Om du försöker växla tillbaka medan jobbet pågår, får du ett meddelande om detta. Vänta tills jobbet har slutförts för att starta återställningen.
* Den tid det tar att slutföra borttagningen av volymbehållare beror på olika faktorer som mängden data, åldern på data och hur många säkerhetskopior av den tillgängliga nätverksbandbredden för åtgärden.

Om du planerar redundanstestning eller testa återställning efter fel, rekommenderar vi att du testar volymbehållare med mindre data (GB). Du kan vanligtvis starta återställningen 24 timmar efter redundansen är klar.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

F. **Vad händer om DR misslyckas eller har delvis?**

A. Om DR misslyckas, rekommenderar vi att du försöka igen. Den andra enheten redundansjobb medveten om förloppet för det första jobbet och börjar från den tidpunkten och framåt.

F. **Kan jag ta bort en enhet när enheten redundans pågår?**

A. Du kan inte ta bort en enhet medan en DR pågår. Du kan bara ta bort din enhet när DR är klar. Du kan övervaka jobbförloppet enheten redundans i den **jobb** bladet.

F. **När skräpinsamling startar på källenheten så att den lokala data på källenheten har tagits bort?**

A. Skräpinsamling är aktiverat på källenheten endast när enheten är helt rensad. Rensningen innehåller Rensa objekt som har misslyckats under från källenheten, till exempel volymer, säkerhetskopierade objekt (inte data), volymbehållare och principer.

F. **Vad händer om det inte går att ta bort jobbet som är associerade med volymbehållare i källenheten?**

A.  Om jobbet Ta bort misslyckas, kan du manuellt ta bort volymbehållarna. I den **enheter** bladet välj källenheten och klicka på **volymbehållare**. Välj de volymbehållare som du inte över och längst ned i bladet, klickar du på **ta bort**. När du tar bort alla de över volymbehållare på källenheten, kan du starta återställningen. Mer information går du till [ta bort en volymbehållare](storsimple-8000-manage-volume-containers.md#delete-a-volume-container).

## <a name="business-continuity-disaster-recovery-bcdr"></a>Kontinuitet för företag-haveriberedskap (BCDR)

Ett affärskontinuitet disaster recovery (BCDR) affärsscenario inträffar när hela Azure-datacentret slutar att fungera. Det här scenariot kan påverka din StorSimple Device Manager-tjänsten och de associera StorSimple-enheterna.

Om en StorSimple-enhet har registrerats precis innan en katastrof har inträffat, kan den här enheten måste genomgå en fabriksåterställning. Efter en katastrof, StorSimple-enheten som visas i Azure portal som offline. Den här enheten måste tas bort från portalen. Återställa enheten till fabriksinställningarna och registrera den igen med tjänsten.

## <a name="next-steps"></a>Nästa steg

Välj något av följande scenarier för detaljerade anvisningar om du är redo att utföra en redundansväxling av enhet:

- [Växla över till en annan fysisk enhet](storsimple-8000-device-failover-physical-device.md)
- [Växla över till samma enhet](storsimple-8000-device-failover-same-device.md)
- [Växla över till StorSimple-Molninstallation](storsimple-8000-device-failover-cloud-appliance.md)

Om du har redundansväxlat enheten kan välja någon av följande alternativ:

* [Inaktivera eller ta bort din StorSimple-enhet](storsimple-8000-deactivate-and-delete-device.md).
* [Använda StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

