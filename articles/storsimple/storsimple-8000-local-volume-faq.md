---
title: StorSimple lokalt fixerade volymer vanliga frågor och svar | Microsoft Docs
description: Innehåller svar på vanliga frågor och svar om StorSimple lokalt fixerade volymer.
services: storsimple
documentationcenter: NA
author: manuaery
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2017
ms.author: manuaery
ms.openlocfilehash: aa69d8b07d31b5cf0386e34c113475cbf4191891
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58013796"
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple lokalt fixerade volymer: vanliga frågor (och svar FAQ)
## <a name="overview"></a>Översikt
Här följer frågor och svar som kan uppstå när du skapar en StorSimple lokalt Fäst volym, konvertera en nivåindelad volym till en lokalt Fäst volym (och vice versa), eller säkerhetskopiera och återställa en lokalt Fäst volym.

Frågor och svar är ordnade i följande kategorier

* Skapa en lokalt Fäst volym
* Säkerhetskopiera en lokalt Fäst
* Konvertera en nivåindelad volym till en lokalt Fäst volym
* Återställa en lokalt Fäst volym
* Redundansväxla en lokalt Fäst volym

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Frågor om hur du skapar en lokalt Fäst volym
**F.** Vad är den maximala storleken för en lokalt Fäst volym som jag kan skapa på 8000-serieenheter?

**En** på enheter som kör StorSimple 8000 Series Update 3.0 kan du etablera lokalt fästa volymer upp till 8,5 TB, eller nivåindelade volymer upp till 200 TB på 8100-enheten. På den större 8600-enheten kan du etablera lokalt fästa volymer upp till 22,5 TB, eller nivåindelade volymer upp till 500 TB.

**F.** Jag har nyligen uppgraderat min 8100-enhet till Update 3.0 och när jag försöker skapa en lokalt Fäst volym, allt tillgängligt utrymme endast 6 TB och inte 8,5 TB. Varför kan jag skapa en volym på 8,5 TB?

**En** om enheten kör update 3.0, kan du etablera lokalt fästa volymer upp till 8,5 TB eller nivåindelade volymer upp till 200 TB på 8100-enheten. Om enheten redan har nivåindelade volymer, så det tillgängliga utrymmet för att skapa en lokalt Fäst volym kommer att vara proportionellt lägre än den här högsta gränsen. Till exempel om cirka 106 TB nivåindelade volymer har redan etablerats på din 8100-enhet (som är hälften av nivåindelad kapacitet), reduceras sedan den maximala storleken för en lokal volym som du kan skapa på 8100-enhet på motsvarande sätt till 4 TB (ungefär hälften av maximalt lokalt Fäst volymkapacitet).

Eftersom vissa lokalt utrymme på enheten används för att hålla arbetsuppsättningen med nivåindelade volymer, minskar det tillgängliga utrymmet för att skapa en lokalt Fäst volym om enheten har nivåindelade volymer. Däremot minskar skapa en lokalt Fäst volym proportionellt det tillgängliga utrymmet för nivåindelade volymer. Följande tabeller sammanfattar de tillgängliga nivåindelad kapaciteten på 8100 och 8600-enheter när lokalt fixerade volymer skapas.

#### <a name="update-30"></a>Uppdatera 3.0 

| Lokalt fixerade volymer etablerad kapacitet | Tillgänglig kapacitet som ska etableras för nivåindelade volymer – 8100 | Tillgänglig kapacitet som ska etableras för nivåindelade volymer – 8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176.5 TB |477.8 TB |
| 4 TB |105.9 TB |411.1 TB |
| 8,5 TB |0 TB |311.1 TB |
| 10 TB |Ej tillämpligt |277.8 TB |
| 15 TB |Ej tillämpligt |166.7 TB |
| 22,5 TB |Ej tillämpligt |0 TB |

**F.** Varför är lokalt fixerade volymer kan skapas en tidskrävande åtgärd?

**S.** Lokalt Fäst volym etableras tjockt. För att skapa utrymme på de lokala nivåerna för enheten, kan vissa data från befintliga nivåindelade volymer skickas till molnet under etableringen. Och eftersom detta beror på storleken på den volym som håller på att etableras, befintliga data på enheten och den tillgängliga bandbredden till molnet och den tid det tar att skapa en lokal volym kanske flera timmar.

