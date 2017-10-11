---
title: "Optimera SQL Server-miljön med Azure Log Analytics | Microsoft Docs"
description: "Du kan använda SQL-bedömning lösning med Azure logganalys bedöma risker och hälsotillståndet för din SQL server-miljöer med regelbundna intervall."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: e297eb57-1718-4cfe-a241-b9e84b2c42ac
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d2aed3315fe60ace46dfb4176dc13aa417257b0c
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="optimize-your-sql-server-environment-with-the-sql-assessment-solution-in-log-analytics"></a>Optimera SQL Server-miljön med SQL-bedömning lösningen i logganalys

![SQL-bedömning symbol](./media/log-analytics-sql-assessment/sql-assessment-symbol.png)

Du kan använda SQL-bedömning-lösning för att bedöma risken och hälsotillståndet för server-miljöer med regelbundna intervall. Den här artikeln beskriver hur du installerar lösningen så att du kan vidta åtgärder för möjliga problem.

Den här lösningen ger en prioriterad lista med rekommendationer som är specifika för din distribuerade serverinfrastruktur. Rekommendationerna kategoriseras i sex fokusområden som hjälper dig att snabbt förstå risken och vidta åtgärder.

Rekommendationer baserat på kunskap och erfarenhet av Microsoft-tekniker från tusentals kunden besök. Varje rekommendation innehåller information om varför ett problem kan verkligen är viktiga och hur du implementerar de föreslagna ändringarna.

Du kan välja fokusområden som är viktigast för din organisation och spåra din framsteg mot kör en risk ledigt och hälsosam miljö.

När du har lagt till lösningen och en bedömning är slutförd, Sammanfattning visas information för fokusområden på den **SQL-bedömning** instrumentpanel för infrastrukturen i din miljö. I följande avsnitt beskrivs hur du använder informationen på den **SQL-bedömning** instrumentpanel, där du kan visa och sedan vidta rekommenderade åtgärder för din SQL server-infrastrukturen.

![Bild av SQL-bedömning sida vid sida](./media/log-analytics-sql-assessment/sql-assess-tile.png)

![Bild av SQL-bedömning instrumentpanelen](./media/log-analytics-sql-assessment/sql-assess-dash.png)

## <a name="installing-and-configuring-the-solution"></a>Installera och konfigurera lösningen
SQL-bedömning fungerar med alla versioner av SQL Server för Standard, Developer och Enterprise-versioner som stöds för närvarande.

Använd följande information för att installera och konfigurera lösningen.

