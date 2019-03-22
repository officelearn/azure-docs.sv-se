---
title: Visa och hantera aviseringar för enhet i StorSimple 8000-serien | Microsoft Docs
description: Beskriver StorSimple aviseringsvillkor och allvarlighetsgrad, hur du konfigurerar aviseringar och hur du använder StorSimple Device Manager-tjänsten för att hantera aviseringar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 38d403be1dc35b8ab4cd1b15fa259b5e6917197c
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314458"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>Använda StorSimple Device Manager-tjänsten för att visa och hantera aviseringar för StorSimple

## <a name="overview"></a>Översikt

Den **aviseringar** bladet i StorSimple Device Manager-tjänsten gör det möjligt för dig att granska och ta bort StorSimple-enhet – relaterade aviseringar på basis av i realtid. Från det här bladet kan du centralt övervaka hälsoproblem för din StorSimple-enheter och den övergripande Microsoft Azure StorSimple-lösningen.

Den här självstudien beskrivs vanliga aviseringsvillkor nivåer för aviseringarnas allvarlighetsgrad och hur du konfigurerar aviseringar. Den innehåller dessutom avisering Snabbreferens tabeller, som gör det möjligt att snabbt hitta en specifik avisering och svara på rätt sätt.

![Sidan varningar](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Vanliga aviseringsvillkor

StorSimple-enheten genererar varningar som svar på en mängd olika villkor. Följande är de vanligaste typerna av aviseringsvillkor:

* **Problem med hårdvaran** – dessa aviseringar meddelar dig om hälsotillståndet för maskinvaran. De meddelar dig om firmware uppgraderingar krävs om ett nätverksgränssnitt har problem med, eller om det finns ett problem med en av dina enheter.
* **Problem med nätverksanslutningen** – dessa aviseringar inträffar när det är svårt att överföra data. Kommunikationsproblem kan uppstå under överföring av data till och från Azure storage-kontot eller på grund av avsaknad av anslutning mellan enheter och StorSimple Device Manager-tjänsten. Kommunikationsproblem är några av de svåraste åtgärda eftersom det finns så många felpunkter. Du bör alltid först kontrollera att nätverksanslutningen och tillgång till Internet är tillgängliga innan du fortsätter till mer avancerad felsökning. För hjälp med felsökning kan du gå till [felsöka med cmdleten Test-Connection](storsimple-8000-troubleshoot-deployment.md).
* **Prestandaproblem** – de här aviseringarna orsakas när datorn inte är presterar optimalt, till exempel när den är hårt belastat.

Du kan dessutom se aviseringar som rör säkerhet, uppdateringar eller misslyckade jobb.

## <a name="alert-severity-levels"></a>Nivåer för aviseringarnas allvarlighetsgrad

Aviseringar har olika allvarlighetsgrader, beroende på effekten som aviseringen situationen har och behovet av att ett svar på aviseringen. Allvarlighetsgraderna finns:

* **Kritiska** – den här aviseringen är reaktion på ett villkor som påverkar systemets lyckad prestanda. Åtgärd krävs för att se till att StorSimple tjänsten inte avbryts.
* **Varning** – det här tillståndet kan bli kritiska om inte matcha. Du bör undersöka situationen och vidta några åtgärder som krävs för att rensa problemet.
* **Information** – den här aviseringen innehåller information som kan vara användbara i spåra och hantera dina system.

## <a name="configure-alert-settings"></a>Konfigurera aviseringsinställningar

Du kan välja om du vill bli meddelad via e-post om aviseringsvillkoren för var och en av dina StorSimple-enheter. Du kan dessutom identifiera andra aviseringsmottagare genom att ange deras e-postadresser i den **andra e-postmottagare** box, avgränsade med semikolon.

> [!NOTE]
> Du kan ange högst 20 e-postadresser per enhet.

När du aktiverar e-postmeddelande om en enhet kan får medlemmar i meddelandelistan över ett e-postmeddelande varje gång en kritisk varning inträffar. Meddelanden skickas från *storsimple-aviseringar-noreply\@mail.windowsazure.com* och beskriver aviseringstillståndet. Mottagare kan klicka på **Unsubscribe** att ta bort själva från listan över e-post-meddelande.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Aktivera e-postmeddelande för aviseringar för en enhet
1. Gå till StorSimple Device Manager-tjänsten. Välj i listan med enheter, och klicka på den enhet som du vill konfigurera.
2. Gå till **inställningar** > **Allmänt** för enheten.

   ![Bladet med säkerhetsaviseringar](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. I den **allmänna inställningar** gå till bladet **varningsinställningar** och ange följande:
   
   1. I den **skicka e-postavisering** väljer **Ja**.
   2. I den **e-tjänstadministratörer** väljer **Ja** att tjänstadministratören och alla medadministratörer ska få aviseringar skickas.
   3. I den **andra e-postmottagare** fältet, anger du e-postadresserna för alla andra mottagare som ska ta emot aviseringar skickas. Ange namn i formatet *någon\@somewhere.com*. Avgränsa e-postadresser med semikolon. Du kan konfigurera högst 20 e-postadresser per enhet. 
      
3. Om du vill skicka ett testmeddelande för e-post, klickar du på **skicka e-posttestmeddelande**. StorSimple Device Manager-tjänsten visar statusmeddelanden som vidarebefordras av test-meddelande.

    ![Aviseringsinställningar](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. Du får ett meddelande när test-e-postmeddelande skickas. 
   
    ![Testa e-postmeddelande skickas för aviseringar](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Om inte går att skicka testmeddelandet, visas ett lämpligt felmeddelande med StorSimple Device Manager-tjänsten. Vänta några minuter och försök sedan att skicka din testnotismeddelande igen. 

5. När du har slutfört konfigurationen, klickar du på **spara**. Klicka på **Ja** när du uppmanas att bekräfta åtgärden.

     ![Testa e-postmeddelande skickas för aviseringar](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Visa och spåra aviseringar

Sammanfattningsbladet för StorSimple Device Manager-tjänsten ger dig en snabb titt på hur många aviseringar på dina enheter, ordnade efter allvarlighetsgrad.

![Instrumentpanel för aviseringar](./media/storsimple-8000-manage-alerts/device-summary4.png)

Om du klickar på allvarlighetsgraden öppnas den **aviseringar** bladet. Resultatet innehåller bara de aviseringar som matchar den allvarlighetsgraden.

Klicka på en avisering i listan får du ytterligare information för aviseringen, inklusive den senaste gången aviseringen rapporterades antalet förekomster av avisering på enheten och den rekommenderade åtgärden för att lösa aviseringen. Om det är en avisering om maskinvara, kommer det också identifiera maskinvarukomponenten.

![Maskinvara-aviseringsexemplet](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

Du kan kopiera Aviseringsinformationen till en textfil, om du vill skicka information till Microsoft Support. När du har följt rekommendationerna och löst varningsvillkor lokala platser, du bör ta bort aviseringen från enheten genom att välja aviseringen i den **aviseringar** bladet och klicka på **Rensa**. Om du vill ta bort flera aviseringar, väljer varje avisering, klickar du på alla kolumner utom den **avisering** kolumnen och klicka sedan på **Rensa** när du har valt alla aviseringar rensas. Observera att vissa aviseringar automatiskt rensas när problemet är löst eller när systemet uppdaterar aviseringen med ny information.

När du klickar på **Rensa**, du har möjlighet att ange kommentarer om aviseringen och de steg som du har gjort för att lösa problemet. Vissa händelser kommer att tas bort av systemet om en annan händelsen utlöses med den nya informationen. I så fall visas följande meddelande.

![Rensa avisering](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Sortera aktiviteter och aviseringar

Du kan det vara mer effektivt att köra rapporter om aviseringar så att du kan granska och tar bort dem i grupper. Dessutom kan den **aviseringar** bladet kan visa upp till 250 aviseringar. Om du har överskridit det antalet aviseringar visas inte alla aviseringar i standardvyn. Du kan kombinera följande fält om du vill anpassa vilka aviseringar visas:

* **Status för** – du kan visa antingen **Active** eller **avmarkerad** aviseringar. Aktiva aviseringar aktiveras fortfarande i systemet, medan avmarkerad aviseringar antingen har rensats manuellt av en administratör eller programmatiskt eftersom systemet uppdateras aviseringstillståndet med den nya informationen.
* **Allvarlighetsgrad** – du kan visa aviseringar för alla allvarlighetsgrader (kritisk, varning, information) eller bara en viss allvarlighetsgrad, till exempel endast kritiska aviseringar.
* **Källan** – du kan visa aviseringar från alla källor eller begränsa aviseringar till dem som kommer från tjänsten eller en eller alla enheter.
* **Tidsintervall** – genom att ange den **från** och **till** datum och tidsstämplar, kan du titta på aviseringar under den tidsperiod som du är intresserad av.

![Aviseringslistan](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Snabbreferens för aviseringar

I tabellerna nedan listas några av de Microsoft Azure StorSimple-aviseringar som kan uppstå, samt ytterligare information och rekommendationer där det är tillgängligt. StorSimple-enhetsaviseringar indelas i följande kategorier:

* [Anslutningsvarningar i molnet](#cloud-connectivity-alerts)
* [Kluster-aviseringar](#cluster-alerts)
* [Disaster recovery aviseringar](#disaster-recovery-alerts)
* [Maskinvara-aviseringar](#hardware-alerts)
* [Varningar om jobbfel](#job-failure-alerts)
* [Lokalt fixerade volymer av aviseringar](#locally-pinned-volume-alerts)
* [Aviseringar för nätverk](#networking-alerts)
* [Aviseringar om programprestanda](#performance-alerts)
* [Säkerhetsaviseringar](#security-alerts)
* [Stöd för paketet aviseringar](#support-package-alerts)

### <a name="cloud-connectivity-alerts"></a>Anslutningsvarningar i molnet

| Aviseringstext | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Anslutningen till <*molnet autentiseringsuppgiftsnamn*> kan inte upprättas. |Det går inte att ansluta till lagringskontot. |Det verkar som det kan finnas anslutningsproblem med enheten. Kör den `Test-HcsmConnection` cmdlet från Windows PowerShell-gränssnittet för StorSimple på enheten för att identifiera och åtgärda problemet. Om inställningarna är korrekta kan problemet vara med autentiseringsuppgifterna för lagringskontot som varningen skapades. I det här fallet den `Test-HcsStorageAccountCredential` cmdlet för att avgöra om det finns problem som du kan lösa.<ul><li>Kontrollera dina nätverksinställningar.</li><li>Kontrollera autentiseringsuppgifterna för ditt lagringskonto.</li></ul> |
| Vi har inte tagit emot något pulsslag från enheten under senaste <*nummer*> minuter. |Det går inte att ansluta till enheten. |Det verkar som det finns ett anslutningsproblem med enheten. Använd den `Test-HcsmConnection` cmdlet från Windows PowerShell-gränssnittet för StorSimple på enheten för att identifiera och åtgärda problemet eller kontakta din nätverksadministratör. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>StorSimple-beteendet när molnanslutning misslyckas

Vad händer om molnanslutning misslyckas för min StorSimple-enhet som körs i produktionsmiljön?

Om molnanslutning misslyckas på din StorSimple-enhet för produktion, kan sedan beroende på din enhet, följande inträffa:

* **För den lokala data på din enhet**: Det blir inga avbrott under en period och läsningar fortsätter att hanteras. När antalet utestående I/o ökar och överskrider en gräns, startade läsningar misslyckas.

    Beroende på mängden data på din enhet fortsätter även skrivningar kan förekomma första några timmar efter avbrott i molnanslutning. Skrivningar kommer sedan långsammare och starta så småningom att misslyckas om molnanslutning avbryts under flera timmar. (Det är tillfälligt lagringsutrymme på enheten för data som ska skickas till molnet. Det här området har rensats när data skickas. Om anslutningen misslyckas data i den här lagringsområde överförs inte till molnet och i/o misslyckas.)
* **För data i molnet**: För de flesta cloud-anslutningsfel returneras ett fel. När anslutningen återupprättas återupptas IOs utan att användaren måste aktivera volymen online. I sällsynta fall kan det krävas åtgärder från användaren att aktivera tillbaka online volymen från Azure-portalen.
* **För ögonblicksbilder av molnet håller på att**: Åtgärden görs några gånger inom 4 – 5 timmar och ögonblicksbilder av molnet misslyckas om anslutningen inte har återställts.

### <a name="cluster-alerts"></a>Kluster-aviseringar

| Aviseringstext | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Enheten redundansväxlades till <*enhetsnamn*>. |Enheten är i underhållsläge. |Enheten redundansväxlades på grund av att delta eller lämnar underhållsläge. Detta är normalt och ingen åtgärd krävs. När du har godkänt aviseringen kan du radera den från aviseringssidan. |
| Enheten redundansväxlades till <*enhetsnamn*>. |Enhetens inbyggda programvara eller programvara har nyss uppdaterats. |Det uppstod ett fel i ett kluster på grund av en uppdatering. Detta är normalt och ingen åtgärd krävs. När du har godkänt aviseringen kan du radera den från aviseringssidan. |
| Enheten redundansväxlades till <*enhetsnamn*>. |Kontrollenheten stängdes eller startas om. |Enheten redundansväxlades eftersom den aktiva kontrollenheten stängdes av eller startades om av en administratör. Ingen åtgärd krävs. När du har godkänt aviseringen kan du radera den från aviseringssidan. |
| Enheten redundansväxlades till <*enhetsnamn*>. |Planerad redundans. |Kontrollera att det var en planerad redundans. När du har vidtagit åtgärder, radera aviseringen från aviseringssidan. |
| Enheten redundansväxlades till <*enhetsnamn*>. |Oplanerad redundans. |StorSimple är utformat för att återställa automatiskt från oplanerade redundanser. Om du ser ett stort antal dessa aviseringar kan du kontakta Microsoft Support. |
| Enheten redundansväxlades till <*enhetsnamn*>. |Andra/okänd orsak. |Om du ser ett stort antal dessa aviseringar kan du kontakta Microsoft Support. Radera aviseringen från aviseringssidan när problemet är löst. |
| En tjänst för kritiska enheter rapporterar status som misslyckad. |DataPath tjänstfel. |Kontakta Microsoft Support för hjälp. |
| Virtuella IP-adressen för nätverksgränssnittet <*DATA #*> rapporterar status som misslyckad. |Andra/okänd orsak. |Ibland kan tillfälliga förhållanden utlösa dessa aviseringar. Om så är fallet, sedan rensas den här aviseringen automatiskt efter en stund. Kontakta Microsoft-supporten om problemet kvarstår. |
| Virtuella IP-adressen för nätverksgränssnittet <*DATA #*> rapporterar status som misslyckad. |Namnet på nätverksgränssnittet som: <*DATA #*> IP-adress <IP address> kunde inte försättas online eftersom en duplicerad IP-adress har identifierats i nätverket. |Se till att IP-adressdubblett tas bort från nätverket eller konfigurera om gränssnittet med en annan IP-adress. |

### <a name="disaster-recovery-alerts"></a>Disaster recovery aviseringar

| Aviseringstext | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Återställningsåtgärder kunde inte återställa alla inställningar för den här tjänsten. Enhetens konfigurationsinformation är i ett inkonsekvent tillstånd för vissa enheter. |Datainkonsekvens upptäcktes efter katastrofåterställning. |Krypterade data på tjänsten är inte synkroniserad med den på enheten. Auktorisera enheten <*enhetsnamn*> från StorSimple Device Manager för att starta synkroniseringsprocessen. Använda Windows PowerShell-gränssnittet för StorSimple för att köra den `Restore-HcsmEncryptedServiceData` på enhet <*enhetsnamn*> cmdlet, vilket ger det gamla lösenordet som indata till denna cmdlet för att återställa säkerhetsprofilen. Kör sedan den `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet för att uppdatera krypteringsnyckeln för tjänstdata. När du har vidtagit åtgärder, radera aviseringen från aviseringssidan. |

### <a name="hardware-alerts"></a>Maskinvara-aviseringar

| Aviseringstext | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Maskinvarukomponent <*komponent-ID*> rapporterar statusen <*status*>. | |Ibland kan tillfälliga förhållanden utlösa dessa aviseringar. I så, fall kommer den här aviseringen lösas automatiskt efter en stund. Kontakta Microsoft-supporten om problemet kvarstår. |
| Passiv styrenhet fungerar inte. |Den passiva styrenheten för (sekundär) fungerar inte. |Enheten fungerar men en av kontrollenheterna fungerar inte. Försök att starta om den styrenheten. Kontakta Microsoft Support om problemet kvarstår. |

### <a name="job-failure-alerts"></a>Varningar om jobbfel

| Aviseringstext | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Säkerhetskopiering av <*grupp-ID för volymen*> misslyckades. |Säkerhetskopieringsjobbet misslyckades. |Problem med nätverksanslutningen kan göra att säkerhetskopieringen inte kan slutföras. Om det finns några anslutningsproblem kan har du nått det maximala antalet säkerhetskopieringar. Ta bort eventuella säkerhetskopior som inte behövs längre och försök igen. När du har vidtagit åtgärder, radera aviseringen från aviseringssidan. |
| Klon av <*käll-ID: N säkerhetskopieringselementets*> till <*mål volym serienummer*> misslyckades. |Det gick inte att klona. |Uppdatera listan säkerhetskopiering för att verifiera att säkerhetskopian fortfarande är giltig. Om säkerhetskopian är giltig, är det möjligt att molnet anslutningsproblem förhindrar klonåtgärden inte kan slutföras. Om det finns några anslutningsproblem kan har du nått gränsen för lagring. Ta bort eventuella säkerhetskopior som inte behövs längre och försök igen. Radera aviseringen från aviseringssidan när du har vidtagit de åtgärder som att lösa problemet. |
| Återställning av <*käll-ID: N säkerhetskopieringselementets*> misslyckades. |Det gick inte att återställa. |Uppdatera listan säkerhetskopiering för att verifiera att säkerhetskopian fortfarande är giltig. Om säkerhetskopian är giltig, är det möjligt att molnet anslutningsproblem förhindrar återställningen inte kan slutföras. Om det finns några anslutningsproblem kan har du nått gränsen för lagring. Ta bort eventuella säkerhetskopior som inte behövs längre och försök igen. Radera aviseringen från aviseringssidan när du har vidtagit de åtgärder som att lösa problemet. |

### <a name="locally-pinned-volume-alerts"></a>Lokalt fixerade volymer av aviseringar

| Aviseringstext | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Skapandet av lokal volym <*volymnamn*> misslyckades. |Det gick inte att jobbet för att skapa volymen. <*Felmeddelande för misslyckad felkoden*>. |Anslutningsproblem kan hindra utrymme skapas åtgärden inte kan slutföras. Lokalt Fäst volym etableras tjockt och processen för att skapa utrymme skapas spills nivåindelade volymer över till molnet. Om det finns några anslutningsproblem kan ha du förbrukat det lokala utrymmet på enheten. Avgör om finns utrymme på enheten innan du försöker igen. |
| Utökningen av lokal volym <*volymnamn*> misslyckades. |Volymen ändring av jobbet misslyckades på grund av <*felmeddelande för misslyckad felkoden*>. |Anslutningsproblem kan hindra volym expansion åtgärden inte kan slutföras. Lokalt Fäst volym etableras tjockt och processen med att utöka befintliga utrymmet utökas spills nivåindelade volymer över till molnet. Om det finns några anslutningsproblem kan ha du förbrukat det lokala utrymmet på enheten. Avgör om finns utrymme på enheten innan du försöker igen. |
| Konvertering av volymen <*volymnamn*> misslyckades. |Volymomvandlingen att omvandla volymtypen från lokalt fästa på nivåindelade misslyckades. |Omvandlingen av volymen från typen lokalt fixerade till nivåer gick inte att slutföra. Se till att det inte finns några anslutningsproblem som hindrar åtgärden slutförs. För felsökning av anslutningsmöjligheter problem går du till [felsöka med cmdleten Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Den ursprungliga lokalt fixerade volymen har nu markerats som en nivåindelad volym eftersom vissa data från lokalt fixerad volym har hamnat till molnet under konverteringen. Den resulterande nivåindelade volymen fortfarande några lokalt utrymme på den enhet som kan vara frigöras för framtida lokala volymer.<br>Lös eventuella problem med nätverksanslutningen, radera aviseringen och konvertera den här volymen tillbaka till den ursprungliga typen lokalt Fäst volym att kontrollera att alla data görs tillgänglig lokalt igen. |
| Konvertering av volymen <*volymnamn*> misslyckades. |Volymomvandlingen att omvandla volymtypen från nivåindelad till lokalt fixerade misslyckades. |Omvandlingen av volymen från typen nivåindelad till lokalt fixerade kunde inte slutföras. Se till att det inte finns några anslutningsproblem som hindrar åtgärden slutförs. För felsökning av anslutningsmöjligheter problem går du till [felsöka med cmdleten Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Den ursprungliga nivåindelade volymen nu markerats som en lokalt Fäst volym som en del av processen fortsätter att ha data som finns i molnet, medan det tjockt allokerade utrymmet på enheten för den här volymen kan inte längre vara frigöras för framtida lokala volymer.<br>Lös eventuella problem med nätverksanslutningen, radera aviseringen och konvertera den här volymen tillbaka till den ursprungliga typen nivåindelad volym så lokalt utrymme tjockt angetts på enheten kan vara frigöras. |
| Närmar sig lokalt utrymme för lokala ögonblicksbilder av <*volym gruppnamn*> |Lokala ögonblicksbilder för säkerhetskopieringsprincipen snart slut på utrymme och kommer att ogiltigförklaras för att undvika värdskrivningsfel. |Frekventa lokala ögonblicksbilder samt hög dataomsättning i de volymer som är associerade med den här orsakar lokalt utrymme på enheten förbrukas snabbt. Ta bort lokala ögonblicksbilder som inte längre behövs. Uppdatera också lokal ögonblicksbild scheman för den här säkerhetskopieringsprincipen ska ta mindre frekventa lokala ögonblicksbilder och se till att molnögonblicksbilder tas regelbundet. Om dessa åtgärder inte utförs, kan snart vara förbrukat lokalt utrymme för de här ögonblicksbilderna och tas automatiskt bort dem för att se till att värden skrivningar har bearbetats. |
| Lokala ögonblicksbilder för <*volym gruppnamn*> har ogiltigförklarats. |Lokala ögonblicksbilder för <*volym gruppnamn*> har ogiltig och sedan tas bort eftersom de mer än det lokala utrymmet på enheten. |För att säkerställa att detta inte återkommer i framtiden, granska scheman för lokala ögonblicksbilder för den här säkerhetskopieringsprincipen och ta bort lokala ögonblicksbilder som inte längre behövs. Frekventa lokala ögonblicksbilder samt hög dataomsättning i de volymer som är associerade med den här orsaka lokalt utrymme på enheten förbrukas snabbt. |
| Återställning av <*käll-ID: N säkerhetskopieringselementets*> misslyckades. |Återställningsjobbet har misslyckats. |Om du har lokalt fixerade eller en blandning av lokalt fixerade och nivåindelade volymer i den här säkerhetskopieringsprincipen uppdatera listan säkerhetskopiering för att kontrollera att är säkerhetskopian fortfarande giltigt. Om säkerhetskopian är giltig, är det möjligt att molnet anslutningsproblem förhindrar återställningen inte kan slutföras. Lokalt fixerade volymerna som återställdes som en del av den här ögonblicksbilden gruppen har inte alla sina data som hämtas till enheten och, om du har en blandning av nivåindelade och lokalt fixerade volymer i den här ögonblicksbilden-gruppen, kan de inte synkroniserade med varandra. För att slutföra återställningen igen, ta volymerna i den här gruppen offline på värden och försök återställa igen. Observera att alla volymdata som utfördes under återställningsprocessen ändringar går förlorade. |

### <a name="networking-alerts"></a>Aviseringar för nätverk

| Aviseringstext | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Det gick inte att starta StorSimple-tjänst(er). |DataPath fel |Kontakta Microsoft-supporten om problemet kvarstår. |
| Duplicerad IP-adress har identifierats för 'Data0'. | |Systemet har upptäckt en konflikt för IP-adress ”10.0.0.1”. Nätverksresursen 'Data0' på enheten *<device1>* är offline. Se till att den här IP-adressen inte används av någon annan enhet i det här nätverket. Om du vill felsöka nätverksproblem, gå till [felsöka med cmdleten Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Kontakta administratören för hjälp med att lösa problemet. Kontakta Microsoft-supporten om problemet kvarstår. |
| IPv4 (eller IPv6)-adress för 'Data0' är offline. | |Nätverksresursen 'Data0 ”med IP-adressen 10.0.0.1. och prefixlängden ”22” på enheten *<device1>* är offline. Säkerställa att växelportarna som gränssnittet är anslutet till fungerar. Om du vill felsöka nätverksproblem, gå till [felsöka med cmdleten Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| Det gick inte att ansluta till Autentiseringstjänsten. |DataPath fel |URLthat används för att autentisera kan inte nås. Se till att brandväggsreglerna omfattar URL-mönster som angetts för StorSimple-enheten. Mer information om URL-mönster i Azure-portalen går du till https:\//aka.ms/ss-8000-network-reqs. Om du använder Azure Government-molnet, gå till URL-mönster i https:\//aka.ms/ss8000-gov-network-reqs.|

### <a name="performance-alerts"></a>Aviseringar om programprestanda

| Aviseringstext | Händelse | Mer information / rekommenderade åtgärder | |
|:--- |:--- |:--- | --- |
| Enhetsbelastningen har överskridit <*tröskelvärdet*>. |Långsammare än förväntat svarstider. |Enheten rapporterar utnyttjande hårt belastat indata/utdata. Detta kan bero på att inte fungerar som den ska. Granska de arbetsbelastningar som du har kopplat till enheten och ta reda på om det finns några, som kan flyttas till en annan enhet eller som inte längre behövs.|
| Det gick inte att starta StorSimple-tjänst(er). |DataPath fel |Kontakta Microsoft-supporten om problemet kvarstår. |

### <a name="security-alerts"></a>Säkerhetsaviseringar

| Aviseringstext | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Microsoft Support-session har startat. |Från tredje part används supportsessionen. |Bekräfta att åtkomsten tillåts. När du har vidtagit åtgärder, radera aviseringen från aviseringssidan. |
| Lösenordet för <*elementet*> går ut om <*lång tid*>. |Lösenordets giltighetstid närmar sig. |Ändra lösenordet innan det upphör att gälla. |
| Säkerhetskonfigurationsinformation saknas för <*element-ID*>. | |Volymer som är associerade med den här volymcontainern kan inte användas för att replikera din StorSimple-konfiguration. För att säkerställa att dina data lagras på ett säkert sätt, rekommenderar vi att du tar bort volymcontainern och alla volymer som är associerade med volymbehållaren. När du har vidtagit åtgärder, radera aviseringen från aviseringssidan. |
| <*antal*> inloggningsförsök misslyckades för <*element-ID*>. |Flera inloggningsförsök misslyckade. |Enheten kan vara utsatt för angrepp eller också kan en obehörig användare försöka ansluta med ett felaktigt lösenord.<ul><li>Kontakta dina behöriga användare och kontrollera att försöken gjordes från en säker källa. Om du fortsätter att se ett stort antal inloggningsförsök kan du inaktivera fjärrhantering och kontakta nätverksadministratören. När du har vidtagit åtgärder, radera aviseringen från aviseringssidan.</li><li>Kontrollera att dina Snapshot Manager-instanserna är konfigurerade med rätt lösenord. När du har vidtagit åtgärder, radera aviseringen från aviseringssidan.</li></ul>Mer information går du till [ändra ett enhetslösenord har upphört att gälla](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password). |
| En eller flera fel inträffade när krypteringsnyckeln för tjänstdata. | |Det fanns fel inträffade när krypteringsnyckeln för tjänstdata. När du har åtgärdat felförhållandena kör du den `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet från Windows PowerShell-gränssnittet för StorSimple på enheten för att uppdatera tjänsten. Kontakta Microsoft-supporten om problemet kvarstår. Radera aviseringen från aviseringssidan när du har löst problemet. |

### <a name="support-package-alerts"></a>Stöd för paketet aviseringar

| Aviseringstext | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Det gick inte att skapa supportpaket. |StorSimple kunde inte generera paketet. |Försök igen. Kontakta Microsoft-supporten om problemet kvarstår. När du har löst problemet kan du radera den här aviseringen från aviseringssidan. |

## <a name="next-steps"></a>Nästa steg

Läs mer om [StorSimple fel och felsöka problem med distribution](storsimple-8000-troubleshoot-deployment.md).

