---
title: StorSimple lokalt fästa volymer Vanliga frågor | Microsoft-dokument
description: Ger svar på vanliga frågor om StorSimple lokalt fästa volymer.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60319555"
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple lokalt fästa volymer: vanliga frågor och svar (FAQ)
## <a name="overview"></a>Översikt
Följande är frågor och svar som du kan ha när du skapar en StorSimple lokalt fäst volym, konvertera en nivåindelad volym till en lokalt fäst volym (och vice versa) eller säkerhetskopiera och återställa en lokalt fäst volym.

Frågor och svar ordnas i följande kategorier

* Skapa en lokalt fäst volym
* Säkerhetskopiera en lokalt fäst
* Konvertera en nivåindelad volym till en lokalt fäst volym
* Återställa en lokalt fäst volym
* Misslyckas över en lokalt fäst volym

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Frågor om hur du skapar en lokalt fäst volym
**F.** Vilken är den maximala storleken på en lokalt fäst volym som jag kan skapa på enheterna i 8000-serien?

**A** På enheter som kör StorSimple 8000 Series Update 3.0 kan du etablera lokalt fästa volymer upp till 8,5 TB eller nivåindelad volym upp till 200 TB på 8100-enheten. På den större 8600-enheten kan du etablera lokalt fästa volymer upp till 22,5 TB, eller nivåindelade volymer upp till 500 TB.

**F.** Jag uppgraderade nyligen min 8100-enhet till Uppdatering 3.0 och när jag försöker skapa en lokalt fäst volym är den maximala tillgängliga storleken bara 6 TB och inte 8,5 TB. Varför kan jag inte skapa en volym på 8,5 TB?

**A** Om enheten kör uppdatering 3.0 kan du etablera lokalt fästa volymer upp till 8,5 TB ELLER nivåindelad volym upp till 200 TB på 8100-enheten. Om enheten redan har nivåindelada volymer är det tillgängliga utrymmet för att skapa en lokalt fäst volym proportionellt lägre än den maximala gränsen. Om till exempel cirka 106 TB nivåindelad volymer redan har etablerats på din 8100-enhet (vilket är hälften av den nivåindelad kapaciteten), kommer den maximala storleken på en lokal volym som du kan skapa på 8100-enheten att minskas till 4 TB (ungefär hälften av den maximala lokalt fästa volymkapaciteten).

Eftersom en del lokalt utrymme på enheten används för att vara värd för arbetsuppsättningen med nivåindelade volymer, minskas det tillgängliga utrymmet för att skapa en lokalt fäst volym om enheten har nivåindelada volymer. Om du däremot skapar en lokalt fäst volym minskar det tillgängliga utrymmet för nivåindelade volymer. I följande tabeller sammanfattas den tillgängliga nivåindelade kapaciteten på enheterna 8100 och 8600 när lokalt fästa volymer skapas.

#### <a name="update-30"></a>Uppdatering 3.0 

| Lokalt fäst volymer etablerad kapacitet | Tillgänglig kapacitet som ska etableras för nivåindelade volymer - 8100 | Tillgänglig kapacitet som ska etableras för nivåindelade volymer - 8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176,5 TB |477,8 TB |
| 4 TB |105,9 TB |411,1 TB |
| 8,5 TB |0 TB |311,1 TB |
| 10 TB |Ej tillämpligt |277,8 TB |
| 15 TB |Ej tillämpligt |166,7 TB |
| 22,5 TB |Ej tillämpligt |0 TB |

**F.** Varför skapas lokalt fästa volym en tidskrävande åtgärd?

**A.** Lokalt fästa volymer etableras tjockt. Om du vill skapa utrymme på enhetens lokala nivåer kan vissa data från befintliga nivåindelade volymer skickas till molnet under etableringsprocessen. Och eftersom detta beror på storleken på volymen som etableras, befintliga data på din enhet och den tillgängliga bandbredden för molnet, kan den tid det tar att skapa en lokal volym vara flera timmar.

**F.** Hur lång tid tar det att skapa en lokalt fäst volym?