* Agenterna måste installeras på servrar som har installerat SQL Server.
* SQL-bedömning lösning kräver en version av .NET Framework 4 installeras på varje dator som har en OMS-agent som stöds.
* För att kunna installera lösningen måste användaren vara administratör eller deltagare i Azure-prenumeration när du använder Azure-portalen. Användaren måste dessutom ha bidragsgivar- eller ha administratörsrollen för OMS-arbetsytan i OMS-portalen.
* När du använder Operations Manager-agenten med SQL-bedömning, måste du använda ett konto för Operations Manager Run-As. Se [Operations Manager kör som-konton för OMS](#operations-manager-run-as-accounts-for-oms) nedan för mer information.

  > [!NOTE]
  > MMA agenten har inte stöd för Operations Manager Run-As konton.
  >
  >
* Lägga till SQL-bedömning lösningen till OMS-arbetsytan med processen som beskrivs i [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md). Det krävs ingen ytterligare konfiguration.

> [!NOTE]
> När du har lagt till lösningen läggs filen AdvisorAssessment.exe till servrar med agenter. Konfigurationsdata läsa och sedan skickas till OMS-tjänsten i molnet för bearbetning. Molntjänsten innehåller data att logik tillämpas för mottagna data.

## <a name="sql-assessment-data-collection-details"></a>SQL-bedömning information för samlingen
SQL-bedömning samlar in WMI-data, registerdata, prestandadata och SQL Server hantering av dynamiska visa resultat med hjälp av de agenter som du har aktiverat.

I följande tabell visas data collection metoder för agenter, om Operations Manager (SCOM) krävs och hur ofta data samlas in av en agent.

| Plattform | Styr Agent | SCOM-agent | Azure Storage | SCOM krävs? | SCOM-agent data som skickas via management-grupp | Frekvens för samlingen |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  | &#8226; |7 dagar |

## <a name="operations-manager-run-as-accounts-for-oms"></a>Operations Manager kör som-konton för OMS
Logganalys i OMS använder Operations Manager-agent och hantering av gruppen för att samla in och skicka data till OMS-tjänsten. OMS-versioner på hanteringspaket för arbetsbelastningar att ge mervärde tjänster. Varje arbetsbelastning kräver belastningsspecifikt behörighet att köra hanteringspaket i en annan säkerhetskontext, till exempel ett domänkonto. Du måste ange autentiseringsuppgifter genom att konfigurera en Operations Manager kör som-konto.

Använd följande information för att ange Operations Manager kör som-konto för SQL.

### <a name="set-the-run-as-account-for-sql-assessment"></a>Ange kör som-konto för SQL-bedömning
 Om du redan använder SQL Server management pack, bör du använda Kör som-kontot.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Så här konfigurerar du SQL kör som-kontot i Operations-konsolen
> [!NOTE]
> Om du använder direkt OMS-agenten, i stället för SCOM-agent körs alltid management pack i säkerhetskontexten för kontot Lokalt System. Hoppa över steg 1-5 nedan och köra T-SQL eller Powershell-exempel som anger att NT AUTHORITY\SYSTEM användarnamnet.
>
>

1. Öppna driftkonsolen i Operations Manager och klicka sedan på **Administration**.
2. Under **kör som-konfiguration**, klickar du på **profiler**, och öppna **OMS SQL Assessment kör som-profilen**.
3. På den **kör som-konton** klickar du på **Lägg till**.
4. Välj ett Windows kör som-konto som innehåller de autentiseringsuppgifter som krävs för SQL Server, eller klicka på **ny** att skapa en.

   > [!NOTE]
   > Kör som-kontotyp måste vara Windows. Kör som-kontot måste också ingå i gruppen lokala administratörer på alla Windows-servrar som är värd för SQL Server-instanser.
   >
   >
5. Klicka på **Spara**.
6. Ändra och kör följande T-SQL-exempel på varje SQL Server-instans för att bevilja lägsta behörighet som krävs för Kör som-konto att utföra SQL-bedömning. Dock behöver du inte göra det om ett kör som-konto redan är en del av serverrollen sysadmin på SQL Server-instanser.

```
---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```
#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Så här konfigurerar du SQL kör som-kontot med Windows PowerShell
Öppna ett PowerShell-fönster och kör följande skript när du har uppdaterat den med din information:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Förstå hur rekommendationer prioriteras
Varje rekommendation är ett givet värde som identifierar en avvägning mellan kraven för rekommendationen. Bara de tio viktigaste rekommendationerna visas.

### <a name="how-weights-are-calculated"></a>Hur vikterna beräknas
Viktningar är sammanställda värden som baseras på tre viktiga faktorer:

* Den *sannolikhet* att ett problem som identifieras ska orsaka problem. En högre sannolikhet är lika med en större övergripande poäng för rekommendationen.
* Den *inverkan* av problemet i din organisation om det uppstår ett problem. En högre inverkan är lika med en större övergripande poäng för rekommendationen.
* Den *arbete* krävs för att implementera denna rekommendation. Det är lika med en mindre övergripande poäng för rekommendationen högre prestanda.

Värde för varje rekommendation uttrycks i procent av den sammanlagda poängen för varje fokusområde. Till exempel om en rekommendation i området för säkerhet och efterlevnad fokus har ett resultat på 5%, ökar implementera denna rekommendation din övergripande säkerhet och efterlevnad poäng av 5%.

### <a name="focus-areas"></a>Fokusområden
**Säkerhet och efterlevnad** -fokus visas här rekommendationer för potentiella säkerhetshot och överträdelser, företagets principer och krav på teknisk, juridisk och regelmässig efterlevnad.

**Tillgänglighet och affärskontinuitet** -fokus visas här rekommendationer för tjänstetillgänglighet, återhämtning för din infrastruktur och business-skydd.

**Prestanda och skalbarhet** -fokus visas här rekommendationer som hjälper din organisations IT-infrastruktur växer, se till att din IT-miljö uppfyller aktuella prestandakrav och kan svara på förändrade infrastrukturbehov.

**Uppgradera migrering och distribution** -fokus visas här rekommendationer när du uppgraderar, migrera och distribuera SQL Server till den befintliga infrastrukturen.

**Åtgärder och övervakning** -fokus visas här rekommendationer för att effektivisera IT-avdelningen, implementera förebyggande underhåll och maximera prestanda.

**Ändrings- och konfigurationshantering** -fokus visas här rekommendationer för att skydda dagliga uppgifter, se till att ändringar inte negativt påverka din infrastruktur kan upprätta ändringskontroll, och för att spåra och granska systemkonfigurationer.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Bör du försöka poängsätta 100% överallt fokus i?
Inte nödvändigtvis. Rekommendationerna baseras på kunskaper och erfarenheter som gjorts av Microsoft-tekniker över tusentals kunden besök. Dock inga två server-infrastrukturen är samma och specifika rekommendationer kan vara mer eller mindre relevant för dig. Exempelvis kanske vissa säkerhetsrekommendationer mindre relevant om de virtuella datorerna inte utsätts för Internet. Vissa tillgänglighet rekommendationer kan vara relevanta för tjänster som erbjuder med låg prioritet ad hoc-insamling och rapportering. Problem som är viktiga för en mogen företag kanske mindre viktiga för en Startup. Du kanske vill identifiera vilka fokusområden är dina prioriteter och titta på hur ditt resultat förändras över tid.

Varje rekommendation innehåller information om varför det är viktigt. Du bör använda den här vägledningen för att utvärdera om implementera rekommendationen är lämplig för dig, baserat på typ av din IT-tjänster och affärsbehoven för din organisation.

## <a name="use-assessment-focus-area-recommendations"></a>Använd assessment fokus området rekommendationer
Innan du kan använda en lösning i OMS måste ha installerat lösningen. Du kan läsa mer om hur du installerar lösningar finns [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md). När den har installerats kan du visa sammanfattning av rekommendationer med hjälp av SQL-bedömning panelen på översiktssidan i OMS.

Visa sammanfattade efterlevnad bedömningar för din infrastruktur och gå till rekommendationer.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Visa rekommendationer för en Fokusområde och vidta åtgärder
1. På den **översikt** klickar du på den **SQL-bedömning** panelen.
2. På den **SQL-bedömning** , Granska sammanfattningen i ett fokus området blad och klickar sedan på en om du vill visa rekommendationer för området fokus.
3. På någon av sidorna fokus område, kan du visa prioriterad rekommendationer för din miljö. Klicka på en rekommendation enligt **påverkade objekt** att visa information om varför rekommendationen görs.  
    ![Bild av SQL-bedömning rekommendationer](./media/log-analytics-sql-assessment/sql-assess-focus.png)
4. Du kan vidta åtgärder i **föreslagna åtgärder**. När objektet har behandlats registrerar senare bedömningar den rekommenderade åtgärder som utförts och kompatibilitet poängen ökar. Korrigerade objekt visas som **skickades objekt**.

## <a name="ignore-recommendations"></a>Ignorera rekommendationer
Om du har rekommendationer som du vill ignorera, kan du skapa en textfil som OMS använder för att förhindra rekommendationer visas i sökresultatet assessment.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Att identifiera rekommendationer som kommer att ignorera
1. Logga in på din arbetsyta och öppna loggen sökning. Använd följande fråga för att lista över rekommendationer som har misslyckats för datorer i din miljö.

   ```
   Type=SQLAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
   ```

   Här är en skärmbild som visar loggen sökfråga: ![misslyckades rekommendationer](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)
2. Välj rekommendationer som du vill ignorera. Du ska använda värdena för RecommendationId i nästa procedur.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Du skapar och använder en IgnoreRecommendations.txt textfil
1. Skapa en fil med namnet IgnoreRecommendations.txt.
2. Klistra in eller ange varje RecommendationId för varje rekommendation som du vill OMS Ignorera på en separat rad och spara och stäng filen.
3. Placera filen i följande mapp på varje dator där du vill att OMS att ignorera rekommendationer.
   * På datorer med Microsoft Monitoring Agent (ansluten direkt eller via Operations Manager) - *SystemDrive*: \Program\Microsoft övervakning Agent\Agent
   * På hanteringsservern för Operations Manager - *SystemDrive*: \Program\Microsoft System Center 2012 R2\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Kontrollera att rekommendationer ignoreras
1. När nästa schemalagda utvärdering körs som standard var sjunde dag, angivna rekommendationerna markeras ignoreras och kommer inte att visas på instrumentpanelen assessment.
2. Du kan använda följande loggen sökfrågor för att lista alla rekommendationer som ignoreras.

   ```
   Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
   ```
3. Om du senare bestämmer att du vill se ignoreras rekommendationer, ta bort IgnoreRecommendations.txt filer eller du kan ta bort RecommendationIDs från dem.

## <a name="sql-assessment-solution-faq"></a>SQL lösning vanliga frågor och svar
*Hur ofta körs en utvärdering?*

* Utvärderingen körs var sjunde dag.

*Finns det ett sätt att konfigurera hur ofta bedömning körs?*

* Inte just nu.

*Om en annan server identifieras när jag har lagt till SQL-bedömning lösning, kommer utvärderas det?*

* Ja, när det upptäcks att det bedöms från sedan, var sjunde dag.

*Om en server tas ur drift, när den tas bort från bedömningen?*

* Om en server inte lämnar data 3 veckor tas bort.

*Vad är namnet på processen som gör datainsamlingen?*

* AdvisorAssessment.exe

*Hur lång tid tar det för data som samlas in?*

* Samlingen faktiska data på servern tar ungefär en timme. Det kan ta längre tid på servrar som har ett stort antal databaser eller SQL-instanser.

*Vilken typ av data som samlas in?*

* Följande typer av data samlas in:
  * WMI
  * Registret
  * Prestandaräknare
  * SQL dynamiska hanteringsvyer (DMV).

*Finns det ett sätt att konfigurera när data samlas in?*

* Inte just nu.

*Varför måste jag konfigurera ett kör som-konto?*

* För SQL Server körs ett litet antal SQL-frågor. För att kunna köra måste en Kör som-konto med VIEW SERVER STATE behörighet till SQL användas.  Dessutom för att fråga WMI krävs lokal administratörsbehörighet.

*Varför visas endast topp 10 rekommendationer?*

* I stället för att ge dig en överväldigande uttömmande förteckning över aktiviteter, rekommenderar vi att du fokusera på adressering prioriterad rekommendationerna först. När du åtgärda dem blir ytterligare rekommendationer tillgängliga. Du kan visa alla rekommendationer OMS loggen sökning om du vill se en detaljerad lista.

*Finns det ett sätt att ignorera en rekommendation?*

* Ja, se [Ignorera rekommendationer](#ignore-recommendations) ovan.

## <a name="next-steps"></a>Nästa steg
* [Söka i loggar](log-analytics-log-searches.md) att visa detaljerad information i SQL-bedömning och rekommendationer.
