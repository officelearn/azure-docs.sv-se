---
title: "StorSimple-redundans, katastrofåterställning för enheter i 8000-serien | Microsoft Docs"
description: "Lär dig mer om att växla över din StorSimple-enhet till sig själv, en annan fysisk enhet eller en enhet i molnet."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 5dc4a98bf889d38c62c76364289c2d58c14d771e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Redundans och disaster recovery för enheten StorSimple 8000-serien

## <a name="overview"></a>Översikt

Den här artikeln beskriver funktionen med redundans i enheten för StorSimple 8000-serien enheter och hur den här funktionen kan användas för att återställa StorSimple-enheter om en olycka inträffar. StorSimple använder enheten redundans för att migrera data från en källenheten i datacentret till en annan målenhet. Riktlinjerna i den här artikeln gäller StorSimple 8000-serien fysiska enheter och molnet installationer med programvaruversioner uppdatering 3 och senare.

StorSimple använder den **enheter** bladet för att starta funktionen med redundans i enheten vid en katastrof. Det här bladet visar en lista över alla StorSimple-enheter som är anslutna till din StorSimple Device Manager-tjänst.

![Bladet för enheter](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Katastrofåterställning (DR) och växling vid fel för enheten

I en (DR) katastrofåterställning, den primära enheten slutar fungera. StorSimple använder den primära enheten som _källa_ och flyttar den associerade molnet till en annan _mål_ enhet. Den här processen kallas den *redundans*. Följande bild illustrerar växling vid fel.

![Vad som händer i enheten växling vid fel?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

Målenhet för ett redundanskluster kan vara en fysisk enhet eller även en moln-installation. Målenheten kan finnas i samma eller en annan geografisk plats än källan.

Under växling vid fel, kan du välja volymbehållare för migrering. StorSimple ändras sedan ägarskap för de här volymbehållare från källan till målenheten. När volymbehållarna ändra ägarskap, StorSimple att tar bort dessa behållare från källan. När borttagningen är klar, kan du växla tillbaka på målenheten. _Återställning efter fel_ överför ägarskapet tillbaka till den ursprungliga källa enheten.

### <a name="cloud-snapshot-used-during-device-failover"></a>Ögonblicksbild i molnet används under växling vid fel för enheten

Efter en DR används den senaste säkerhetskopian av molnet för att återställa data till målenheten. Mer information om molnögonblicksbilder finns [använda Enhetshanteraren för StorSimple-tjänsten för att ta en manuell säkerhetskopiering](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup).

Principer för säkerhetskopiering är kopplade till säkerhetskopiering på StorSimple 8000-serien. Om det finns flera principer för säkerhetskopiering för samma volym, väljs StorSimple säkerhetskopieringsprincip med det största antalet volymer. StorSimple använder sedan den senaste säkerhetskopian från den valda säkerhetskopieringsprincipen för att återställa data på målenheten.

Anta att det finns två principer för säkerhetskopiering, *defaultPol* och *customPol*:

* *defaultPol*: en volym *vol1*, kör daglig början på 10:30 PM.
* *customPol*: fyra volymer *vol1*, *vol2*, *vol3*, *vol4*, kör daglig början på 10:00 PM.

I det här fallet StorSimple prioriterar för kraschkonsekvens och använder *customPol* eftersom den har fler volymer. Den senaste säkerhetskopian från den här principen används för att återställa data. Mer information om hur du skapar och hanterar principer för säkerhetskopiering, gå till [använda Enhetshanteraren för StorSimple-tjänsten för att hantera principer för säkerhetskopiering](storsimple-8000-manage-backup-policies-u2.md).

## <a name="common-considerations-for-device-failover"></a>Vanliga överväganden för enheten redundans

Läs igenom följande information innan du växla en enhet:

* Alla volymer i volymbehållarna måste vara offline innan en enhet redundans startar. I en oplanerad redundans StotSimple volymer kommer automatiskt att kopplas från. Men om du utför en planerad redundans (om du vill testa DR), måste du utföra alla volymer offline.
* Endast volymbehållarna som har en associerad ögonblicksbild i molnet visas för Katastrofåterställning. Det måste finnas minst en volymbehållare med en tillhörande moln-ögonblicksbild för återställning av data.
* Om det finns molntjänster ögonblicksbilder som sträcker sig över över flera volymbehållare, växlar StorSimple behållarna volymen som en uppsättning. I sällsynta fall, om lokala ögonblicksbilder som sträcker sig över över flera volymbehållare men inte gör det associerade molnögonblicksbilder, StorSimple misslyckas över lokala ögonblicksbilder och lokala data går förlorade efter DR.
* De tillgängliga målenheterna för Katastrofåterställning är enheter som har tillräckligt med utrymme för de valda volymbehållarna. Alla enheter som inte har tillräckligt med utrymme, visas inte som målenheter.
* Efter en DR (för en begränsad tid), kan data access prestanda påverkas avsevärt när enheten behöver komma åt data från molnet och lagras lokalt.

#### <a name="device-failover-across-software-versions"></a>Enheten redundans över programvaruversioner

En StorSimple Device Manager-tjänst i en distribution kan ha flera enheter, både fysiska och moln, alla kör olika versioner.

Använd följande tabell för att avgöra om du kan växla över eller växla tillbaka till en annan enhet som kör en annan programvaruversion och funktionssättet för volymtyper under Katastrofåterställning.

#### <a name="failover-and-failback-across-software-versions"></a>Redundans och återställning mellan olika programvaruversioner

| Växla över / växla tillbaka från | Fysisk enhet | Molninstallation |
| --- | --- | --- |
| Uppdatering 3 för att uppdatera 4 |Nivåindelade volymer misslyckas över nivåer. <br></br>Lokalt fästa volymer växlar över lokalt Fäst. <br></br> Efter en växling vid fel när du skapar en ögonblicksbild på enheten uppdatering 4 [heatmap-baserade spårning](storsimple-update4-release-notes.md#whats-new-in-update-4) aktiveras. |Lokalt fästa volymer redundans som nivåindelade. |
| Uppdatering 4 för att uppdatera 3 |Nivåindelade volymer misslyckas över nivåer. <br></br>Lokalt fästa volymer växlar över lokalt Fäst. <br></br> Används för att återställa säkerhetskopior behåller heatmap metadata. <br></br>Heatmap-baserade spårning är inte tillgänglig i uppdatering 3 efter en återställning efter fel. |Lokalt fästa volymer redundans som nivåindelade. |


## <a name="device-failover-scenarios"></a>Scenarier för enheter med växling vid fel

Om det finns en katastrof, kan du välja att växla över din StorSimple-enhet:

* [Till en fysisk enhet](storsimple-8000-device-failover-physical-device.md).
* [Till sig själv](storsimple-8000-device-failover-same-device.md).
* [Till en enhet i molnet](storsimple-8000-device-failover-cloud-appliance.md).

Föregående artiklar innehåller detaljerade anvisningar för varje fall som ovan växling vid fel.


## <a name="failback"></a>Återställning efter fel

För uppdatering 3 och senare versioner stöd StorSimple också för återställning efter fel. Återställning är bara omvänt växling vid fel, målet blir källan och den ursprungliga källa enheten under växling vid fel nu blir målenheten. 

StorSimple igen synkroniserar data tillbaka till primär plats, uppehåll i i/o och programaktiviteten under återställning efter fel, och övergångar tillbaka till den ursprungliga platsen.

När en redundansväxlingen är klar, utför StorSimple följande åtgärder:

* StorSimple rensar volymbehållarna från källan har redundansväxlats.
* StorSimple initierar bakgrunden per volymbehållare (redundansväxlats) på källan. Om du försöker växla tillbaka medan jobbet pågår kan få du ett meddelande om detta. Vänta tills jobbet är klart att starta återställningen.
* Den tid det tar att slutföra borttagningen av volymbehållare beror på faktorer som mängden data, åldern på data, antal säkerhetskopior och den tillgängliga bandbredden för åtgärden.

Om du planerar redundanstestningar eller testa återställning efter fel, rekommenderar vi att du testar volymbehållare med mindre data (GB). Vanligtvis kan du starta återställningen 24 timmar efter växling vid fel är klar.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

FRÅGOR. **Vad händer om DR misslyckas eller har lyckats delvis?**

A. Om DR misslyckas, rekommenderar vi att du försöker igen. Andra beställningsjobbet om enheten är medveten om förloppet för det första jobbet och startar från den tidpunkten och framåt.

FRÅGOR. **Kan jag ta bort en enhet när enheten för växling vid fel pågår?**

A. Du kan inte ta bort en enhet medan en DR pågår. Du kan bara ta bort din enhet när ar har slutförts. Du kan övervaka jobbförloppet enheten växling vid fel i den **jobb** bladet.

FRÅGOR. **När startar skräpinsamling på källenheten så att den lokala data på källenheten har tagits bort?**

A. Skräpinsamling är aktiverat på på källenheten endast när enheten är helt rensad. Rensningen innehåller Rensa objekt som har redundansväxlats från källan till exempel volymer, säkerhetskopieobjekt (inte data), volymbehållare och principer.

FRÅGOR. **Vad händer om det inte går att ta bort jobbet som är associerade med volymbehållare i källan?**

A.  Om jobbet Ta bort misslyckas, kan du manuellt ta bort volymbehållarna. I den **enheter** bladet Välj källa enheten och klicka på **volymbehållare**. Välj volymbehållarna som du inte över och längst ned i bladet, klickar du på **ta bort**. När du tar bort alla de över volymbehållare på källenhet, kan du starta återställningen. Mer information finns på [ta bort en volymbehållare](storsimple-8000-manage-volume-containers.md#delete-a-volume-container).

## <a name="business-continuity-disaster-recovery-bcdr"></a>Katastrofåterställning för verksamhetskontinuitet (BCDR)

En business continuity (BCDR) katastrofåterställning inträffar när hela Azure-datacentret slutar att fungera. Det här scenariot kan påverka din StorSimple Device Manager-tjänst och de associera StorSimple-enheterna.

Om en StorSimple-enhet har registrerats precis innan en katastrof inträffade, kan den här enheten måste genomgå en fabriksåterställning. StorSimple-enheten visas i Azure-portalen som offline efter katastrofåterställning. Den här enheten måste tas bort från portalen. Återställa enheten till fabriksinställningarna och registrera den igen med tjänsten.

## <a name="next-steps"></a>Nästa steg

Välj något av följande scenarier för detaljerade anvisningar om du är redo att utföra en växling vid fel för enhet:

- [Växla över till en annan fysisk enhet](storsimple-8000-device-failover-physical-device.md)
- [Växla över till samma enhet](storsimple-8000-device-failover-same-device.md)
- [Växla över till molnet för StorSimple-enhet](storsimple-8000-device-failover-cloud-appliance.md)

Om du har redundansväxlats enheten, Välj något av följande alternativ:

* [Inaktivera eller ta bort din StorSimple-enhet](storsimple-8000-deactivate-and-delete-device.md).
* [Använda Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

