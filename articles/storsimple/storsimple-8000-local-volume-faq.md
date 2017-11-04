---
title: "StorSimple lokalt fästa volymer vanliga frågor och svar | Microsoft Docs"
description: "Innehåller svar på vanliga frågor om lokalt Fäst StorSimple-volymer."
services: storsimple
documentationcenter: NA
author: manuaery
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2017
ms.author: manuaery
ms.openlocfilehash: 857647e89b7ac5d9bc72bc4b2ace0ea7279353f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple lokalt fästa volymer: vanliga frågor (FAQ)
## <a name="overview"></a>Översikt
Följande är frågor och svar som kan uppstå när du skapar en StorSimple lokalt Fäst volym, konvertera en nivåindelad volym till en lokalt Fäst volym (och vice versa), eller säkerhetskopiera och återställa en lokalt Fäst volym.

Frågor och svar ordnas i följande kategorier

* Skapa en lokalt Fäst volym
* Säkerhetskopiera en lokalt Fäst
* Konvertera en nivåindelad volym till en lokalt Fäst volym
* Återställa en lokalt Fäst volym
* En lokalt Fäst volym inte körs

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Frågor om hur du skapar en lokalt Fäst volym
**F.** Vad är den maximala storleken för en lokalt Fäst volym som jag kan skapa på 8000-serien enheter?

**En** på enheter som kör StorSimple 8000 Series uppdatering 3.0 kan du etablera lokalt fästa volymer upp till 8.5 TB, eller nivåindelade volymer upp till 200 TB på 8100-enheten. På den större 8600-enheten kan du etablera lokalt fästa volymer upp till 22,5 TB, eller nivåindelade volymer upp till 500 TB.

**F.** Jag har nyligen uppgraderat 8100-enhet till Update 3.0 och när jag försöker skapa en lokalt Fäst volym tillgängliga maxstorleken är endast 6 TB och inte 8.5 TB. Varför kan jag skapa en volym med 8.5 TB?

**En** om enheten kör uppdatering 3.0 kan du etablera lokalt fästa volymer upp till 8.5 TB eller nivåindelade volymer upp till 200 TB på 8100-enheten. Om enheten redan har nivåindelade volymer och diskutrymme för att skapa att en lokalt Fäst volym proportionerligt lägre än den här maxgränsen. Till exempel om cirka 106 TB nivåindelade volymer har redan etablerats på din 8100-enhet (som är hälften av nivåindelade kapacitet), reduceras sedan den maximala storleken för en lokal volym som du kan skapa på 8100-enhet på motsvarande sätt till 4 TB (ungefär hälften av maximalt lokalt Fäst volymkapacitet).

Eftersom vissa lokalt utrymme på enheten för att hålla arbetsuppsättningen med nivåindelade volymer kan reduceras det tillgängliga utrymmet för att skapa en lokalt Fäst volym om enheten har nivåindelade volymer. Däremot minskar skapa en lokalt Fäst volym proportionerligt det tillgängliga utrymmet för nivåindelade volymer. Följande tabeller sammanfattar nivåindelade tillgänglig kapacitet på 8100 och 8600-enheter när lokalt fästa volymer skapas.

#### <a name="update-30"></a>Uppdatera 3.0 
| Lokalt fästa volymer etablerad kapacitet | Tillgänglig kapacitet som ska etableras för nivåindelade volymer - 8100 | Tillgänglig kapacitet som ska etableras för nivåindelade volymer - 8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176.5 TB |477.8 TB |
| 4 TB |105.9 TB |411.1 TB |
| 8.5 TB |0 TB |311.1 TB |
| 10 TB |Ej tillämpligt |277.8 TB |
| 15 TB |Ej tillämpligt |166.7 TB |
| 22,5 TB |Ej tillämpligt |0 TB |

**F.** Varför är lokalt Fäst volym skapas en långvarig åtgärd?

**S.** Lokalt fästa volymer etableras tjockt, vilket. För att skapa utrymme på de lokala nivåerna för enheten, kan vissa data från befintliga nivåindelade volymer flyttas till molnet under etableringen. Och eftersom detta beror på storleken på den volym som håller på att etableras, befintliga data på enheten och den tillgängliga bandbredden till molnet och den tid det tar att skapa en lokal volym vara flera timmar.

