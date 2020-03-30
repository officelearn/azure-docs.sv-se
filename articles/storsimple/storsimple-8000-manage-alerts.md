---
title: Visa och hantera aviseringar för StorSimple 8000-seriens enhet
description: Beskriver LagringSimples varningsvillkor och allvarlighetsgrad, hur du konfigurerar varningsmeddelanden och hur du använder Tjänsten StorSimple Device Manager för att hantera aviseringar.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: ff50836e1438b8d35f26ddfdf165084406f52faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267825"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>Använda Tjänsten StorSimple Device Manager för att visa och hantera StorSimple-aviseringar

## <a name="overview"></a>Översikt

**Bladet Aviseringar** i StorSimple Device Manager-tjänsten är ett sätt för dig att granska och rensa StorSimple-enhetsrelaterade aviseringar i realtid. Från det här bladet kan du centralt övervaka hälsoproblemen för dina StorSimple-enheter och den övergripande Microsoft Azure StorSimple-lösningen.

Den här självstudien beskriver vanliga varningsvillkor, allvarlighetsgrad för aviseringar och hur du konfigurerar varningsmeddelanden. Dessutom innehåller den snabbreferenstabeller för aviseringar, vilket gör att du snabbt kan hitta en viss avisering och svara på rätt sätt.

