---
title: Redundans och haveriberedskap för enheter i StorSimple 8000-serien
description: Lär dig hur du växlar över din StorSimple-enhet till sig själv, en annan fysisk enhet eller en molninstallation.
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
ms.openlocfilehash: c1acc084d5abe3385fe311873dfd64c9009e83f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254968"
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Växling vid fel och haveriberedskap för enhet i StorSimple 8000-serien

## <a name="overview"></a>Översikt

I den här artikeln beskrivs enhetens redundansfunktion för StorSimple 8000-serieenheterna och hur den här funktionen kan användas för att återställa StorSimple-enheter om en katastrof inträffar. StorSimple använder enhetsväxling för att migrera data från en källenhet i datacentret till en annan målenhet. Vägledningen i den här artikeln gäller fysiska enheter i StorSimple 8000-serien och molninstallationer som kör programversioner uppdatering 3 och senare.

StorSimple använder bladet **Enheter** för att starta enhetens redundansfunktion i händelse av en katastrof. Det här bladet listar alla StorSimple-enheter som är anslutna till tjänsten StorSimple Device Manager.

![Enhetsblad](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Katastrofåterställning (DR) och enhetsväxling

I ett DR-scenario (Disaster Recovery) slutar den primära enheten att fungera. StorSimple använder den primära enheten som _källa_ och flyttar associerade molndata till en annan _målenhet._ Den här processen kallas *redundans*. Följande bild illustrerar processen för redundans.

![Vad händer i enhetens redundans?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

Målenheten för en redundans kan vara en fysisk enhet eller till och med en molninstallation. Målenheten kan finnas på samma eller en annan geografisk plats än källenheten.

Under redundansen kan du välja volymbehållare för migrering. StorSimple ändrar sedan ägarskapet för dessa volymbehållare från källenheten till målenheten. När volymbehållarna har bytt ägare tar StorSimple bort behållarna från källenheten. När borttagningen är klar kan du återställa målenheten. _Återställningen_ av återställningen överför ägarskapet tillbaka till den ursprungliga källenheten.

### <a name="cloud-snapshot-used-during-device-failover"></a>Ögonblicksbild av molnet som används vid enhetsväxling

Efter en DR används den senaste säkerhetskopieringen i molnet för att återställa data till målenheten. Mer information om ögonblicksbilder i molnet finns i [Använda Tjänsten StorSimple Device Manager för att göra en manuell säkerhetskopiering](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup).

I en StorSimple 8000-serie associeras säkerhetskopieringsprinciper med säkerhetskopior. Om det finns flera principer för säkerhetskopiering för samma volym väljer StorSimple säkerhetskopieringsprincipen med det största antalet volymer. StorSimple använder sedan den senaste säkerhetskopian från den valda säkerhetskopieringsprincipen för att återställa data på målenheten.

Anta att det finns två principer för säkerhetskopiering, *defaultPol* och *customPol:*

* *defaultPol*: En volym, *vol1*, körs dagligen från 22:30.
* *customPol*: Fyra volymer, *vol1*, *vol2*, *vol3*, *vol4*, körs dagligen från 10:00.

I det här fallet prioriterar StorSimple för kraschkonsekvens och använder *customPol* eftersom den har fler volymer. Den senaste säkerhetskopian från den här principen används för att återställa data. Mer information om hur du skapar och hanterar principer för säkerhetskopiering finns i [Använda Tjänsten StorSimple Device Manager för att hantera principer för säkerhetskopiering](storsimple-8000-manage-backup-policies-u2.md).

## <a name="common-considerations-for-device-failover"></a>Vanliga överväganden för enhetsväxling

Innan du växlar över en enhet läser du följande information:

* Innan en enhetsväxling startar måste alla volymer i volymbehållarna vara offline. I en oplanerad redundans går StotSimple-volymer automatiskt offline. Men om du utför en planerad redundans (för att testa DR) måste du ta alla volymer offline.
* Endast volymbehållare som har en associerad ögonblicksbild av molnet visas för DR. Det måste finnas minst en volymbehållare med en associerad ögonblicksbild av molnet för att återställa data.
* Om det finns ögonblicksbilder av molnet som sträcker sig över flera volymbehållare misslyckas StorSimple över dessa volymbehållare som en uppsättning. I en sällsynt instans, om det finns lokala ögonblicksbilder som sträcker sig över flera volymbehållare men associerade ögonblicksbilder av molnet inte, misslyckas StorSimple över de lokala ögonblicksbilderna och de lokala data går förlorade efter DR.
* De tillgängliga målenheterna för DR är enheter som har tillräckligt med utrymme för att rymma de valda volymbehållarna. Alla enheter som inte har tillräckligt med utrymme visas inte som målenheter.
* Efter en DR (under en begränsad tid) kan dataåtkomstprestandan påverkas avsevärt, eftersom enheten måste komma åt data från molnet och lagra dem lokalt.

#### <a name="device-failover-across-software-versions"></a>Enhetsväxling över programvaruversioner

En StorSimple Device Manager-tjänst i en distribution kan ha flera enheter, både fysiska och moln, som alla kör olika programvaruversioner.

Använd följande tabell för att avgöra om du kan växla över eller växla tillbaka till en annan enhet som kör en annan programvaruversion och hur volymtyperna beter sig under DR.

#### <a name="failover-and-failback-across-software-versions"></a>Redundans och redundans över programvaruversioner

| Växla över/ Växla tillbaka från | Fysisk enhet | Molninstallation |
| --- | --- | --- |
| Uppdatering 3 till uppdatering 4 |Nivåindelad volym växlas över som nivåindelad. <br></br>Lokalt fästa volymer växlas över som lokalt fäst. <br></br> Efter en redundans när du tar en ögonblicksbild på Update [4-enheten, värmekarta-baserade spårning](storsimple-update4-release-notes.md#whats-new-in-update-4) sparkar i. |Lokalt fästa volymer växlas över som nivåindelad. |
| Uppdatering 4 till uppdatering 3 |Nivåindelad volym växlas över som nivåindelad. <br></br>Lokalt fästa volymer växlas över som lokalt fäst. <br></br> Säkerhetskopior som används för att återställa behålla heatmap-metadata. <br></br>Heatmap-baserad spårning är inte tillgänglig i uppdatering 3 efter en återställning efter fel. |Lokalt fästa volymer växlas över som nivåindelad. |


## <a name="device-failover-scenarios"></a>Scenarier för enhetsväxling

Om det inträffar en katastrof kan du välja att växla över Din StorSimple-enhet:

* [Till en fysisk enhet](storsimple-8000-device-failover-physical-device.md).
* [För sig själv](storsimple-8000-device-failover-same-device.md).
* [Till en molninstallation](storsimple-8000-device-failover-cloud-appliance.md).

De föregående artiklarna innehåller detaljerade steg för vart och ett av ovanstående redundansfall.


## <a name="failback"></a>Återställning efter fel

För uppdatering 3 och senare versioner stöder StorSimple även återställning efter fel. Redundans är bara baksidan av redundans, blir målet källan och den ursprungliga källenheten under redundansen blir nu målenheten. 

Under återställningen av fel kan StorSimple tillbaka synkronisera data tillbaka till den primära platsen, stoppar I/O- och programaktiviteten och övergår till den ursprungliga platsen.

När en redundans har slutförts utför StorSimple följande åtgärder:

* StorSimple rensar volymbehållarna som har misslyckats över från källenheten.
* StorSimple initierar ett bakgrundsjobb per volymbehållare (misslyckades över) på källenheten. Om du försöker återställa problemet igen medan jobbet pågår får du ett meddelande om detta. Vänta tills jobbet är klart för att starta återställningen.
* Hur lång tid det tar att slutföra borttagningen av volymbehållare beror på olika faktorer, till exempel mängden data, datans ålder, antalet säkerhetskopior och den nätverksbandbredd som är tillgänglig för åtgärden.

Om du planerar test failovers eller testa failbacks rekommenderar vi att du testar volymbehållare med mindre data (Gbs). Vanligtvis kan du starta återställningen 24 timmar efter att redundansen är klar.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

F. **Vad händer om DR misslyckas eller har partiell framgång?**

A. Om DR misslyckas rekommenderar vi att du försöker igen. Det andra redundansjobbet för enheten är medvetet om förloppet för det första jobbet och startar från den punkten och framåt.

F. **Kan jag ta bort en enhet medan enheten redundans pågår?**

A. Du kan inte ta bort en enhet medan en DR pågår. Du kan bara ta bort enheten när DR har slutförts. Du kan övervaka hur enheten redundansjobbet fortskrider i bladet **Jobb.**

F. **När startar skräpinsamlingen på källenheten så att de lokala data på källenheten tas bort?**

A. Skräpinsamling är aktiverat på källenheten först när enheten är helt rengjord. Rensningen omfattar rensning av objekt som har misslyckats över från källenheten, till exempel volymer, säkerhetskopieringsobjekt (inte data), volymbehållare och principer.

F. **Vad händer om borttagningsjobbet som är associerat med volymbehållarna i källenheten misslyckas?**

A.  Om borttagningsjobbet misslyckas kan du ta bort volymbehållarna manuellt. I bladet **Enheter** väljer du källenheten och klickar på **Volymbehållare**. Markera de volymbehållare som du misslyckades med och klicka på **Ta bort**i bladets undersida. När du har tagit bort alla misslyckade volymbehållare på källenheten kan du starta återställningen. Mer information finns i [Ta bort en volymbehållare](storsimple-8000-manage-volume-containers.md#delete-a-volume-container).

## <a name="business-continuity-disaster-recovery-bcdr"></a>Återställning av haveuliker för affärskontinuitet (BCDR)

Ett SCENARIO för återställning av affärskontinuitetskatastrof (BCDR) inträffar när hela Azure-datacentret slutar fungera. Det här scenariot kan påverka tjänsten StorSimple Device Manager och tillhörande StorSimple-enheter.

Om en StorSimple-enhet registrerades strax innan en katastrof inträffade kan den här enheten behöva genomgå en fabriksåterställning. Efter katastrofen visas StorSimple-enheten i Azure-portalen som offline. Den här enheten måste tas bort från portalen. Återställ enheten till fabriksinställningar och registrera den igen med tjänsten.

## <a name="next-steps"></a>Nästa steg

Om du är redo att utföra en enhetsväxling väljer du något av följande scenarion för detaljerade instruktioner:

- [Växla över till en annan fysisk enhet](storsimple-8000-device-failover-physical-device.md)
- [Växla över till samma enhet](storsimple-8000-device-failover-same-device.md)
- [Växla över till StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md)

Om du har misslyckats över enheten väljer du något av följande alternativ:

* [Inaktivera eller ta bort StorSimple-enheten](storsimple-8000-deactivate-and-delete-device.md).
* [Använd Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

