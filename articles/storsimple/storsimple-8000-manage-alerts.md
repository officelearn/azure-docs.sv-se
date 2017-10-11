---
title: "Visa och hantera aviseringar för StorSimple 8000-serieenhet | Microsoft Docs"
description: "Beskriver StorSimple avisering villkor och allvarlighetsgrad, hur du konfigurerar aviseringar och hur du använder tjänsten StorSimple Device Manager för att hantera aviseringar."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/22/2017
ms.author: alkohli
ms.openlocfilehash: b7f9a2b7eb3dbf4cc97fac9a410359e068e67eb1
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>Använda Enhetshanteraren för StorSimple-tjänsten för att visa och hantera aviseringar för StorSimple

## <a name="overview"></a>Översikt

Den **aviseringar** bladet i StorSimple enheten Manager-tjänsten kan du granska och avmarkera StorSimple-enhet – relaterade aviseringar på en i realtid. Från det här bladet kan du centralt övervaka hälsotillstånd problem med din StorSimple-enheter och den övergripande Microsoft Azure StorSimple-lösningen.

Den här självstudiekursen beskriver vanliga avisering villkor, allvarlighetsgrad nivåer och hur du konfigurerar aviseringar. Dessutom innehåller aviseringen Snabbreferens tabeller, vilket gör att du snabbt hitta en specifik avisering och svara på lämpligt sätt.