**F.** Hur lång tid tar det för att skapa en lokalt Fäst volym?

**S.** Eftersom lokalt fästa volymer etableras tjockt, vilket, kan vissa befintliga data från nivåindelade volymer pushas till molnet under etableringen. Den tid det tar att skapa en lokalt Fäst volym är därför beroende av flera faktorer, bland annat paketets storlek på volymen, data på enheten och den tillgängliga bandbredden. Tid för att skapa en lokalt Fäst volym är cirka 10 minuter per terabyte data på en nyligen installerade enhet som har inga volymer. Dock kan lokala volymer ta flera timmar beroende på faktorer som beskrivs ovan på en enhet som används.

**F.** Jag vill skapa en lokalt Fäst volym. Finns det några metodtips som jag behöver vara medveten om?

**S.** Lokalt fästa volymer är lämpliga för arbetsbelastningar som kräver lokala garantier data vid alla tidpunkter och är känsligt för molnet svarstiderna. När du överväger användning av lokala volymer för någon av dina arbetsbelastningar, Tänk på följande:

* Lokalt fästa volymer etableras tjockt, vilket och skapa lokala volymer påverkar det tillgängliga utrymmet för nivåindelade volymer. Därför kan rekommenderar vi att du börjar med mindre volymer och skala upp när din lagring krav ökar.
* Etablering av lokala volymer är en tidskrävande process som kan handla om push-överföring av befintliga data från nivåindelade volymer till molnet. Därför kan det uppstå nedsatt prestanda på volymerna.
* Etablering av lokala volymer är en tidskrävande åtgärd. Den faktiska tiden som är beroende av flera faktorer: storleken på volymen som håller på att etableras, data på din enhet och tillgänglig bandbredd. Om du inte har säkerhetskopierat dina befintliga volymer till molnet är volymer skapas långsammare. Vi rekommenderar att du skapa molnögonblicksbilder av din befintliga volymer innan du etablerar en lokal volym.
* Du kan konvertera befintliga nivåindelade volymer till lokalt fästa volymer och konverteringen innefattar etablering av utrymme på enheten för den resulterande lokalt Fäst volymen (förutom att stänga av nivåindelade data från molnet). Igen, det här är en tidskrävande process som beror på faktorer som vi nämnt ovan. Vi rekommenderar att du säkerhetskopierar dina befintliga volymer innan konvertering som processen kommer att även långsammare om befintliga volymer inte som säkerhetskopieras. Enheten kan även uppstå nedsatt prestanda under den här processen.