**F.** Hur lång tid tar det för att skapa en lokalt Fäst volym?

**S.** Eftersom lokalt Fäst volym etableras tjockt, kan vissa befintliga data från nivåindelade volymer skickas till molnet under etableringen. Därför kan beror den tid det tar att skapa en lokalt Fäst volym på flera faktorer, bland annat paketets storlek på volymen, data på enheten och den tillgängliga bandbredden. På nyligen installerade enheter som har inga volymer, är tiden för att skapa en lokalt Fäst volym cirka 10 minuter per terabyte data. Skapandet av lokala volymer kan dock ta flera timmar beroende på faktorer som beskrivs ovan på en enhet som används.

**F.** Jag vill skapa en lokalt Fäst volym. Finns det några metodtips som jag behöver känna till?

**S.** Lokalt fixerade volymer är lämpliga för arbetsbelastningar som kräver lokala garantier av data vid alla tidpunkter och är känsliga för fördröjningar i molnet. Överväga var användning av lokala volymer för någon av dina arbetsbelastningar medveten om följande:

* Lokalt Fäst volym etableras tjockt och skapa lokala volymer påverkar det tillgängliga utrymmet för nivåindelade volymer. Därför kan föreslår vi att du börjar med mindre volymer och skala upp när dina storage krav ökar.
* Etablering av lokala volymer är en tidskrävande åtgärd som kan handla om push-överför befintliga data från nivåindelade volymer till molnet. Därför kan uppstå det nedsatt prestanda på dessa volymer.
* Etablering av lokala volymer är en tidskrävande åtgärd. Den faktiska tiden som är inblandade beror på flera faktorer: storleken på volymen som håller på att etableras, data på din enhet och tillgänglig bandbredd. Om du inte har säkerhetskopierat dina befintliga volymer till molnet är skapa volymer långsammare. Vi rekommenderar att du ta ögonblicksbilder av molnet för dina befintliga volymer innan du etablerar en lokal volym.
* Du kan konvertera befintliga nivåindelade volymer till lokalt fixerade volymer och den här konverteringen innefattar etablering av utrymme på enheten för den resulterande lokalt fixerad volymen (förutom att stänga av nivåindelade data från molnet). Igen, det här är en tidskrävande åtgärd som beror på faktorer som har beskrivits här ovan. Vi rekommenderar att du säkerhetskopierar dina befintliga volymer innan konverteringen som processen kommer att även långsammare om befintliga volymer inte som säkerhetskopieras. Enheten kan även uppstå nedsatt prestanda under den här processen.