**A.** Eftersom lokalt fästa volymer är tjockt etablerade kan vissa befintliga data från nivåindelada volymer skickas till molnet under etableringsprocessen. Därför beror den tid det tar att skapa en lokalt fäst volym på flera faktorer, inklusive volymens storlek, data på enheten och den tillgängliga bandbredden. På en nyinstallerad enhet som inte har några volymer är tiden för att skapa en lokalt fäst volym cirka 10 minuter per terabyte data. Det kan dock ta flera timmar att skapa lokala volymer baserat på de faktorer som beskrivs ovan på en enhet som används.

**F.** Jag vill skapa en lokalt fäst volym. Finns det några bästa metoder jag behöver vara medveten om?

**A.** Lokalt fästa volymer är lämpliga för arbetsbelastningar som kräver lokala garantier för data hela tiden och är känsliga för molntidsdämningar. När du överväger användning av lokala volymer för någon av dina arbetsbelastningar bör du tänka på följande:

* Lokalt fästa volymer etableras tjockt och lokala volymer påverkar det tillgängliga utrymmet för nivåindelade volymer. Därför föreslår vi att du börjar med mindre volymer och skala upp när ditt lagringskrav ökar.
* Etablering av lokala volymer är en tidskrävande åtgärd som kan innebära att befintliga data från nivåindelad volymer till molnet. Som ett resultat kan du uppleva minskad prestanda på dessa volymer.
* Etablering av lokala volymer är en tidskrävande åtgärd. Den faktiska tiden beror på flera faktorer: storleken på volymen som etableras, data på enheten och tillgänglig bandbredd. Om du inte har säkerhetskopierat dina befintliga volymer till molnet är volymgenereringen långsammare. Vi föreslår att du tar ögonblicksbilder av molnet av dina befintliga volymer innan du etablerar en lokal volym.
* Du kan konvertera befintliga nivåindelada volymer till lokalt fästa volymer, och den här konverteringen innebär etablering av utrymme på enheten för den resulterande lokalt fästa volymen (förutom att eventuellt neddelat nivåindelad data ska komma från molnet). Återigen, detta är en långvarig operation som beror på faktorer som vi har diskuterat ovan. Vi föreslår att du säkerhetskopierar dina befintliga volymer före konverteringen eftersom processen blir ännu långsammare om befintliga volymer inte säkerhetskopieras. Enheten kan också få nedsatt prestanda under den här processen.