![Sidan Aviseringar](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Vanliga varningsvillkor

Din StorSimple-enhet genererar aviseringar som svar på en mängd olika villkor. Följande är de vanligaste typerna av varningsvillkor:

* **Maskinvaruproblem** – Dessa aviseringar berättar om maskinvarans hälsa. De låter dig veta om firmware uppgraderingar behövs, om ett nätverksgränssnitt har problem, eller om det finns ett problem med en av dina dataenheter.
* **Anslutningsproblem** – Dessa aviseringar inträffar när det är svårt att överföra data. Kommunikationsproblem kan uppstå under överföring av data till och från Azure-lagringskontot eller på grund av bristande anslutning mellan enheterna och Tjänsten StorSimple Device Manager. Kommunikationsproblem är några av de svåraste att fixa eftersom det finns så många felpunkter. Du bör alltid först kontrollera att nätverksanslutning och Internet-åtkomst är tillgängliga innan du fortsätter till mer avancerad felsökning. Om du vill ha hjälp med felsökning går du till [Felsök med cmdleten Test-Connection](storsimple-8000-troubleshoot-deployment.md).
* **Prestandaproblem** – Dessa aviseringar orsakas när systemet inte fungerar optimalt, till exempel när det är under en tung belastning.

Dessutom kan du se aviseringar relaterade till säkerhet, uppdateringar eller jobbfel.

## <a name="alert-severity-levels"></a>Allvarlighetsgrad för aviseringar

Aviseringar har olika allvarlighetsgrad, beroende på vilken inverkan aviseringssituationen kommer att ha och behovet av ett svar på aviseringen. Allvarlighetsgraderna är:

* **Kritisk** – Den här aviseringen är ett svar på ett tillstånd som påverkar systemets prestanda. Åtgärder krävs för att säkerställa att StorSimple-tjänsten inte avbryts.
* **Varning** – Detta tillstånd kan bli kritiskt om det inte löses. Du bör undersöka situationen och vidta alla åtgärder som krävs för att rensa problemet.
* **Information** – Den här aviseringen innehåller information som kan vara användbar för att spåra och hantera ditt system.

## <a name="configure-alert-settings"></a>Konfigurera varningsinställningar

Du kan välja om du vill bli meddelad via e-post om varningsvillkor för var och en av dina StorSimple-enheter. Dessutom kan du identifiera andra mottagare av varningsmeddelanden genom att ange deras e-postadresser i rutan **Andra e-postmottagare,** avgränsade med semikolon.

> [!NOTE]
> Du kan ange högst 20 e-postadresser per enhet.

När du har aktiverat e-postavisering för en enhet får medlemmar i meddelandelistan ett e-postmeddelande varje gång en kritisk avisering inträffar. Meddelandena skickas från *storsimple-alerts-noreply\@mail.windowsazure.com* och kommer att beskriva varningsvillkoret. Mottagarna kan klicka på **Avsluta prenumeration** om du vill ta bort sig själva från e-postmeddelandet.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Så här aktiverar du e-postmeddelanden om aviseringar för en enhet
1. Gå till StorSimple Device Manager-tjänsten. Välj och klicka på den enhet som du vill konfigurera i listan över enheter.
2. Gå till **Inställningar** > **allmänt** för enheten.

   ![Bladet Varningar](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. I bladet **Allmänna inställningar** går du till **Varningsinställningar och** ställer in följande:
   
   1. Välj **JA**i fältet **Skicka e-postmeddelande.**
   2. I fältet **Administratörer för e-posttjänst** väljer du **JA** om du vill att tjänstadministratören och alla medadministratörer ska få aviseringarna.
   3. I fältet **Övriga e-postmottagare** anger du e-postadresserna till alla andra mottagare som ska ta emot aviseringarna. Ange namn i det format *som någon\@somewhere.com*. Använd semikolon för att separera e-postadresserna. Du kan konfigurera högst 20 e-postadresser per enhet. 
      
3. Om du vill skicka ett testmeddelande via e-post klickar du på **Skicka testmeddelande**. Tjänsten StorSimple Device Manager visar statusmeddelanden när testmeddelandet vidarebefordras.

    ![Varningsinställningar](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. Du ser ett meddelande när testmeddelandet skickas. 
   
    ![E-postmeddelande med e-postmeddelande för aviseringar som skickats](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Om testmeddelandet inte kan skickas visas ett lämpligt felmeddelande i Tjänsten StorSimple Device Manager. Vänta några minuter och försök sedan skicka testmeddelandet igen. 

5. När du har slutfört konfigurationen klickar du på **Spara**. Klicka på **Ja** när du uppmanas att bekräfta åtgärden.

     ![E-postmeddelande med e-postmeddelande för aviseringar som skickats](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Visa och spåra aviseringar

Sammanfattningsbladet för Tjänsten StorSimple Device Manager ger dig en snabb överblick över antalet aviseringar på dina enheter, ordnade efter allvarlighetsgrad.

![Instrumentpanelen Aviseringar](./media/storsimple-8000-manage-alerts/device-summary4.png)

Om du klickar på **allvarlighetsgraden** öppnas bladet Aviseringar. Resultaten innehåller endast aviseringar som matchar allvarlighetsgraden.

Om du klickar på en avisering i listan får du ytterligare information om aviseringen, inklusive den senaste gången aviseringen rapporterades, antalet förekomster av aviseringen på enheten och den rekommenderade åtgärden för att lösa aviseringen. Om det är en maskinvaruavisering identifieras även maskinvarukomponenten.

![Exempel på maskinvaruvarning](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

Du kan kopiera aviseringsinformationen till en textfil om du behöver skicka informationen till Microsoft Support. När du har följt rekommendationen och löst **varningsvillkoret** lokalt bör du rensa aviseringen från enheten genom att välja aviseringen i bladet Aviseringar och klicka på **Rensa**. Om du vill ta bort flera aviseringar markerar du varje avisering, klickar på en kolumn utom kolumnen **Avisering** och klickar sedan på **Rensa** när du har markerat alla aviseringar som ska rensas. Observera att vissa aviseringar rensas automatiskt när problemet är löst eller när systemet uppdaterar aviseringen med ny information.

När du klickar på **Rensa**får du möjlighet att ge kommentarer om aviseringen och de åtgärder som du tog för att lösa problemet. Vissa händelser rensas av systemet om en annan händelse utlöses med ny information. I så fall visas följande meddelande.

![Rensa varningsmeddelande](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Sortera och granska aviseringar

Det kan vara mer effektivt att köra rapporter om aviseringar så att du kan granska och rensa dem i grupper. Dessutom kan **bladet Aviseringar** visa upp till 250 varningar. Om du har överskridit antalet aviseringar visas inte alla aviseringar i standardvyn. Du kan kombinera följande fält för att anpassa vilka aviseringar som visas:

* **Status** – Du kan visa antingen **aktiva** eller **rensade** aviseringar. Aktiva aviseringar utlöses fortfarande på datorn, medan rensade aviseringar har antingen rensats manuellt av en administratör eller programmässigt rensats eftersom systemet uppdaterade varningsvillkoret med ny information.
* **Allvarlighetsgrad** – Du kan visa aviseringar om alla allvarlighetsgrader (kritisk, varning, information) eller bara en viss allvarlighetsgrad, till exempel endast kritiska aviseringar.
* **Källa** – Du kan visa aviseringar från alla källor eller begränsa aviseringarna till de som kommer från antingen tjänsten eller en eller alla enheter.
* **Tidsintervall** – Genom att ange datum och tidsstämplar **från** **och till** kan du titta på aviseringar under den tidsperiod som du är intresserad av.

![Lista över aviseringar](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Snabbreferens för varningar

I följande tabeller visas några av microsoft Azure StorSimple-aviseringar som du kan stöta på, samt ytterligare information och rekommendationer där sådana finns. StorSimple-enhetsaviseringar kan delas in i någon av följande kategorier:

* [Aviseringar för molnanslutning](#cloud-connectivity-alerts)
* [Klusteraviseringar](#cluster-alerts)
* [Varningar för haveriberedskap](#disaster-recovery-alerts)
* [Maskinvaruvarningar](#hardware-alerts)
* [Aviseringar om jobbfel](#job-failure-alerts)
* [Lokalt fästa volymaviseringar](#locally-pinned-volume-alerts)
* [Aviseringar om nätverk](#networking-alerts)
* [Prestandavarningar](#performance-alerts)
* [Säkerhetsaviseringar](#security-alerts)
* [Aviseringar för supportpaket](#support-package-alerts)

### <a name="cloud-connectivity-alerts"></a>Aviseringar för molnanslutning

| Varningstext | Händelse | Mer information /rekommenderade åtgärder |
|:--- |:--- |:--- |
| Det går inte att upprätta anslutning till <*molnautentiserings>namn.* |Det går inte att ansluta till lagringskontot. |Det ser ut som det kan finnas ett anslutningsproblem med din enhet. Kör `Test-HcsmConnection` cmdleten från Windows PowerShell-gränssnittet för StorSimple på enheten för att identifiera och åtgärda problemet. Om inställningarna är korrekta kan problemet bero på autentiseringsuppgifterna för det lagringskonto som aviseringen har väckts för. I det här `Test-HcsStorageAccountCredential` fallet använder du cmdleten för att avgöra om det finns problem som du kan lösa.<ul><li>Kontrollera nätverksinställningarna.</li><li>Kontrollera dina lagringskontouppgifter.</li></ul> |
| Vi har inte fått ett hjärtslag från din enhet under de senaste <*nummer*> minuter. |Det går inte att ansluta till enheten. |Det ser ut som det finns ett anslutningsproblem med din enhet. Använd `Test-HcsmConnection` cmdleten från Windows PowerShell-gränssnittet för StorSimple på enheten för att identifiera och åtgärda problemet eller kontakta nätverksadministratören. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>StorSimple-beteende när molnanslutningen misslyckas

Vad händer om molnanslutningen misslyckas för min StorSimple-enhet som körs i produktion?

Om molnanslutningen misslyckas på storsmultnadiska produktionsenheten kan följande, beroende på enhetens tillstånd, inträffa:

* **För lokala data på din enhet**: Under en tid kommer det inte att finnas några störningar och läsningar kommer att fortsätta att serveras. Men eftersom antalet utestående IOs ökar och överskrider en gräns, kan läsningarna börja misslyckas.

    Beroende på mängden data på enheten fortsätter skrivningarna också att inträffa under de första timmarna efter avbrott i molnanslutningen. Skrivarna kommer sedan att sakta ner och så småningom börja misslyckas om molnanslutningen störs i flera timmar. (Det finns tillfällig lagring på enheten för data som ska skickas till molnet. Det här området rensas när data skickas. Om anslutningen misslyckas kommer data i det här lagringsutrymmet inte att skickas till molnet och IO misslyckas.)
* **För data i molnet**: För de flesta molnanslutningsfel returneras ett fel. När anslutningen har återställts återupptas IOs utan att användaren behöver ansluta volymen. I sällsynta fall kan det krävas att användaren behöver göra det för att få tillbaka volymen online från Azure-portalen.
* **För ögonblicksbilder av molnet som pågår:** Åtgärden görs om några gånger inom 4-5 timmar och om anslutningen inte återställs misslyckas ögonblicksbilderna i molnet.

### <a name="cluster-alerts"></a>Klusteraviseringar

| Varningstext | Händelse | Mer information /rekommenderade åtgärder |
|:--- |:--- |:--- |
| Enheten gick inte över till <*enhetsnamn*>. |Enheten är i underhållsläge. |Enheten gick över på grund av att anordningen gick in i eller avslutar underhållsläge. Detta är normalt och inga åtgärder behövs. När du har bekräftat den här aviseringen rensar du den från sidan aviseringar. |
| Enheten gick inte över till <*enhetsnamn*>. |Enhetens inbyggda programvara eller programvara har precis uppdaterats. |Det fanns en kluster redundans på grund av en uppdatering. Detta är normalt och inga åtgärder behövs. När du har bekräftat den här aviseringen rensar du den från sidan aviseringar. |
| Enheten gick inte över till <*enhetsnamn*>. |Controllern stängdes av eller startades om. |Enheten gick över eftersom den aktiva styrenheten stängdes av eller startades om av en administratör. Inga åtgärder behövs. När du har bekräftat den här aviseringen rensar du den från sidan aviseringar. |
| Enheten gick inte över till <*enhetsnamn*>. |Planerad redundans. |Kontrollera att detta var en planerad redundans. När du har vidtagit lämpliga åtgärder avmarkerar du den här aviseringen från sidan aviseringar. |
| Enheten gick inte över till <*enhetsnamn*>. |Oplanerad redundans. |StorSimple är byggd för att automatiskt återställa från oplanerade redundans. Om du ser ett stort antal av dessa aviseringar kontaktar du Microsoft Support. |
| Enheten gick inte över till <*enhetsnamn*>. |Annan/okänd orsak. |Om du ser ett stort antal av dessa aviseringar kontaktar du Microsoft Support. När problemet har lösts rensar du den här aviseringen från sidan aviseringar. |
| En kritisk enhetstjänst rapporterar status som misslyckad. |Datapath-tjänsten misslyckades. |Kontakta Microsoft Support för hjälp. |
| Virtuell IP-adress för nätverksgränssnitt <*DATA #*> rapporterar status som misslyckades. |Annan/okänd orsak. |Ibland kan tillfälliga villkor orsaka dessa aviseringar. Om så är fallet rensas den här aviseringen automatiskt efter en tid. Om problemet kvarstår kontaktar du Microsoft Support. |
| Virtuell IP-adress för nätverksgränssnitt <*DATA #*> rapporterar status som misslyckades. |Gränssnittsnamn: <*DATA #*> IP-adress `<IP address>` kan inte anslutas eftersom en dubblett-IP-adress upptäcktes i nätverket. |Kontrollera att den dubbla IP-adressen tas bort från nätverket eller konfigurera om gränssnittet med en annan IP-adress. |

### <a name="disaster-recovery-alerts"></a>Varningar för haveriberedskap

| Varningstext | Händelse | Mer information /rekommenderade åtgärder |
|:--- |:--- |:--- |
| Återställningsåtgärder kunde inte återställa alla inställningar för den här tjänsten. Enhetskonfigurationsdata är i ett inkonsekvent tillstånd för vissa enheter. |Datainkonsekvens har upptäckts efter haveriberedskap. |Krypterade data på tjänsten synkroniseras inte med dem på enheten. Auktorisera enhetens <*enhetsnamn*> från StorSimple Enhetshanteraren för att starta synkroniseringsprocessen. Använd Windows PowerShell-gränssnittet för StorSimple för att köra `Restore-HcsmEncryptedServiceData` enhetsnamnet på enheten <> cmdlet, vilket ger det gamla lösenordet som en indata till den här cmdleten för att återställa säkerhetsprofilen. *device name* Kör sedan `Invoke-HcsmServiceDataEncryptionKeyChange` cmdleten för att uppdatera krypteringsnyckeln för tjänstdata. När du har vidtagit lämpliga åtgärder avmarkerar du den här aviseringen från sidan aviseringar. |

### <a name="hardware-alerts"></a>Maskinvaruvarningar

| Varningstext | Händelse | Mer information /rekommenderade åtgärder |
|:--- |:--- |:--- |
| Maskinvarukomponent <*komponent-ID*> rapporterar status som <*status*>. | |Ibland kan tillfälliga villkor orsaka dessa aviseringar. Om så är fallet rensas den här aviseringen automatiskt efter en viss tid. Om problemet kvarstår kontaktar du Microsoft Support. |
| Passiv regulator inte fungerar. |Den passiva (sekundära) styrenheten fungerar inte. |Enheten är i drift, men en av handkontrollerna fungerar inte. Försök att starta om handkontrollen. Om problemet inte är löst kontaktar du Microsoft Support. |

### <a name="job-failure-alerts"></a>Aviseringar om jobbfel

| Varningstext | Händelse | Mer information /rekommenderade åtgärder |
|:--- |:--- |:--- |
| Det gick inte att säkerhetskopiera <*källvolymgrupps-ID*>. |Säkerhetskopieringsjobbet misslyckades. |Anslutningsproblem kan hindra säkerhetskopieringen från att slutföras. Om det inte finns några anslutningsproblem kan du ha nått det maximala antalet säkerhetskopior. Ta bort alla säkerhetskopior som inte längre behövs och försök igen. När du har vidtagit lämpliga åtgärder avmarkerar du den här aviseringen från sidan aviseringar. |
| Det gick inte att klona <*källsäkerhetselement-ID:er*> för att <*målvolymserienummer*> misslyckades. |Klonjobbet misslyckades. |Uppdatera säkerhetskopian för att kontrollera att säkerhetskopian fortfarande är giltig. Om säkerhetskopian är giltig är det möjligt att molnanslutningsproblem förhindrar att klonåtgärden slutförs. Om det inte finns några anslutningsproblem kan du ha nått lagringsgränsen. Ta bort alla säkerhetskopior som inte längre behövs och försök igen. När du har vidtagit lämpliga åtgärder för att lösa problemet rensar du den här aviseringen från sidan aviseringar. |
| Det gick inte att återställa <*källsäkerhetselement-ID:>.* |Återställningsjobbet misslyckades. |Uppdatera säkerhetskopian för att kontrollera att säkerhetskopian fortfarande är giltig. Om säkerhetskopian är giltig är det möjligt att molnanslutningsproblem förhindrar att återställningen slutförs. Om det inte finns några anslutningsproblem kan du ha nått lagringsgränsen. Ta bort alla säkerhetskopior som inte längre behövs och försök igen. När du har vidtagit lämpliga åtgärder för att lösa problemet rensar du den här aviseringen från sidan aviseringar. |

### <a name="locally-pinned-volume-alerts"></a>Lokalt fästa volymaviseringar

| Varningstext | Händelse | Mer information /rekommenderade åtgärder |
|:--- |:--- |:--- |
| Det gick inte att skapa lokala <*volymnamn*>. |Jobbet för att skapa volym har misslyckats. <*Felmeddelande som motsvarar den misslyckade felkoden*>. |Anslutningsproblem kan förhindra att åtgärden för att skapa utrymme slutförs. Lokalt fästa volymer etableras tjockt och processen att skapa utrymme innebär att spilla nivåindelade volymer till molnet. Om det inte finns några anslutningsproblem kan du ha uttömt det lokala utrymmet på enheten. Ta reda på om det finns utrymme på enheten innan du försöker utföra den här åtgärden igen. |
| Det gick inte att expandera det lokala volymnamnet> <*volymnamn.* |Jobbet för volymändring misslyckades på grund av <*felmeddelande som motsvarar den misslyckade felkoden*>. |Anslutningsproblem kan förhindra att volymexpansionen slutförs. Lokalt fästa volymer etableras tjockt och processen att utöka det befintliga utrymmet innebär att spilla nivåindelade volymer till molnet. Om det inte finns några anslutningsproblem kan du ha uttömt det lokala utrymmet på enheten. Ta reda på om det finns utrymme på enheten innan du försöker utföra den här åtgärden igen. |
| Konverteringen av volym <*volymnamn*> misslyckades. |Det gick inte att konvertera volymtypen från lokalt fäst till nivåindelad. |Det gick inte att slutföra konverteringen av volymen från typen lokalt fäst till nivåindelad. Kontrollera att det inte finns några anslutningsproblem som hindrar åtgärden från att slutföras. Felsökningsproblem finns i [Felsöka med test-hcsmConnection-cmdlet](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Den ursprungliga lokalt fästa volymen har nu markerats som en nivåindelad volym eftersom en del av data från den lokalt fästa volymen har spillts till molnet under konverteringen. Den resulterande nivåindelad volymen upptar fortfarande lokalt utrymme på enheten som inte kan återvunnas för framtida lokala volymer.<br>Lös eventuella anslutningsproblem, rensa aviseringen och konvertera den här volymen tillbaka till den ursprungliga lokalt fästa volymtypen för att säkerställa att alla data görs tillgängliga lokalt igen. |
| Konverteringen av volym <*volymnamn*> misslyckades. |Det volymkonverteringsjobb som skulle konvertera volymtypen från nivåindelade till lokalt fästa misslyckades. |Det gick inte att slutföra konverteringen av volymen från typnivå till lokalt fäst. Kontrollera att det inte finns några anslutningsproblem som hindrar åtgärden från att slutföras. Felsökningsproblem finns i [Felsöka med test-hcsmConnection-cmdlet](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Den ursprungliga nivåindelad volymen som nu markeras som en lokalt fäst volym som en del av konverteringsprocessen fortsätter att ha data som finns i molnet, medan det tjockt etablerade utrymmet på enheten för den här volymen inte längre kan återknästs för framtida lokala volymer.<br>Lös eventuella anslutningsproblem, rensa aviseringen och konvertera den här volymen tillbaka till den ursprungliga nivåindelad volymtypen för att säkerställa att lokalt utrymme tjockt etablerat på enheten kan återställas. |
| Närmar sig lokal utrymmesförbrukning för lokala ögonblicksbilder av <*volymgruppsnamn*> |Lokala ögonblicksbilder för säkerhetskopieringsprincipen kan snart ta på utrymme och ogiltigförklaras för att undvika skrivfel för värden. |Frekventa lokala ögonblicksbilder tillsammans med en hög dataomsättning i de volymer som är associerade med den här principgruppen för säkerhetskopiering gör att lokalt utrymme på enheten förbrukas snabbt. Ta bort alla lokala ögonblicksbilder som inte längre behövs. Uppdatera också dina lokala ögonblicksbildscheman för den här säkerhetskopieringsprincipen för att ta mindre frekventa lokala ögonblicksbilder och se till att ögonblicksbilder av molnet tas regelbundet. Om dessa åtgärder inte vidtas kan lokalt utrymme för dessa ögonblicksbilder snart vara uttömt och systemet kommer automatiskt att ta bort dem för att säkerställa att värdskrivningar fortsätter att bearbetas. |
| Lokala ögonblicksbilder för <*volymgruppsnamn*> har ogiltigförklarats. |De lokala ögonblicksbilderna för <*volymgruppsnamn*> har ogiltigförklarats och sedan tagits bort eftersom de överskred det lokala utrymmet på enheten. |Om du vill vara säkra på att detta inte upprepas i framtiden bör du granska de lokala ögonblicksbildschemana för den här säkerhetskopieringsprincipen och ta bort alla lokala ögonblicksbilder som inte längre behövs. Frekventa lokala ögonblicksbilder tillsammans med en hög dataomsättning i de volymer som är associerade med den här principgruppen för säkerhetskopiering kan orsaka att lokalt utrymme på enheten förbrukas snabbt. |
| Det gick inte att återställa <*källsäkerhetselement-ID:>.* |Återställningsjobbet misslyckades. |Om du har fäst lokalt eller en blandning av lokalt fästa och nivåindelade volymer i den här säkerhetskopieringsprincipen uppdaterar du säkerhetskopieringslistan för att kontrollera att säkerhetskopian fortfarande är giltig. Om säkerhetskopian är giltig är det möjligt att molnanslutningsproblem förhindrar att återställningen slutförs. De lokalt fästa volymerna som återställdes som en del av den här ögonblicksbildgruppen har inte alla sina data hämtade till enheten, och om du har en blandning av nivåindelade och lokalt fästa volymer i den här ögonblicksbildgruppen är de inte synkroniserade med varandra. Om du vill slutföra återställningen tar du volymerna i den här gruppen offline på värden och försöker återställa återställningen igen. Observera att alla ändringar av volymdata som utfördes under återställningsprocessen kommer att gå förlorade. |

### <a name="networking-alerts"></a>Aviseringar om nätverk

| Varningstext | Händelse | Mer information /rekommenderade åtgärder |
|:--- |:--- |:--- |
| Det gick inte att starta StorSimple-tjänster. |Datapath-fel |Om problemet kvarstår kontaktar du Microsoft Support. |
| Duplicera IP-adress som identifierats för "Data0". | |Systemet har upptäckt en konflikt för IP-adressen "10.0.0.1". Nätverksresursen "Data0" på * \<enhetsenhet1>* är offline. Kontrollera att den här IP-adressen inte används av någon annan enhet i det här nätverket. Om du vill felsöka nätverksproblem går du till [Felsöka med Cmdlet Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Kontakta nätverksadministratören om du vill ha hjälp med att lösa problemet. Om problemet kvarstår kontaktar du Microsoft Support. |
| IPv4-adressen (eller IPv6) för Data0 är offline. | |Nätverksresursen "Data0" med IP-adressen "10.0.0.1". och prefixlängden "22" på * \<enheten1>* är offline. Kontrollera att de växelportar som gränssnittet är anslutet till fungerar. Om du vill felsöka nätverksproblem går du till [Felsöka med Cmdlet Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| Det gick inte att ansluta till autentiseringstjänsten. |Datapath-fel |DEN URL som används för att autentisera kan inte nås. Kontrollera att brandväggsreglerna innehåller de URL-mönster som angetts för StorSimple-enheten. Mer information om URL-mönster i Azure-portalen finns på https:\//aka.ms/ss-8000-network-reqs. Om du använder Azure Government Cloud går du\/till URL-mönstren i https: /aka.ms/ss8000-gov-network-reqs.|

### <a name="performance-alerts"></a>Prestandavarningar

| Varningstext | Händelse | Mer information /rekommenderade åtgärder | |
|:--- |:--- |:--- | --- |
| Enhetsbelastningen har överskridit <*tröskelvärden*>. |Långsammare svarstider än förväntat. |Enheten rapporterar användning under en tung in- och utdatabelastning. Detta kan leda till att enheten inte fungerar så bra som den borde. Granska de arbetsbelastningar som du har anslutit till enheten och ta reda på om det finns några som kan flyttas till en annan enhet eller som inte längre är nödvändiga.|
| Det gick inte att starta StorSimple-tjänster. |Datapath-fel |Om problemet kvarstår kontaktar du Microsoft Support. |

### <a name="security-alerts"></a>Säkerhetsaviseringar

| Varningstext | Händelse | Mer information /rekommenderade åtgärder |
|:--- |:--- |:--- |
| Microsoft Support-sessionen har börjat. |Supportsession från tredje part. |Bekräfta att åtkomsten är auktoriserad. När du har vidtagit lämpliga åtgärder avmarkerar du den här aviseringen från sidan aviseringar. |
| Lösenord för <*element*> upphör att gälla <*tid*>. |Lösenordets utgångsdatum närmar sig. |Ändra ditt lösenord innan det går ut. |
| Information om säkerhetskonfiguration saknas för <*element-ID*>. | |De volymer som är associerade med den här volymbehållaren kan inte användas för att replikera din StorSimple-konfiguration. För att säkerställa att dina data lagras på ett säkert sätt rekommenderar vi att du tar bort volymbehållaren och alla volymer som är associerade med volymbehållaren. När du har vidtagit lämpliga åtgärder avmarkerar du den här aviseringen från sidan aviseringar. |
| <*>* inloggningsförsök misslyckades för <*element-ID*>. |Flera misslyckade inloggningsförsök. |Enheten kan vara under attack eller så försöker en behörig användare ansluta till ett felaktigt lösenord.<ul><li>Kontakta dina behöriga användare och kontrollera att dessa försök kom från en legitim källa. Om du fortsätter att se ett stort antal misslyckade inloggningsförsök kan du överväga att inaktivera fjärrhantering och kontakta nätverksadministratören. När du har vidtagit lämpliga åtgärder avmarkerar du den här aviseringen från sidan aviseringar.</li><li>Kontrollera att ögonblicksbilder är konfigurerade med rätt lösenord. När du har vidtagit lämpliga åtgärder avmarkerar du den här aviseringen från sidan aviseringar.</li></ul>Mer information finns i [Ändra ett lösenord för en utgångna enhet](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password). |
| Ett eller flera fel uppstod när krypteringsnyckeln för tjänstdata ändrades. | |Det uppstod fel när du ändrade krypteringsnyckeln för tjänstdata. När du har åtgärdat felvillkoren `Invoke-HcsmServiceDataEncryptionKeyChange` kör du cmdleten från Windows PowerShell-gränssnittet för StorSimple på enheten för att uppdatera tjänsten. Om problemet kvarstår kontaktar du Microsoft-supporten. När du har löst problemet rensar du den här aviseringen från sidan aviseringar. |

### <a name="support-package-alerts"></a>Aviseringar för supportpaket

| Varningstext | Händelse | Mer information /rekommenderade åtgärder |
|:--- |:--- |:--- |
| Det gick inte att skapa supportpaket. |StorSimple kunde inte generera paketet. |Försök igen med den här åtgärden. Om problemet kvarstår kontaktar du Microsoft Support. När du har löst problemet avmarkerar du den här aviseringen från sidan aviseringar. |

## <a name="next-steps"></a>Nästa steg

Läs mer om [StorSimple-fel och felsökning av problem med enhetsdistribution](storsimple-8000-troubleshoot-deployment.md).