Mer information om hur du [skapar en lokalt Fäst volym](storsimple-8000-manage-volumes-u2.md#add-a-volume)

**F.** Kan jag skapa flera lokalt fixerade volymer på samma gång?

**S.** Ja, men alla lokalt fixerad volym skapandet och expansion jobb utförs i ordningsföljd.

Lokalt Fäst volym etableras tjockt, vilket kräver skapandet av lokalt utrymme på enheten (vilket kan resultera i att befintliga data från nivåindelade volymer att överföras till molnet under etableringen). Därför, om en Etableringsjobbet pågår andra jobb för skapande av lokal volym placeras i kö tills jobbet har slutförts.

På samma sätt, om en befintlig lokal volym utökas eller en nivåindelad volym konverteras till en lokalt Fäst volym, sedan skapandet av en ny lokalt Fäst volym är i kö tills det föregående jobbet har slutförts. Utöka storleken på en lokalt Fäst volym innebär att du klickat på det befintliga lokala utrymmet för volymen. Konverteringen från nivåindelad till lokalt fixerade volymen omfattar även skapandet av lokalt utrymme för den resulterande lokalt Fäst volym. I båda dessa åtgärder, skapa eller expansion av lokalt utrymme lång körs jobbet.

Du kan visa dessa jobb i den **jobb** bladet för StorSimple Device Manager-tjänsten. Jobb som bearbetas aktivt uppdateras kontinuerligt för att återspegla förloppet för etablering av utrymme. De återstående lokalt fixerad volym-jobb är markerad som körs, men framstegen har stoppats och de plockas i den ordning som de har ställts i kö.

**F.** Jag har tagit bort en lokalt Fäst volym. Varför ser jag det återvunnet utrymmet som visas i det tillgängliga utrymmet när jag försöker skapa en ny volym?

**S.** Om du tar bort en lokalt Fäst volym kan det tillgängliga utrymmet för nya volymer inte uppdateras direkt. StorSimple Device Manager-tjänsten uppdaterar lokalt tillgängligt utrymme ungefär varje timme. Vi rekommenderar att du väntar på en timme innan du försöker skapa den nya volymen.

**F.** Lokalt fixerade volymer stöds på molninstallationen?

**S.** Lokalt fixerade volymer stöds inte för molninstallationen (8010 och 8020-enheter samlingsnamnet StorSimple-enhet).

**F.** Kan jag använda Azure PowerShell-cmdlets för att skapa och hantera lokalt fixerade volymer?

**S.** Nej, du kan inte skapa lokalt fixerade volymer via Azure PowerShell-cmdletar (alla volymer som du skapar via Azure PowerShell är nivåindelad). Vi rekommenderar också att du inte använder Azure PowerShell-cmdletar för att ändra några egenskaper för en lokalt Fäst volym, eftersom det skulle ha oönskad leda till att volymtypen till nivåindelad.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Frågor om hur du säkerhetskopierar en lokalt Fäst volym
**F.** Är lokala ögonblicksbilder av lokalt fixerade volymer stöds?

**S.** Ja, kan du dra lokala ögonblicksbilder av dina lokalt fixerade volymer. Men rekommenderar vi starkt att du regelbundet säkerhetskopierar dina lokalt fixerade volymer med ögonblicksbilder av molnet så att dina data är skyddade i fall av ett haveri.

Observera att lokala ögonblicksbilder av lokalt fixerade volymer kan också dela nivåer ut till molnet och garanteras inte vara i den lokala nivån på enheten.

**F.** Finns det några riktlinjer för att hantera lokala ögonblicksbilder för lokalt fästa volymer?

**S.** Frekventa lokala ögonblicksbilder samt en hög andel dataomsättning i lokalt fixerad volym kan orsaka lokalt utrymme på enheten förbrukas snabbt och leda till data från nivåindelade volymer som läggs till molnet. Vi rekommenderar därför att du minimera antalet lokala ögonblicksbilder.

**F.** Jag har fått en varning om att min lokala ögonblicksbilder av lokalt fixerade volymer kan vara ogiltig. När kan detta inträffa?

**S.** Frekventa lokala ögonblicksbilder samt en hög andel dataomsättning i lokalt fixerad volym kan orsaka lokalt utrymme på enheten förbrukas snabbt. Om de lokala nivåerna för enheten används mycket, ett extra molnkontroll avbrott kan resultera i att enheten blir fulla och inkommande skrivning till volymen kan resultera i ogiltigförklaring av ögonblicksbilder (som finns inget utrymme för att uppdatera ögonblicksbilder att referera till de äldre block med data som har skrivits över). I en sådan situation skrivning till volymen kommer att fortsätta hanteras, men de lokala ögonblicksbilderna kan vara ogiltig. Det finns ingen inverkan på din befintliga ögonblicksbilder av molnet.

Varning varning som meddelar dig att en sådan situation kan uppstå och se till att du har gått samma inom en rimlig genom att antingen granska dina lokala ögonblicksbilder scheman för att ta mindre frekventa lokala ögonblicksbilder eller ta bort äldre lokala ögonblicksbilder som inte längre behövs.

Om de lokala ögonblicksbilderna är ogiltig, får du en avisering för information som meddelar dig om att de lokala ögonblicksbilderna för specifika säkerhetskopieringspolicyn har ogiltigförklarats tillsammans med listan över tidsstämplar lokala ögonblicksbilder som ogiltigförklarades. De här ögonblicksbilderna blir automatiskt tas bort och du kommer inte längre att kunna se dem i den **säkerhetskopiering kataloger** bladet i Azure-portalen.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Frågor om hur du konverterar en nivåindelad volym till en lokalt Fäst volym
**F.** Jag får vissa långsamhet på enheten vid konvertering av en nivåindelad volym till en lokalt Fäst volym. Varför är det fortfarande händer?

**S.** Processen omfattar två steg:

1. Etablering av utrymme på enheten för den snart-till--konverteras lokalt Fäst volym.
2. Ladda ned alla nivåindelade data från molnet för att se till att lokala garanterar.

Båda de här stegen är långa åtgärder som är beroende av storleken på volymen som konverteras data på enheten och tillgänglig bandbredd. Eftersom vissa data från befintliga nivåindelade volymer kan läcker över till molnet som en del av etableringen, kan enheten uppleva nedsatt prestanda under den här tiden. Dessutom kan processen kan ta längre tid om:

* Befintliga volymer har inte säkerhetskopierats till molnet. så vi rekommenderar att du säkerhetskopierar dina volymer innan du påbörjar en konvertering.
* Principer för begränsning av bandbredd har tillämpats, vilket kan begränsa den tillgängliga bandbredden till molnet. Vi rekommenderar därför att du har en dedikerad 40 Mbit/s eller mer anslutning till molnet.
* Processen kan ta flera timmar beror på flera faktorer som beskrivs ovan. Vi rekommenderar därför att du utför den här åtgärden under icke-det högsta antalet gånger eller på en helg att undvika påverkan på slutet konsumenter.

Mer information om hur du [konvertera en nivåindelad volym till en lokalt Fäst volym](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

**F.** Kan jag säga upp konvertering volymen?

**S.** Nej, du kan inte Avbryt konvertering initieras en gång. Som beskrivs i föregående fråga kan var medveten om potentiella prestandaproblem som du kan stöta på under processen och följ rekommenderade metoder som anges ovan när du planerar din konvertering.

**F.** Vad händer med Mina volym om konvertering misslyckas?

**S.** Volymkonvertering kan misslyckas på grund av anslutningsproblem för molnet. Enheten kan upphöra processen när du har ett antal misslyckade försök att påverkar nivåindelade data från molnet. I ett sådant scenario volymtypen fortsätter att vara källtyp för volymen innan konverteringen, och:

* En kritisk varning aktiveras för att meddela dig om felet för konvertering av volymen. Mer information om [aviseringar relaterade till lokalt fixerade volymer](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts)
* Om du konverterar en nivåindelad till en lokalt Fäst volym, fortsätter volymen kan visa egenskaperna för en nivåindelad volym eftersom data kan fortfarande finnas i molnet. Vi rekommenderar att du löser problem med nätverksanslutningen och försök sedan konvertera igen.
* När konvertering från en lokalt Fäst till en nivåindelad volym inte, även om volymen kommer att markeras som en lokalt Fäst volym, fungerar det på samma sätt som en nivåindelad volym (eftersom data kan ha hamnat i molnet). Men fortsätter att uppta utrymmet på de lokala nivåerna på enheten. Här är inte tillgänglig för andra lokalt fixerade volymer. Vi rekommenderar att du gör om åtgärden för att säkerställa att volymen konverteringen har slutförts och det lokala utrymmet på enheten kan vara frigöras.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Frågor om hur du återställer en lokalt Fäst volym
**F.** Är lokalt fixerade volymer återställs direkt?

**S.** Ja, lokalt fixerade volymer återställs direkt. När metadata-information för volymen hämtas från molnet som en del av återställningen, volymen är online och kan nås av värden. Dock nedsatt lokala garantier för volymen data visas inte förrän alla data har hämtats från molnet och kan uppstå prestanda på dessa volymer under återställningen.

**F.** Hur lång tid tar det för att återställa en lokalt Fäst volym?

**S.** Lokalt fixerade volymer återställs direkt och tas online när volyminformation för metadata har hämtats från molnet, medan volymdata fortsätter att laddas ned i bakgrunden. Den här senare delen av återställningen – få tillbaka de lokala garantierna för volymdata – är en tidskrävande åtgärd och kan ta flera timmar för alla data görs lokala igen. Den tid det tar att slutföra samma beror på flera faktorer, till exempel storleken på den volym som håller på att återställas och den tillgängliga bandbredden. Om den ursprungliga volymen som håller på att återställas har tagits bort, tas extra tid att skapa det lokala utrymmet på enheten som en del av återställningen.

**F.** Jag behöver återställa Mina befintliga lokalt fixerad volym till en äldre ögonblicksbilden (när volymen har nivåer). Volymen som ska återställas som nivåer i det här fallet?

**S.** Nej, kommer volymen att återställas som en lokalt Fäst volym. Även om datumen ögonblicksbild till den tidpunkt när volymen har nivåer, vid återställning befintliga volymer, StorSimple använder alltid typ av volym på disken eftersom den finns för närvarande.

**F.** Jag nyligen utökade min lokalt Fäst volym, men jag nu måste du återställa data till en tidpunkt när volymen har mindre storlek. Ändras storleken återställning på den aktuella volymen och behöver jag utöka storleken på volymen när återställningen är klar?

**S.** Ja, återställningen ändras storleken på volymen och måste du utöka storleken på volymen när återställningen är klar.

**F.** Kan jag ändra typen av en volym under återställning?

**S.** Nej, du kan inte ändra typ av volym under återställning.

* Volymer som har tagits bort återställs som den typ som sparades i ögonblicksbilden.
* Befintliga volymer återställs baserat på aktuella typ, oberoende av vilken typ som sparades i ögonblicksbilden (se föregående två frågor).

**F.** Jag behöver återställa Mina lokalt Fäst volym, men jag har valt en felaktig tidpunkt i ögonblicksbild. Kan jag säga upp återställningen?

**S.** Ja, du kan avbryta en pågående återställning. Status för volymen kommer att återställas till tillståndet i början av återställningen. Men går alla skrivningar som har gjorts för volymen under återställningen förlorade.

**F.** Jag började en restore-åtgärd på en av Mina lokalt fixerade volymer och nu visas en ögonblicksbild i min eftersläpning för katalogen som jag inte recollect skapar. Vad används det för?

**S.** Det här är den tillfälliga ögonblicksbilden som skapades före återställningen och används för återställning om återställningen avbryts eller misslyckas. Ta inte bort den här ögonblicksbilden; det tas automatiskt bort när återställningen är slutförd. Detta kan inträffa om din Återställningsjobbet har endast lokalt fixerade volymer eller en blandning av lokalt fixerade och nivåindelade volymer. Om återställningsjobbet innehåller endast nivåindelade volymer, utförs inte det här beteendet.

**F.** Kan jag klona en lokalt Fäst volym?

**S.** Ja, det kan du. Dock kommer lokalt fixerad volym att klonas när en nivåindelad volym som standard. Mer information om hur du [klona en lokalt Fäst volym](storsimple-8000-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Frågor om hur du återställer via en lokalt Fäst volym
**F.** Jag vill redundansväxla enheten till en annan fysisk enhet. Min lokalt fästa volymer att växlas över lokalt fixerade eller nivåer?

**S.** Lokalt fixerade volymer har redundansväxlats som lokalt fixerade om målenheten kör StorSimple 8000 uppdatering 3 eller högre.

Mer information om [redundans och DR av lokalt fixerade volymer mellan versioner](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**F.** Återställs lokalt fixerade volymer direkt under haveriberedskap (DR)?

**S.** Ja, lokalt fixerade volymer återställs direkt under redundansväxlingen. När metadata-information för volymen hämtas från molnet som en del av redundansväxlingen, volymen är online på målenheten och kan nås av värden. Under tiden kan volymdata fortsätter att ladda ned i bakgrunden och nedsatt prestanda på dessa volymer för varaktigheten för växling vid fel kan uppstå.

**F.** Jag ser redundansväxlingen har slutförts, hur kan jag följa förloppet av lokalt fixerade volymer som återställs på målenheten?

**S.** Under en redundansåtgärd har redundansväxlingen markerats som en gång slutföra alla volymer i redundansuppsättning har återställts och tas online på målenheten direkt. Detta inkluderar alla lokalt fixerade volymer som kanske har redundansväxlats; dock är lokala garantier av data endast tillgänglig när alla data på volymen har laddats ned. Du kan följa den här förloppet för varje lokalt fixerad volym som har inte över av övervakning av motsvarande återställningsjobb som skapas som en del av redundansen. Dessa enskilda återställningsjobb skapas endast för lokalt fästa volymer.

**F.** Kan jag ändra typen av en volym under en redundansväxling?

**S.** Nej, du kan inte ändra typ av volym under en redundansväxling. Om du inte importeras till en annan fysisk enhet som kör StorSimple 8000 series uppdatera 3, volymerna växlas över baserat på den volymtyp som sparades i ögonblicksbilden.

**F.** Kan jag växla över en volymcontainer med lokalt fixerade volymer till molninstallationen?

**S.** Ja, det kan du. Lokalt fixerade volymer redundansväxlas som nivåindelade volymer. Mer information om [redundans och DR av lokalt fixerade volymer mellan versioner](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover)