Mer information om hur du [skapar en lokalt fäst volym](storsimple-8000-manage-volumes-u2.md#add-a-volume)

**F.** Kan jag skapa flera lokalt fästa volymer samtidigt?

**A.** Ja, men alla lokalt fästa volymskapande och expansionsjobb bearbetas sekventiellt.

Lokalt fästa volymer etableras tjockt och detta kräver att du skapar lokalt utrymme på enheten (vilket kan resultera i att befintliga data från nivåindelad volymer skickas till molnet under etableringsprocessen). Om ett etableringsjobb pågår kommer därför andra lokala skapandejobb att köas tills jobbet är klart.

På samma sätt, om en befintlig lokal volym expanderas eller en nivåindelad volym konverteras till en lokalt fäst volym, köas skapandet av en ny lokalt fäst volym tills det föregående jobbet har slutförts. Att utöka storleken på en lokalt fäst volym innebär att det befintliga lokala utrymmet för den volymen utökas. Konvertering från en nivåindelad till lokalt fäst volym innebär också att skapa lokalt utrymme för den resulterande lokalt fästa volymen. I båda dessa operationer är skapandet eller utbyggnaden av det lokala utrymmet ett långvarigt jobb.

Du kan visa dessa jobb i **jobbbladet** för Tjänsten StorSimple Device Manager. Jobbet som aktivt bearbetas uppdateras kontinuerligt för att återspegla förloppet för utrymmesetablering. De återstående lokalt fästa volymjobben markeras som körs, men deras förlopp har stoppats och de plockas i den ordning de köade.

**F.** Jag har tagit bort en lokalt fäst volym. Varför ser jag inte det återvunna utrymmet som återspeglas i det tillgängliga utrymmet när jag försöker skapa en ny volym?

**A.** Om du tar bort en lokalt fäst volym kanske det tillgängliga utrymmet för nya volymer inte uppdateras omedelbart. StorSimple Device Manager-tjänsten uppdaterar det lokala utrymme som är tillgängligt ungefär varje timme. Vi föreslår att du väntar i en timme innan du försöker skapa den nya volymen.

**F.** Stöds lokalt fästa volymer på molninstallationen?

**A.** Lokalt fästa volymer stöds inte på molninstallationen (8010- och 8020-enheter som tidigare kallades StorSimples virtuella enhet).

**F.** Kan jag använda Azure PowerShell-cmdletar för att skapa och hantera lokalt fästa volymer?

**A.** Nej, du kan inte skapa lokalt fästa volymer via Azure PowerShell-cmdlets (alla volymer som du skapar via Azure PowerShell är nivåindelade). Vi föreslår också att du inte använder Azure PowerShell-cmdletar för att ändra alla egenskaper för en lokalt fäst volym, eftersom det kommer att ha den oönskade effekten av att ändra volymtypen till nivåindelade.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Frågor om säkerhetskopiering av en lokalt fäst volym
**F.** Stöds lokala ögonblicksbilder av lokalt fästa volymer?

**A.** Ja, du kan ta lokala ögonblicksbilder av dina lokalt fästa volymer. Vi rekommenderar dock starkt att du regelbundet säkerhetskopierar dina lokalt fästa volymer med ögonblicksbilder av molnet för att säkerställa att dina data skyddas i en katastrof.

Observera att lokala ögonblicksbilder av lokalt fästa volymer också kan nivå ut till molnet och är inte garanterade att stanna i den lokala nivån på enheten.

**F.** Finns det några riktlinjer för hantering av lokala ögonblicksbilder för lokalt fästa volymer?

**A.** Frekventa lokala ögonblicksbilder tillsammans med en hög frekvens av dataomsättning i den lokalt fästa volymen kan orsaka att lokalt utrymme på enheten förbrukas snabbt och resultera i att data från nivåindelad volymer skickas till molnet. Vi föreslår därför att du minimerar antalet lokala ögonblicksbilder.

**F.** Jag fick en avisering om att mina lokala ögonblicksbilder av lokalt fästa volymer kan vara ogiltiga. När kan detta hända?

**A.** Frekventa lokala ögonblicksbilder tillsammans med en hög dataomsättning i den lokalt fästa volymen kan orsaka att lokalt utrymme på enheten förbrukas snabbt. Om enhetens lokala nivåer används i stor utsträckning kan ett utökat molnavbrott resultera i att enheten blir full, och inkommande skrivningar till volymen kan leda till att ögonblicksbilderna blir ogiltiga (eftersom det inte finns något utrymme för att uppdatera ögonblicksbilderna för att referera till de äldre blocken i uppgifter som har skrivits över). I en sådan situation kommer skrivningar till volymen att fortsätta att serveras, men de lokala ögonblicksbilderna kan vara ogiltiga. Det finns ingen inverkan på dina befintliga ögonblicksbilder av molnet.

Varningen är att meddela dig om att en sådan situation kan uppstå och se till att du tar itu med samma sak i tid genom att antingen granska dina lokala ögonblicksbilder scheman för att ta mindre frekventa lokala ögonblicksbilder eller ta bort äldre lokala ögonblicksbilder som inte längre behövs.

Om de lokala ögonblicksbilderna är ogiltiga får du en informationsavisering som meddelar dig om att de lokala ögonblicksbilderna för den specifika säkerhetskopieringsprincipen har ogiltigförklarats tillsammans med listan över tidsstämplar för de lokala ögonblicksbilder som har ogiltigförklarats. Dessa ögonblicksbilder tas bort automatiskt och du kommer inte längre att kunna visa dem i bladet **Säkerhetskopior** i Azure-portalen.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Frågor om hur du konverterar en nivåindelad volym till en lokalt fäst volym
**F.** Jag observerar lite långsamhet på enheten när jag konverterar en nivåindelad volym till en lokalt fäst volym. Varför händer detta?

**A.** Konverteringsprocessen omfattar två steg:

1. Etablering av utrymme på enheten för den snart konverterade lokalt fästa volymen.
2. Hämtar alla nivåindelade data från molnet för att säkerställa lokala garantier.

Båda dessa steg är tidskrävande åtgärder som är beroende av storleken på volymen som konverteras, data på enheten och tillgänglig bandbredd. Eftersom vissa data från befintliga nivåindelade volymer kan spillas till molnet som en del av etableringsprocessen kan enheten uppleva minskad prestanda under den här tiden. Dessutom kan konverteringsprocessen vara långsammare om:

* Befintliga volymer har inte säkerhetskopierats till molnet. därför föreslår vi att du säkerhetskopierar dina volymer innan du påbörjar en konvertering.
* Principer för begränsning av bandbredd har tillämpats, vilket kan begränsa den tillgängliga bandbredden till molnet. Vi rekommenderar därför att du har en dedikerad 40 Mbps eller mer anslutning till molnet.
* Konverteringsprocessen kan ta flera timmar på grund av de flera faktorer som förklaras ovan; Därför föreslår vi att du utför den här åtgärden under icke-toppar gånger eller på en helg för att undvika påverkan på slutkonsumenterna.

Mer information om hur du [konverterar en nivåindelad volym till en lokalt fäst volym](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

**F.** Kan jag avbryta volymkonverteringen?

**A.** Nej, du kan inte avbryta konverteringen när den har initierats. Som diskuterats i föregående fråga bör du vara medveten om de potentiella prestandaproblem som du kan stöta på under processen och följ de bästa metoderna som anges ovan när du planerar konverteringen.

**F.** Vad händer med min volym om konverteringen misslyckas?

**A.** Volymkonvertering kan misslyckas på grund av problem med molnanslutningen. Enheten kan så småningom stoppa konverteringsprocessen efter en serie misslyckade försök att få ned nivåindelade data från molnet. I ett sådant scenario fortsätter volymtypen att vara källvolymtypen före konverteringen, och

* En kritisk avisering utlöses för att meddela dig om volymkonverteringsfelet. Mer information om [aviseringar relaterade till lokalt fästa volymer](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts)
* Om du konverterar en nivåindelade till en lokalt fäst volym fortsätter volymen att visa egenskaper för en nivåindelad volym eftersom data fortfarande kan finnas i molnet. Vi föreslår att du löser anslutningsproblemen och sedan försöker igen konverteringsåtgärden.
* På samma sätt, när konvertering från en lokalt fäst till en nivåindelad volym misslyckas, även om volymen kommer att markeras som en lokalt fäst volym, kommer den att fungera som en nivåindelad volym (eftersom data kan ha spillts till molnet). Det kommer dock att fortsätta att uppta utrymme på de lokala nivåerna i enheten. Det här utrymmet är inte tillgängligt för andra lokalt fästa volymer. Vi föreslår att du försöker igen för att säkerställa att volymkonverteringen är klar och att det lokala utrymmet på enheten kan återkn återanvändas.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Frågor om hur du återställer en lokalt fäst volym
**F.** Återställs lokalt fästa volymer direkt?

**A.** Ja, lokalt fästa volymer återställs direkt. Så snart metadatainformationen för volymen hämtas från molnet som en del av återställningsåtgärden, är volymen online och kan nås av värden. Lokala garantier för volymdata kommer dock inte att finnas förrän alla data har hämtats från molnet, och du kan uppleva minskad prestanda på dessa volymer under återställningens varaktighet.

**F.** Hur lång tid tar det att återställa en lokalt fäst volym?

**A.** Lokalt fästa volymer återställs omedelbart och online så snart volymmetadatainformationen hämtas från molnet, medan volymdata fortsätter att hämtas i bakgrunden. Den senare delen av återställningsåtgärden – att få tillbaka de lokala garantierna för volymdata - är en tidskrävande åtgärd och kan ta flera timmar för alla data som ska göras lokala igen. Hur mycket tid det tar att slutföra detsamma beror på flera faktorer, till exempel storleken på volymen som återställs och den tillgängliga bandbredden. Om den ursprungliga volymen som återställs har tagits bort, kommer ytterligare tid att tas för att skapa det lokala utrymmet på enheten som en del av återställningen.

**F.** Jag måste återställa min befintliga lokalt fästa volym till en äldre ögonblicksbild (tas när volymen var nivåindelade). Återställs volymen som nivåindelade i det här fallet?

**A.** Nej, volymen återställs som en lokalt fäst volym. Även om ögonblicksbilden dateras till den tidpunkt då volymen nivåindelades, medan befintliga volymer återställs, använder StorSimple alltid den typ av volym på disken som den finns för närvarande.

**F.** Jag utökade min lokalt fästa volym nyligen, men jag måste nu återställa data till en tid då volymen var mindre i storlek. Kommer att återställa storlek på den aktuella volymen och måste jag utöka volymens storlek när återställningen är klar?

**A.** Ja, återställningen ändrar storlek på volymen och du måste utöka volymens storlek när återställningen har slutförts.

**F.** Kan jag ändra typen av volym under återställningen?

**A.** Nej, du kan inte ändra volymtypen under återställningen.

* Volymer som har tagits bort återställs som den typ som lagras i ögonblicksbilden.
* Befintliga volymer återställs baserat på deras aktuella typ, oavsett vilken typ som lagras i ögonblicksbilden (se de två föregående frågorna).

**F.** Jag måste återställa min lokalt fästa volym, men jag valde en felaktig ögonblicksbild av tidsgränsen. Kan jag avbryta den aktuella återställningen?

**A.** Ja, du kan avbryta en pågående återställning. Volymens tillstånd återställs till tillståndet i början av återställningen. Alla skrivningar som gjordes på volymen medan återställningen pågick kommer dock att gå förlorad.

**F.** Jag startade en återställningsåtgärd på en av mina lokalt fästa volymer, och nu ser jag en ögonblicksbild i min eftersläpningskatalog som jag inte minns att jag skapar. Vad används det här för?

**A.** Detta är den tillfälliga ögonblicksbilden som skapas före återställningen och används för återställning om återställningen avbryts eller misslyckas. Ta inte bort den här ögonblicksbilden. Den tas bort automatiskt när återställningen är klar. Detta kan inträffa om återställningsjobbet bara har lokalt fästa volymer eller en blandning av lokalt fästa och nivåindelade volymer. Om återställningsjobbet endast innehåller nivåindelade volymer kommer det här beteendet inte att inträffa.

**F.** Kan jag klona en lokalt fäst volym?

**A.** Ja, det kan du. Den lokalt fästa volymen klonas dock som en nivåindelad volym som standard. Mer information om hur du [klonar en lokalt fäst volym](storsimple-8000-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Frågor om att misslyckas över en lokalt fäst volym
**F.** Jag måste växla över enheten till en annan fysisk enhet. Kommer mina lokalt fästa volymer att misslyckas som lokalt fästa eller nivåindelade?

**A.** De lokalt fästa volymerna kan inte anges som Lokalt fästa om målenheten kör Uppdatering 3 av StorSimple 8000-serien 3 eller senare.

Mer information om [redundans och DR för lokalt fästa volymer i olika versioner](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**F.** Återställs lokalt fästa volymer omedelbart under haveriberedskap (DR)?

**A.** Ja, lokalt fästa volymer återställs direkt under redundans. Så snart metadatainformationen för volymen hämtas från molnet som en del av redundansåtgärden, är volymen online på målenheten och kan nås av värden. Under tiden fortsätter volymdata att hämtas i bakgrunden och du kan uppleva försämrad prestanda på dessa volymer under redundansens varaktighet.

**F.** Jag ser redundansjobbet slutfört, hur kan jag spåra förloppet för lokalt fäst volym som återställs på målenheten?

**A.** Under en redundansåtgärd markeras redundansjobbet som slutfört när alla volymer i redundansuppsättningen har återställts omedelbart och online på målenheten. Detta inkluderar alla lokalt fästa volymer som kan ha misslyckats över. Lokala garantier för uppgifterna kommer dock endast att vara tillgängliga när alla data för volymen har hämtats. Du kan spåra den här förloppet för varje lokalt fäst volym som misslyckades genom att övervaka motsvarande återställningsjobb som skapas som en del av redundansen. Dessa enskilda återställningsjobb skapas endast för lokalt fästa volymer.

**F.** Kan jag ändra typ av volym under redundans?

**A.** Nej, du kan inte ändra volymtypen under en redundansväxling. Om du misslyckas med en annan fysisk enhet som kör Uppdatering 3 i StorSimple 8000-serien, kan volymerna överskridas baserat på den volymtyp som lagras i ögonblicksbilden.

**F.** Kan jag växla över en volymbehållare med lokalt fästa volymer till molninstallationen?

**A.** Ja, det kan du. De lokalt fästa volymerna kommer att misslyckas över som nivåindelade volymer. Mer information om [redundans och DR för lokalt fästa volymer i olika versioner](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover)