![Sidan varningar](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Vanliga avisering villkor

StorSimple-enheten genererar aviseringar som svar på en mängd olika villkor. Följande är de vanligaste typerna av aviseringen villkor:

* **Problem med maskinvara** – dessa aviseringar meddelar dig om hälsotillståndet för maskinvaran. De kan du vet om firmware-uppgraderingar krävs om ett nätverksgränssnitt har problem, eller om det är problem med en av dina enheter.
* **Problem med nätverksanslutningen** – dessa aviseringar inträffar när det är svårt att överföra data. Kommunikationsproblem kan uppstå under överföring av data till och från Azure storage-konto eller på grund av avsaknad av anslutning mellan enheter och StorSimple Device Manager-tjänsten. Kommunikationsproblem är några av de svåraste åtgärda eftersom det finns så många felpunkter. Du bör alltid först kontrollera att nätverksanslutningen och Internet-åtkomst är tillgängliga innan du fortsätter in mer avancerad felsökning. För hjälp med felsökning kan du gå till [felsökning med cmdleten Test-Connection](storsimple-troubleshoot-deployment.md).
* **Prestandaproblem** – aviseringarna orsakas när systemet inte är presterar optimalt, till exempel när det är hårt belastat.

Dessutom kan du se aviseringar relaterade till säkerhet, uppdateringar eller jobbfel.

## <a name="alert-severity-levels"></a>Nivåer för aviseringarnas allvarlighetsgrad

Aviseringar har olika allvarlighetsgrader, beroende på inverkan som aviseringen situationen ska ha och behovet av att ett svar till aviseringen. Allvarlighetsgrader finns:

* **Kritiska** – den här aviseringen är som svar på ett villkor som påverkar systemets lyckade prestanda. Åtgärd krävs för att säkerställa att StorSimple tjänsten inte avbryts.
* **Varning** – det här tillståndet kan bli kritiska om inte matcha. Du bör undersöka situationen och vidta någon åtgärd krävs för att rensa problemet.
* **Information** – den här aviseringen innehåller information som kan vara användbar vid spåra och hantera datorn.

## <a name="configure-alert-settings"></a>Konfigurera aviseringsinställningar

Du kan välja om du vill meddelas via e-post med aviseringen villkor för var och en av dina StorSimple-enheter. Dessutom kan du identifiera andra mottagare varningsmeddelanden genom att ange sina e-postadresser i den **andra e-postmottagare** rutan, avgränsade med semikolon.

> [!NOTE]
> Du kan ange högst 20 e-postadresser per enhet.

När du aktiverar e-postmeddelanden för en enhet, kommer medlemmar i meddelandelistan över får ett e-postmeddelande varje gång en kritisk varning inträffar. Meddelanden skickas från  *storsimple-alerts-noreply@mail.windowsazure.com*  och beskriver villkoret för aviseringen. Mottagarna kan klicka på **Unsubscribe** att ta bort själva från listan över e-postavisering.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Så här aktiverar du e-postmeddelanden om aviseringar för en enhet
1. Gå till StorSimple Device Manager-tjänsten. Välj i listan med enheter och klicka på den enhet som du vill konfigurera.
2. Gå till **inställningar** > **allmänna** för enheten.

   ![Bladet med säkerhetsaviseringar](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. I den **allmänna inställningar** gå till bladet **varningsinställningar** och ange följande:
   
   1. I den **skicka e-postavisering** väljer **Ja**.
   2. I den **e-tjänstadministratörer** väljer **Ja** till har tjänstadministratören och medadministratörer för alla ta emot aviseringar skickas.
   3. I den **andra e-postmottagare** anger du de e-postadresserna för alla andra mottagare som får aviseringar skickas. Skriv namnen i formatet  *someone@somewhere.com* . Avgränsa e-postadresser med semikolon. Du kan konfigurera högst 20 e-postadresser per enhet. 
      
3. Om du vill skicka ett testmeddelande för e-post, klickar du på **skicka testmeddelandet**. Tjänsten StorSimple Enhetshanteraren visar statusmeddelanden som vidarebefordrar testmeddelande.

    ![aviseringsinställningar](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. Du ser ett meddelande när test-e-postmeddelande skickas. 
   
    ![Aviseringar testa e-postmeddelande skickas](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Om testmeddelandet inte kan skickas, visas StorSimple enheten Manager-tjänsten ett felmeddelande visas. Vänta några minuter och försök sedan att skicka din anmälan testmeddelande igen. 

5. När du har slutfört konfigurationen, klickar du på **spara**. Klicka på **Ja** när du uppmanas att bekräfta åtgärden.

     ![Aviseringar testa e-postmeddelande skickas](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Visa och spåra aviseringar

Bladet StorSimple Enhetshanteraren service sammanfattning ger dig en snabb överblick över antalet aviseringar på dina enheter, ordnade efter allvarlighetsgrad.

![Instrumentpanel för aviseringar](./media/storsimple-8000-manage-alerts/device-summary4.png)

Om du klickar på allvarlighetsgraden öppnas den **aviseringar** bladet. Resultatet innehåller bara de aviseringar som matchar den allvarlighetsgraden.

Klicka på en avisering i listan tillhandahåller ytterligare information för aviseringen, inklusive den senaste gången aviseringen rapporterades antalet förekomster av avisering på enheten och den rekommenderade åtgärden för att lösa aviseringen. Om det är en avisering om maskinvara, kommer det också identifiera maskinvarukomponenten.

![Maskinvara avisering exempel](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

Du kan kopiera Aviseringsinformationen till en textfil, om du vill skicka information till Microsoft Support. När du har följt rekommendationen varningsvillkor lokal du bör ta bort aviseringen från enheten genom att markera aviseringen i den **aviseringar** bladet och klicka på **Rensa**. Om du vill rensa flera aviseringar, väljer du varje avisering klickar du på alla kolumner utom den **avisering** kolumnen och klicka sedan på **Rensa** när du har valt alla aviseringar som ska tömmas. Observera att vissa aviseringar tas bort automatiskt när problemet är löst eller när systemet uppdaterar aviseringen med ny information.

När du klickar på **Rensa**, du har möjlighet att ange kommentarer om aviseringen och de steg som du gjorde för att lösa problemet. Kommer att tas bort vissa händelser i systemet om en annan händelse utlöses med ny information. I så fall visas följande meddelande.

![Ta bort meddelande](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Sortera och granska aviseringar

Du kan hitta mer effektivt att köra rapporter om aviseringar så att du kan granska och rensa dem i grupper. Dessutom kan den **aviseringar** bladet kan visa upp till 250 aviseringar. Om du har överskridit det antalet aviseringar visas inte alla aviseringar i standardvyn. Du kan kombinera följande fält om du vill anpassa vilka aviseringar visas:

* **Status för** – du kan visa antingen **Active** eller **avmarkerad** aviseringar. Aktiva aviseringar aktiveras fortfarande i systemet, medan avmarkerad aviseringar antingen har rensats manuellt av en administratör eller programmatiskt eftersom systemet uppdateras aviseringstillståndet med ny information.
* **Allvarlighetsgrad** – du kan visa aviseringar för alla allvarlighetsgrader (kritisk, varning, information) eller bara en viss allvarlighetsgrad, till exempel endast kritiska aviseringar.
* **Källan** – du kan visa aviseringar från alla källor eller begränsa aviseringar till de som kommer från tjänsten eller en eller flera enheter.
* **Tidsintervallet** – genom att ange den **från** och **till** datum och tidsstämplar du tittar på aviseringar under den tidsperiod som du är intresserad av.

![Lista över aviseringar](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Snabbreferens för aviseringar

I tabellerna nedan listas vissa av Microsoft Azure StorSimple-aviseringar som kan uppstå, samt ytterligare information och rekommendationer i förekommande fall. StorSimple-enhet aviseringar indelas i följande kategorier:

* [Molnet anslutningsvarningar](#cloud-connectivity-alerts)
* [Klustret aviseringar](#cluster-alerts)
* [Disaster recovery aviseringar](#disaster-recovery-alerts)
* [Maskinvara aviseringar](#hardware-alerts)
* [Jobbet aviseringar](#job-failure-alerts)
* [Lokalt Fäst volym aviseringar](#locally-pinned-volume-alerts)
* [Aviseringar för nätverk](#networking-alerts)
* [Prestandavarningar](#performance-alerts)
* [Säkerhetsaviseringar](#security-alerts)
* [Stöd för paketet aviseringar](#support-package-alerts)

### <a name="cloud-connectivity-alerts"></a>Molnet anslutningsvarningar

| Varningstexten | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Anslutningen till <*autentiseringsuppgifter molnnamn*> kan inte upprättas. |Det går inte att ansluta till lagringskontot. |Det verkar som om det kan finnas ett anslutningsproblem med din enhet. Kör den `Test-HcsmConnection` cmdlet i Windows PowerShell för StorSimple på enheten för att identifiera och åtgärda problemet. Om inställningarna är korrekta, kanske problemet med autentiseringsuppgifterna för lagringskontot som varningen skapades. I det här fallet använder den `Test-HcsStorageAccountCredential` för att avgöra om det finns problem som du kan lösa.<ul><li>Kontrollera nätverksinställningarna.</li><li>Kontrollera autentiseringsuppgifterna för ditt lagringskonto.</li></ul> |
| Vi har inte tagit emot ett pulsslag från enheten under senaste <*nummer*> minuter. |Det går inte att ansluta till enheten. |Det verkar som om det finns ett anslutningsproblem med din enhet. Använd den `Test-HcsmConnection` cmdlet i Windows PowerShell för StorSimple på enheten för att identifiera och åtgärda problemet eller kontakta nätverksadministratören. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>StorSimple-beteendet när molnet anslutningen misslyckas

Vad händer om molnet anslutningen misslyckas för enheten StorSimple körs i produktion?

Om molnet anslutningen misslyckas på din StorSimple-enhet för produktion, kan sedan beroende på din enhet status följande inträffa:

* **För den lokala data på enheten**: under en viss tid, finns inga avbrott och läser fortsätter att hanteras. Antalet utestående I/o, ökar och överskrider en gräns startade läsningar misslyckas.

    Beroende på mängden data på enheten fortsätter också skrivningar ska ske första några timmar efter störningar i anslutningen för molnet. Skrivningar kommer sedan långsammare och slutligen börjar misslyckas om molnet anslutningen avbryts under flera timmar. (Det är tillfälligt lagringsutrymme på enheten för data som ska skickas till molnet. Det här området rensas när data skickas. Om anslutningen misslyckas, data i den här lagringsutrymmet inte ska skickas till molnet och IO misslyckas.)
* **För data i molnet**: för de flesta molnet anslutningsfel, returneras ett fel. När anslutningen har återställts återupptas IOs utan att användaren behöver aktivera volymen online. I sällsynta fall kan det krävas åtgärder från användaren för att volymen online från Azure-portalen.
* **För molnögonblicksbilder pågår**: åtgärden försöks några gånger inom 4 – 5 timmar och molnögonblicksbilder misslyckas om anslutningen inte har återställts.

### <a name="cluster-alerts"></a>Klustret aviseringar

| Varningstexten | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Enheten kunde inte över <*enhetsnamn*>. |Enheten är i underhållsläge. |Enheten växlar över på grund av att ange eller lämnar underhållsläge. Detta är normalt och ingen åtgärd krävs. När du har godkänt den här aviseringen, avmarkerar du den från sidan aviseringar. |
| Enheten kunde inte över <*enhetsnamn*>. |Enhetens inbyggda programvara eller programvara har precis uppdaterats. |Det uppstod ett kluster på grund av en uppdatering. Detta är normalt och ingen åtgärd krävs. När du har godkänt den här aviseringen, avmarkerar du den från sidan aviseringar. |
| Enheten kunde inte över <*enhetsnamn*>. |Domänkontrollanten stängdes av eller startas om. |Det gick inte över enheten eftersom den aktiva styrenheten stängdes av eller startas om av en administratör. Ingen åtgärd krävs. När du har godkänt den här aviseringen, avmarkerar du den från sidan aviseringar. |
| Enheten kunde inte över <*enhetsnamn*>. |Planerad redundans. |Kontrollera att det var en planerad redundans. Ta bort aviseringen från sidan aviseringar när du har vidtagit lämpliga åtgärder. |
| Enheten kunde inte över <*enhetsnamn*>. |Oplanerad redundans. |StorSimple är utformat för att automatiskt återställa från oplanerad växling vid fel. Kontakta Microsoft Support om du ser ett stort antal aviseringarna. |
| Enheten kunde inte över <*enhetsnamn*>. |Andra/okänd orsak. |Kontakta Microsoft Support om du ser ett stort antal aviseringarna. Ta bort aviseringen från sidan aviseringar när problemet är löst. |
| En tjänst för kritiska enheter rapporterar status som misslyckad. |DataPath tjänstfel. |Kontakta Microsoft Support för hjälp. |
| Den virtuella IP-adressen för nätverksgränssnittet <*DATA #*> rapporterar status som misslyckad. |Andra/okänd orsak. |Ibland temporära tillstånd kan orsaka aviseringarna. Om så är fallet, sedan rensas den här aviseringen automatiskt efter en stund. Kontakta Microsoft-supporten om problemet kvarstår. |
| Den virtuella IP-adressen för nätverksgränssnittet <*DATA #*> rapporterar status som misslyckad. |Gränssnittsnamn: <*DATA #*> IP-adress <IP address> kunde inte försättas online eftersom en dubbel IP-adress har identifierats i nätverket. |Se till att IP-adressdubblett tas bort från nätverket eller konfigurera om gränssnittet med en annan IP-adress. |

### <a name="disaster-recovery-alerts"></a>Disaster recovery aviseringar

| Varningstexten | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Återställningsåtgärder kunde inte återställa alla inställningar för den här tjänsten. Konfigurationsdata för enheten är i ett inkonsekvent tillstånd för vissa enheter. |Datainkonsekvens upptäcktes när katastrofåterställning. |Krypterade data på tjänsten är inte synkroniserad med på enheten. Auktorisera enheten <*enhetsnamn*> från StorSimple Enhetshanteraren för att starta synkroniseringen. Använda Windows PowerShell-gränssnitt för StorSimple för att köra den `Restore-HcsmEncryptedServiceData` på enhet <*enhetsnamn*> cmdlet, att det gamla lösenordet som indata till denna cmdlet ska återställa säkerheten. Kör sedan den `Invoke-HcsmServiceDataEncryptionKeyChange` att uppdatera krypteringsnyckeln för tjänstdata. Ta bort aviseringen från sidan aviseringar när du har vidtagit lämpliga åtgärder. |

### <a name="hardware-alerts"></a>Maskinvara aviseringar

| Varningstexten | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Maskinvarukomponent <*komponent-ID*> rapporterar status som <*status*>. | |Ibland temporära tillstånd kan orsaka aviseringarna. I så fall, kommer aviseringen rensas automatiskt efter en stund. Kontakta Microsoft-supporten om problemet kvarstår. |
| Passiva domänkontrollanten fungerar inte. |Den passiva (sekundär) inte fungerar. |Enheten är i drift, men en av dina domänkontrollanter är fel. Försök att starta om den styrenheten. Kontakta Microsoft Support om problemet kvarstår. |

### <a name="job-failure-alerts"></a>Jobbet aviseringar

| Varningstexten | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Säkerhetskopiering av <*grupp-ID för volymen*> misslyckades. |Säkerhetskopieringsjobbet misslyckades. |Problem med nätverksanslutningen kan hindra att säkerhetskopieringen från en lyckad. Om det inte finns några anslutningsproblem, har du nått det maximala antalet säkerhetskopieringar. Ta bort alla säkerhetskopieringar som inte längre behövs och försök igen. Ta bort aviseringen från sidan aviseringar när du har vidtagit lämpliga åtgärder. |
| Klona av <*datakällan säkerhetskopiering element-ID: N*> till <*mål volym serienummer*> misslyckades. |Det gick inte att klona. |Uppdatera listan säkerhetskopiering för att kontrollera att säkerhetskopian är fortfarande giltig. Om säkerhetskopieringen är giltiga, är det möjligt att molnet anslutningsproblem hindrar åtgärden klona från en lyckad. Om det inte finns några anslutningsproblem, har du nått lagringsgränsen. Ta bort alla säkerhetskopieringar som inte längre behövs och försök igen. När du har vidtagit lämpliga åtgärder för att åtgärda problemet genom att ta bort aviseringen från sidan aviseringar. |
| Återställningen av <*datakällan säkerhetskopiering element-ID: N*> misslyckades. |Det gick inte att återställa. |Uppdatera listan säkerhetskopiering för att kontrollera att säkerhetskopian är fortfarande giltig. Om säkerhetskopieringen är giltiga, är det möjligt att molnet anslutningsproblem förhindrar återställningen från en lyckad. Om det inte finns några anslutningsproblem, har du nått lagringsgränsen. Ta bort alla säkerhetskopieringar som inte längre behövs och försök igen. När du har vidtagit lämpliga åtgärder för att åtgärda problemet genom att ta bort aviseringen från sidan aviseringar. |

### <a name="locally-pinned-volume-alerts"></a>Lokalt Fäst volym aviseringar

| Varningstexten | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Skapa lokal volym <*volymnamn*> misslyckades. |Det gick inte att jobbet för att skapa volymen. <*Felmeddelandet som motsvarar felkoden misslyckade*>. |Problem med nätverksanslutningen kan hindra att skapandeåtgärd utrymme från en lyckad. Lokalt fästa volymer etableras tjockt, vilket och skapa utrymme innebär läcka nivåindelade volymer till molnet. Om det inte finns några anslutningsproblem, kan du ha uttömt lokalt utrymme på enheten. Avgör om utrymme finns på enheten innan du gör den här åtgärden. |
| Utökningen av lokal volym <*volymnamn*> misslyckades. |Volymen ändras jobbet misslyckades på grund av att <*felmeddelande som motsvarar felkoden misslyckade*>. |Problem med nätverksanslutningen kan hindra att volymen expansion åtgärden från en lyckad. Lokalt fästa volymer etableras tjockt, vilket och utöka det befintliga utrymmet innebär läcka nivåindelade volymer till molnet. Om det inte finns några anslutningsproblem, kan du ha uttömt lokalt utrymme på enheten. Avgör om utrymme finns på enheten innan du gör den här åtgärden. |
| Konvertering av volymen <*volymnamn*> misslyckades. |Volymen konverteringsjobbet att konvertera volymtyp från lokalt fasta nivåer misslyckades. |Konvertering av volymen från typen lokalt Fäst nivåer gick inte att slutföra. Se till att det inte finns några anslutningsproblem som hindrar åtgärden slutförs. För felsökning av anslutningsmöjligheter problem går du till [felsökning med cmdleten Test-HcsmConnection](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Den ursprungliga lokalt Fäst volymen har nu markerats som en nivåindelad volym eftersom vissa data från lokalt Fäst volym har hamnat i molnet under konverteringen. Den resulterande nivåindelade volymen är fortfarande använder lokalt utrymme på enheten som inte kan frigöras för framtida lokala volymer.<br>Lös eventuella problem med nätverksanslutningen, ta bort aviseringen och konvertera den här volymen tillbaka till den ursprungliga typen lokalt Fäst volym att se till att alla data görs tillgänglig lokalt igen. |
| Konvertering av volymen <*volymnamn*> misslyckades. |Volymen konverteringsjobbet att konvertera volymtyp från nivåer att lokalt Fäst misslyckades. |Konvertering av volymen från typen nivåer att lokalt Fäst kunde inte slutföras. Se till att det inte finns några anslutningsproblem som hindrar åtgärden slutförs. För felsökning av anslutningsmöjligheter problem går du till [felsökning med cmdleten Test-HcsmConnection](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Den ursprungliga nivåindelade volymen nu markerats som en lokalt Fäst volym som en del av processen fortsätter att ha data som finns i molnet, medan tjockt, vilket allokerade utrymmet på enheten för den här volymen kan inte längre vara frigöras för framtida lokala volymer.<br>Lös eventuella problem med nätverksanslutningen, ta bort aviseringen och konvertera den här volymen tillbaka till den ursprungliga typen nivåindelad volym så lokalt utrymme etableras tjockt, vilket på enheten kan vara frigöras. |
| Snart lokalt utrymme förbrukningen för lokala ögonblicksbilder av <*volym gruppnamn*> |Lokala ögonblicksbilder för principen för säkerhetskopiering kan snart slut på utrymme och ogiltiga för att undvika skrivfel för värden. |Ofta lokala ögonblicksbilder tillsammans med en hög dataomsättningen i de volymer som är associerade med den här gruppen säkerhetskopieringsprincip orsakar lokalt utrymme på enheten som ska konsumeras snabbt. Ta bort alla lokala ögonblicksbilder som inte längre behövs. Uppdatera också lokal ögonblicksbild scheman för denna säkerhetskopieringsprincip ta mindre ofta lokala ögonblicksbilder och kontrollera att molnögonblicksbilder tas regelbundet. Om dessa åtgärder inte utförs, lokala utrymmet för dessa ögonblicksbilder kan snart slut och tas automatiskt bort dem för att se till att värden skrivningar fortsätter att bearbetas. |
| Lokala ögonblicksbilder för <*volym gruppnamn*> är ogiltiga. |Lokala ögonblicksbilder för <*volym gruppnamn*> ogiltig och sedan tas bort eftersom de mer än lokalt utrymme på enheten. |Granska lokal ögonblicksbild scheman för denna säkerhetskopieringsprincip för att säkerställa att detta inte återkommer i framtiden, och ta bort alla lokala ögonblicksbilder som inte längre behövs. Ofta lokala ögonblicksbilder tillsammans med en hög dataomsättningen i de volymer som är associerade med den här gruppen för säkerhetskopieringsprincip kan orsaka lokalt utrymme på enheten som ska konsumeras snabbt. |
| Återställningen av <*datakällan säkerhetskopiering element-ID: N*> misslyckades. |Återställningsjobbet har misslyckats. |Om du har fäst lokalt eller en blandning av lokalt Fäst och nivåindelade volymer i denna säkerhetskopieringsprincip uppdatera listan säkerhetskopiering för att kontrollera att är säkerhetskopian fortfarande giltig. Om säkerhetskopieringen är giltiga, är det möjligt att molnet anslutningsproblem förhindrar återställningen från en lyckad. Lokalt fästa volymer som återställts som en del av den här gruppen med ögonblicksbilder har inte alla sina data hämtas till enheten och, om du har en blandning av nivåindelade och lokalt fästa volymer i den här ögonblicksbilden-gruppen, kan de inte synkroniserade med varandra. För att slutföra återställningen, åtgärda volymerna i den här gruppen offline på värden och försök utföra återställningen igen. Observera att alla ändringar som utfördes under återställningsprocessen volymens data går förlorade. |

### <a name="networking-alerts"></a>Aviseringar för nätverk

| Varningstexten | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Det gick inte att starta StorSimple-tjänst(er). |DataPath fel |Kontakta Microsoft-supporten om problemet kvarstår. |
| IP-adressdubblett upptäcktes för 'Data0'. | |Systemet har upptäckt en konflikt för IP-adress ”10.0.0.1”. Nätverksresursen 'Data0' på enheten  *<device1>*  är offline. Se till att IP-adressen inte används av andra entiteter i nätverket. Felsökning av nätverksproblem med går du till [felsökning med cmdleten Get-NetAdapter](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Kontakta nätverksadministratören för hjälp med att lösa problemet. Kontakta Microsoft-supporten om problemet kvarstår. |
| IPv4 (eller IPv6) adress 'Data0' är offline. | |Nätverksresursen 'Data0' med IP-adressen 10.0.0.1. och prefixlängd '22' på enheten  *<device1>*  är offline. Säkerställa att de växelportar som det här gränssnittet är anslutet fungerar. Felsökning av nätverksproblem med går du till [felsökning med cmdleten Get-NetAdapter](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| Det gick inte att ansluta till Autentiseringstjänsten. |DataPath fel |URLthat används för att autentisera kan inte nås. Se till att brandväggsreglerna URL-mönster som angetts för StorSimple-enhet. Mer information om URL-mönster i Azure-portalen går du till https://aka.ms/ss-8000-network-reqs. Om du använder Azure offentliga moln, gå till URL-mönster i https://aka.ms/ss8000-gov-network-reqs.|

### <a name="performance-alerts"></a>Prestandavarningar

| Varningstexten | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Enheten belastningen har överskridit <*tröskelvärdet*>. |Långsammare än förväntat svarstider. |Enheten rapporterar användning under en hög i/o-belastning. Det kan leda till enheten inte fungerar så väl som den ska. Granska arbetsbelastningar som du har kopplat till enheten och kontrollera om det finns några som kunde flyttas till en annan enhet eller som inte längre behövs.| Det gick inte att starta StorSimple-tjänst(er). |DataPath fel |Kontakta Microsoft-supporten om problemet kvarstår. |och aktuell status, gå till [använda Enhetshanteraren för StorSimple-tjänsten för att övervaka din enhet](storsimple-monitor-device.md) |

### <a name="security-alerts"></a>Säkerhetsaviseringar

| Varningstexten | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Microsoft Support-sessionen har startat. |Tredjeparts-åt stöd session. |Bekräfta att du har denna behörighet. Ta bort aviseringen från sidan aviseringar när du har vidtagit lämpliga åtgärder. |
| Lösenordet för <*elementet*> upphör att gälla om <*tid*>. |Lösenordets giltighetstid närmar sig. |Ändra lösenordet innan det upphör att gälla. |
| Information om säkerhetskonfigurationen saknas för <*element-ID*>. | |Volymer som är associerade med den här volymbehållaren kan inte användas för att replikera StorSimple-konfigurationen. För att säkerställa att dina data lagras på ett säkert sätt, rekommenderar vi att du tar bort volymbehållaren och alla volymer som är associerade med volymbehållaren. Ta bort aviseringen från sidan aviseringar när du har vidtagit lämpliga åtgärder. |
| <*antal*> inloggningsförsök misslyckades för <*element-ID*>. |Flera inloggningsförsök misslyckade. |Enheten kan vara utsatt för angrepp eller också kan en obehörig användare försöka ansluta med ett felaktigt lösenord.<ul><li>Kontakta din behöriga användare och kontrollera att dessa försök var från en säker källa. Överväg att inaktivera fjärrhantering och kontakta nätverksadministratören om du fortsätter att se stort antal misslyckade inloggningsförsök. Ta bort aviseringen från sidan aviseringar när du har vidtagit lämpliga åtgärder.</li><li>Kontrollera att din Snapshot Manager-instanserna är konfigurerade med rätt lösenord. Ta bort aviseringen från sidan aviseringar när du har vidtagit lämpliga åtgärder.</li></ul>Mer information finns på [ändrar du ett enhetslösenord har upphört att gälla](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password). |
| En eller flera fel inträffade vid ändring av krypteringsnyckeln för tjänstdata. | |Det uppstod fel påträffades vid ändring av krypteringsnyckeln för tjänstdata. När du har åtgärdat felvillkor, kör du den `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet i Windows PowerShell för StorSimple på enheten för att uppdatera tjänsten. Kontakta Microsoft-supporten om problemet kvarstår. Ta bort aviseringen från sidan aviseringar när du har löst problemet. |

### <a name="support-package-alerts"></a>Stöd för paketet aviseringar

| Varningstexten | Händelse | Mer information / rekommenderade åtgärder |
|:--- |:--- |:--- |
| Det gick inte att skapa för stödpaketet. |StorSimple gick inte att skapa paketet. |Försök igen. Kontakta Microsoft-supporten om problemet kvarstår. Ta bort aviseringen från sidan aviseringar när du har löst problemet. |

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [StorSimple fel och felsöka en operativa enhet](storsimple-troubleshoot-operational-device.md).