Mer information om hur du [skapar en lokalt Fäst volym](storsimple-8000-manage-volumes-u2.md#add-a-volume)

**F.** Kan jag skapa flera lokalt fästa volymer på samma gång?

**S.** Ja, men alla lokalt Fäst volym skapande och utvidgning jobb tur och ordning.

Lokalt fästa volymer etableras tjockt, vilket vilket kräver skapandet av lokalt utrymme på enheten (vilket kan resultera i befintliga data från nivåindelade volymer att flyttas till molnet under etableringen). Därför om en Etableringsjobbet pågår andra jobb för skapande av lokal volym placeras i kö tills jobbet har slutförts.

På liknande sätt, om en befintlig lokal volym utökas eller en nivåindelad volym konverteras till en lokalt Fäst volym, sedan att skapa en ny lokalt Fäst volym placeras i kö tills det tidigare jobbet har slutförts. Utöka storleken på en lokalt Fäst volym omfattar ökning av det befintliga lokala utrymmet för den volymen. Konverteringen från en skiktad lokalt Fäst volym omfattar också skapandet av lokalt utrymme för det resulterande lokalt Fäst volym. I båda dessa åtgärder, skapas eller expandering av lokalt utrymme lång körs jobbet.

Du kan visa dessa jobb i den **jobb** bladet för StorSimple enheten Manager-tjänsten. Jobb som bearbetas aktivt uppdateras kontinuerligt förloppet för etablering av utrymme. De återstående lokalt Fäst volym jobb är markerad som körs, men framstegen har stoppats och de plockats i den ordning som de har ställts i kö.

**F.** Jag har tagit bort en lokalt Fäst volym. Varför ser jag återvunnet utrymme som visas i det tillgängliga utrymmet när jag försöker skapa en ny volym?

**S.** Om du tar bort en lokalt Fäst volym kan det tillgängliga utrymmet för nya volymer inte uppdateras omedelbart. Tjänsten StorSimple Device Manager uppdaterar lokalt tillgängligt utrymme cirka varje timme. Vi rekommenderar att du väntar en timme innan du försöker skapa den nya volymen.

**F.** Stöds lokalt fästa volymer på molnet-enhet?

**S.** Lokalt fästa volymer stöds inte på molnet-enhet (8010 och 8020 enheter samlingsnamnet virtuell StorSimple-enhet).

**F.** Kan jag använda Azure PowerShell-cmdlets för att skapa och hantera lokalt fästa volymer?

**S.** Nej, du kan inte skapa lokalt fästa volymer via Azure PowerShell-cmdlets (alla volymer som du skapar via Azure PowerShell skikt). Vi rekommenderar också att du inte använder Azure PowerShell-cmdlets för att ändra några egenskaper för en lokalt Fäst volym, eftersom det skulle ha områdena leda till att volymtypen till nivåindelade.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Frågor om hur du säkerhetskopierar en lokalt Fäst volym
**F.** Finns lokala ögonblicksbilder av lokalt fästa volymer stöds?

**S.** Ja, kan du skapa lokala ögonblicksbilder av din lokalt fästa volymer. Men rekommenderar vi starkt att du regelbundet säkerhetskopierar din lokalt fästa volymer med molnögonblicksbilder så att dina data skyddas i fall av en katastrofåterställning.

Observera att lokala ögonblicksbilder av lokalt fästa volymer kan också tjänstnivån ut till molnet och är inte garanterat att stanna på den lokala nivån för enheten.

**F.** Finns det några riktlinjer för att hantera lokala ögonblicksbilder för lokalt fästa volymer?

**S.** Ofta lokala ögonblicksbilder tillsammans med en hög andel dataomsättningen i lokalt Fäst volym kan orsaka lokalt utrymme på enheten för att förbrukas snabbt och resultera i att data från nivåindelade volymer som läggs till molnet. Vi rekommenderar därför att du minimerar antalet lokala ögonblicksbilder.

**F.** Jag har fått en varning om att min lokala ögonblicksbilder av lokalt fästa volymer kan vara ogiltiga. När kan detta inträffa?

**S.** Ofta lokala ögonblicksbilder tillsammans med en hög andel dataomsättningen i lokalt Fäst volym kan orsaka lokalt utrymme på enheten som ska konsumeras snabbt. Om de lokala nivåerna på enheten används kraftigt utökat molnet avbrott leda till enheten blir fulla och inkommande skrivning till volymen kan resultera i ogiltigförklarade ögonblicksbilder (eftersom det finns inget utrymme för att uppdatera ögonblicksbilder att referera till de äldre datablock som har skrivits över). I en sådan situation skrivning till volymen kommer att fortsätta hanteras, men de lokala ögonblicksbilderna kan vara ogiltig. Det finns ingen inverkan på din befintliga molnögonblicksbilder.

Varning varning är att meddela dig om att en sådan situation kan uppstå och kontrollerar du adress som är samma inom rimlig tid genom att granska din lokala ögonblicksbilder scheman för att ta mindre ofta lokala ögonblicksbilder eller ta bort äldre lokala ögonblicksbilder som inte längre behövs.

Om lokala ögonblicksbilder är ogiltiga får du en avisering för information som får ett meddelande om att de lokala ögonblicksbilderna för den specifika säkerhetskopieringsprincipen har ogiltigförklarats tillsammans med listan över tidsstämplar lokala ögonblicksbilder upphävdes. Dessa ögonblicksbilder tas automatiskt bort och du kommer inte längre att kunna se dem i den **säkerhetskopiering kataloger** bladet i Azure-portalen.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Frågor om hur du konverterar en nivåindelad volym till en lokalt Fäst volym
**F.** Jag sett vissa långsamt arbete på enheten vid konvertering av en nivåindelad volym till en lokalt Fäst volym. Varför är det fortfarande händer?

**S.** Processen består av två steg:

1. Etablering av utrymme på enheten för den snart-till--konverteras lokalt Fäst volym.
2. Hämtar alla nivåindelade data från molnet så lokala garanterar.

Båda stegen är långa kör åtgärder som är beroende på storleken på volymen som konverteras data på enheten och tillgänglig bandbredd. Eftersom vissa data från befintliga nivåindelade volymer kan läcker över till molnet som en del av etableringen, kan enheten uppleva nedsatt prestanda under denna tid. Dessutom kan processen kan ta längre tid om:

* Befintliga volymer har inte säkerhetskopierats till moln; så vi rekommenderar att du säkerhetskopierar dina volymer innan du påbörjar en konvertering.
* Bandbredd bandbreddsbegränsning principer har tillämpats, vilket kan begränsa den tillgängliga bandbredden till molnet; Vi rekommenderar därför att du har en dedikerad 40 Mbit/s eller mer anslutning till molnet.
* Processen kan ta flera timmar beror på flera faktorer som beskrivs ovan. Vi rekommenderar därför att du utför den här åtgärden under icke toppar gånger eller på en helg att undvika effekten på slutet konsumenter.

Mer information om hur du [konvertera en nivåindelad volym till en lokalt Fäst volym](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

**F.** Kan jag avbryta konvertering volymen?

**S.** Nej, du kan inte Avbryt konvertering initierade en gång. Enligt beskrivningen i föregående fråga Tänk potentiella prestandaproblem som du kan stöta på under processen och följ rekommenderade metoder när du planerar din konvertering i listan ovan.

**F.** Vad händer med min volym om konvertering misslyckas?

**S.** Volymen konverteringen kan misslyckas på grund av anslutningsproblem för molnet. Enheten kan slutligen sluta konverteringen efter ett antal misslyckade försök att sätta nivåindelade data från molnet. I ett sådant scenario volymtyp fortsätter att vara volym källtypen innan konverteringen och:

* En kritisk varning aktiveras för att meddela dig om Konverteringsfel volym. Mer information om [aviseringar relaterade till lokalt fästa volymer](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts)
* Om du konverterar en skiktad till en lokalt Fäst volym, fortsätter volymen kan visa egenskaperna för en nivåindelad volym som data kan fortfarande finnas i molnet. Vi rekommenderar att du löser problem med nätverksanslutningen och försök sedan konvertering.
* Om konvertering från en lokalt Fäst till en nivåindelad volym misslyckas, även om volymen kommer att markeras som en lokalt Fäst volym, fungerar det på samma sätt som en nivåindelad volym (eftersom data kan ha hamnat i molnet). Den fortsätter dock tar upp diskutrymme på de lokala nivåerna för enheten. Här är inte tillgänglig för andra lokalt fästa volymer. Vi rekommenderar att du försöka igen för att säkerställa att volymen konverteringen är klar och kan vara frigöras lokalt utrymme på enheten.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Frågor om hur du återställer en lokalt Fäst volym
**F.** Är lokalt fästa volymer återställs direkt?

**S.** Ja, lokalt fästa volymer återställs omedelbart. Så snart som en del av återställningen hämtas metadata-information för volymen från molnet, volymen är online och kan nås av värden. Dock nedsatt lokala garantier för volymen data visas inte förrän alla data som har hämtats från molnet och kan uppstå prestanda på dessa volymer under återställningen.

**F.** Hur lång tid tar det för att återställa en lokalt Fäst volym?

**S.** Lokalt fästa volymer återställs omedelbart och anslutas så snart volyminformation för metadata har hämtats från molnet, medan volymens data fortsätter att hämtas i bakgrunden. Den här senare delen av återställningen--hämta lokala garantier för volymdata--är en tidskrävande åtgärd och kan ta flera timmar för alla data görs lokala igen. Den tid det tar att slutföra samma beror på flera faktorer, till exempel storleken på volymen som återställs och tillgänglig bandbredd. Om den ursprungliga volymen som återställs har tagits bort, tas extra tid att skapa lokala utrymme på enheten som en del av återställningen.

**F.** Jag behöver återställa mitt befintliga lokalt Fäst volym till en tidigare ögonblicksbild (tas när volymen har nivåer). Volymen återställs som nivåer i det här fallet?

**S.** Nej, återställs volymen som en lokalt Fäst volym. Även om ögonblicksbild datum med tiden när volymen har nivåer, när befintliga volymer, StorSimple använder alltid typ av volym på disken eftersom den finns för närvarande.

**F.** Det utökade min lokalt Fäst volym nyligen, men jag nu måste du återställa data till en tid när volymen var mindre storlek. Återställ storleken den aktuella volymen och behöver jag utöka storleken på volymen när återställningen har slutförts?

**S.** Ja, återställningen ändrar storlek på volymen och du behöver utöka storleken på volymen när återställningen har slutförts.

**F.** Kan jag ändra typen av en volym under återställning?

**A.**Nej, du kan inte ändra volymtypen under återställning.

* Volymer som har tagits bort återställs som lagras i ögonblicksbilden.
* Befintliga volymer återställs baserat på aktuell typ, oavsett vilken typ som lagras i ögonblicksbilden (se föregående två frågor).

**F.** Jag måste du återställa mitt lokalt Fäst volym, men jag importerade en felaktig punkt i ögonblicksbilder. Kan jag avbryta återställningen?

**S.** Ja, du kan avbryta en pågående återställning. Status för volymen kommer att återställas till tillståndet i början av återställningen. Dock försvinner alla skrivningar som har gjorts till volymen medan återställningen pågår.

**F.** Jag startade en återställning på en av min lokalt fästa volymer och nu visas en ögonblicksbild i min eftersläpning katalogen som jag inte recollect skapar. Vad är detta används för?

**S.** Det här är den tillfälliga ögonblicksbilden som skapades före återställningen och används för återställning om återställningen avbryts eller misslyckas. Ta inte bort den här ögonblicksbilden; det tas automatiskt bort när återställningen är slutförd. Detta kan inträffa om din Återställningsjobbet har bara lokalt Fäst volymer eller en blandning av lokalt Fäst och nivåindelade volymer. Om återställningsjobbet innehåller endast nivåindelade volymer, sker inte det här beteendet.

**F.** Kan jag klona en lokalt Fäst volym?

**S.** Ja, du kan. Dock kommer lokalt Fäst volym att klonas när en nivåindelad volym som standard. Mer information om hur du [klona en lokalt Fäst volym](storsimple-8000-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Frågor om inte körs en lokalt Fäst volym
**F.** Jag behöver växla över min enhet till en annan fysisk enhet. Kommer min lokalt fästa volymer att växlas över som lokalt Fäst eller nivåer?

**S.** Lokalt fästa volymer har redundansväxlats lokalt Fäst om målenheten kör StorSimple 8000 uppdatering 3 eller senare.

Mer information om [redundans och Katastrofåterställning för lokalt fästa volymer mellan olika versioner](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**F.** Återställs lokalt fästa volymer direkt vid katastrofåterställning (DR)?

**S.** Ja, återställs lokalt fästa volymer direkt under växling vid fel. Så snart som en del av Redundansåtgärden hämtas metadata-information för volymen från molnet, volymen tas online på målenheten och kan nås av värden. Under tiden volymens data kommer att fortsätta att ladda ned i bakgrunden och nedsatt prestanda på volymerna under växling vid fel kan uppstå.

**F.** Visas failover-jobbet har slutförts, hur kan jag spåra förloppet för lokalt Fäst volym återställs på målenheten?

**S.** Under en redundansväxling har beställningsjobbet markerats som slutförd när alla volymer i uppsättningen av redundans har återställts och tas online på målenheten omedelbart. Det inkluderar alla lokalt fästa volymer som kanske har växlats över; lokala garantier data kommer dock bara är tillgänglig när alla data på volymen har hämtats. Du kan följa den här förloppet för varje lokalt Fäst volym som gick över övervakning motsvarande återställningsjobb som skapats som en del av växling vid fel. Dessa enskilda jobb skapas endast för lokalt fästa volymer.

**F.** Kan jag ändra typen av en volym under växling vid fel?

**S.** Nej, du kan inte ändra volymtypen under en växling vid fel. Om du misslyckas över till en annan fysisk enhet som kör StorSimple 8000 series uppdatering-3, volymerna har redundansväxlats baserat på den volymtyp som lagras i ögonblicksbilden.

**F.** Kan jag växla över en volymbehållare med lokalt fästa volymer till molnet enhet?

**S.** Ja, du kan. Lokalt fästa volymer kommer att redundansväxlas som nivåindelade volymer. Mer information om [redundans och Katastrofåterställning för lokalt fästa volymer mellan olika versioner](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover)

