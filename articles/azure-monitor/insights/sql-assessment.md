---
title: Optimera din SQL Server-miljö med Azure Monitor | Microsoft-dokument
description: Med Azure Monitor kan du använda SQL Health Check-lösningen för att bedöma risken och hälsan för dina miljöer med jämna mellanrum.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/28/2019
ms.openlocfilehash: ceaed0800df01bf2c44fee13d98b01b6e726200d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662492"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-azure-monitor"></a>Optimera din SQL-miljö med sql server-hälsokontrolllösningen i Azure Monitor

![Symbol för SQL Health Check](./media/sql-assessment/sql-assessment-symbol.png)

Du kan använda SQL Health Check-lösningen för att bedöma risken och hälsan för dina servermiljöer med jämna mellanrum. Den här artikeln hjälper dig att installera lösningen så att du kan vidta korrigerande åtgärder för potentiella problem.

Den här lösningen innehåller en prioriterad lista med rekommendationer som är specifika för din distribuerade serverinfrastruktur. Rekommendationerna kategoriseras över sex fokusområden som hjälper dig att snabbt förstå risken och vidta korrigerande åtgärder.

Rekommendationerna är baserade på den kunskap och erfarenhet som Microsofts tekniker fått från tusentals kundbesök. Varje rekommendation ger vägledning om varför ett problem kan vara viktigt för dig och hur du implementerar de föreslagna ändringarna.

Du kan välja fokusområden som är viktigast för din organisation och följa dina framsteg mot att driva en riskfri och hälsosam miljö.

När du har lagt till lösningen och en utvärdering har slutförts visas sammanfattande information för fokusområden på instrumentpanelen för **SQL Health Check** för infrastrukturen i din miljö. I följande avsnitt beskrivs hur du använder informationen på instrumentpanelen **för SQL Health Check,** där du kan visa och sedan vidta rekommenderade åtgärder för SQL Server-infrastrukturen.

![bild av panelen SQL Health Check](./media/sql-assessment/sql-healthcheck-summary-tile.png)

![bild av instrumentpanelen för SQL Health Check](./media/sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Krav

* SQL Health Check-lösningen kräver en version av .NET Framework 4.6.2 som stöds på varje dator som har MMA (Microsoft Monitoring Agent) installerat.  MMA-agenten används av System Center 2016 - Operations Manager och Operations Manager 2012 R2 och Azure Monitor.  
* Lösningen stöder SQL Server version 2012, 2014 och 2016.
* En Log Analytics-arbetsyta för att lägga till SQL Health Check-lösningen från Azure-marknadsplatsen i Azure-portalen.  För att kunna installera lösningen måste du vara administratör eller deltagare i Azure-prenumerationen.

  > [!NOTE]
  > När du har lagt till lösningen läggs filen AdvisorAssessment.exe till på servrar med agenter. Konfigurationsdata läss och skickas sedan till Azure Monitor i molnet för bearbetning. Logik tillämpas på mottagna data och molntjänsten registrerar data.
  >
  >

För att utföra hälsokontrollen mot dina SQL Server-servrar kräver de en agent och anslutning till Azure Monitor med någon av följande metoder som stöds:

1. Installera [MICROSOFT Monitoring Agent (MMA)](../../azure-monitor/platform/agent-windows.md) om servern inte redan övervakas av System Center 2016 - Operations Manager eller Operations Manager 2012 R2.
2. Om den övervakas med System Center 2016 - Operations Manager eller Operations Manager 2012 R2 och hanteringsgruppen inte är integrerad med Azure Monitor, kan servern vara flera hem med Log Analytics för att samla in data och vidarebefordra till tjänsten och ändå vara övervakas av Operations Manager.  
3. Annars, om din Operations Manager-hanteringsgrupp är integrerad med tjänsten, måste du lägga till domänkontrollanterna för datainsamling av tjänsten enligt stegen under [Lägg till agenthanterade datorer](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor) när du har aktiverat lösningen på arbetsytan.  

Agenten på sql server som rapporterar till en Hanteringsgrupp för Operations Manager, samlar in data, vidarebefordrar till den tilldelade hanteringsservern och sedan skickas direkt från en hanteringsserver till Azure Monitor.  Data skrivs inte till Operations Manager-databaserna.  

Om SQL Server övervakas av Operations Manager måste du konfigurera ett Operations Manager Run As-konto. Mer information finns [i Operations Manager-körningskonton för Azure Monitor](#operations-manager-run-as-accounts-for-log-analytics) nedan.

## <a name="sql-health-check-data-collection-details"></a>Information om datainsamling i SQL-hälsokontroll
SQL Health Check samlar in data från följande källor med hjälp av agenten som du har aktiverat:

* Windows Management Instrumentation (WMI)
* Register
* Prestandaräknare
* Sql Server dynamiska hanteringsvyresultat

Data samlas in på SQL Server och vidarebefordras till Log Analytics var sjunde dag.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Kör som-konton i Operations Manager för Log Analytics
Log Analytics använder Operations Manager-agenten och hanteringsgruppen för att samla in och skicka data till Log Analytics-tjänsten. Log Analytics bygger på hanteringspaket för arbetsbelastningar för att tillhandahålla mervärdestjänster. Varje arbetsbelastning kräver arbetsbelastningsspecifika privilegier för att köra hanteringspaket i en annan säkerhetskontext, till exempel ett domänanvändarkonto. Du måste ange autentiseringsuppgifter information genom att konfigurera en Operations Manager Kör som konto.

Använd följande information för att ange Operations Manager Run As-kontot för SQL Health Check.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Ange Kör som-konto för SQL-hälsokontroll
 Om du redan använder SQL Server-hanteringspaketet bör du använda konfigurationen Kör som.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Så här konfigurerar du SQL Run As-kontot i driftkonsolen
> [!NOTE]
> Som standard körs arbetsflöden i hanteringspaketet i säkerhetskontexten för kontot Lokalt system. Om du använder Microsoft Monitoring Agent som är ansluten direkt till tjänsten i stället för att rapportera direkt till en Operations Manager-hanteringsgrupp hoppar du över steg 1-5 nedan och kör antingen T-SQL- eller PowerShell-exemplet och anger NT AUTHORITY\SYSTEM som användarnamn.
>
>

1. Öppna driftkonsolen i Operations Manager och klicka sedan på **Administration**.
2. Klicka på **Profiler**under **Kör som konfiguration**och öppna SQL Assessment Run As **Profile**.
3. På sidan **Kör som-konton** klickar du på **Lägg till**.
4. Välj ett Windows Run As-konto som innehåller de autentiseringsuppgifter som behövs för SQL Server, eller klicka på **Ny** för att skapa ett.

   > [!NOTE]
   > Kontotypen Kör som måste vara Windows. Kontot Kör som måste också ingå i gruppen Lokala administratörer på alla Windows-servrar som är värdar för SQL Server-instanser.
   >
   >
5. Klicka på **Spara**.
6. Ändra och kör sedan följande T-SQL-exempel på varje SQL Server-instans för att bevilja minsta behörigheter som krävs för att köra som-konto för att utföra hälsokontrollen. Du behöver dock inte göra detta om ett Run As-konto redan är en del av sysadmin-serverrollen på SQL Server-instanser.

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

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Så här konfigurerar du SQL Run As-kontot med Windows PowerShell
Öppna ett PowerShell-fönster och kör följande skript när du har uppdaterat det med din information:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Förstå hur rekommendationer prioriteras
Varje rekommendation ges ett viktningsvärde som identifierar rekommendationens relativa betydelse. Endast de tio viktigaste rekommendationerna visas.

### <a name="how-weights-are-calculated"></a>Hur vikter beräknas
Viktningar är aggregerade värden baserade på tre nyckelfaktorer:

* *Sannolikheten för* att ett problem identifieras kommer att orsaka problem. En högre sannolikhet motsvarar en större totalpoäng för rekommendationen.
* *Problemets inverkan* på din organisation om det orsakar ett problem. En högre effekt motsvarar en större totalpoäng för rekommendationen.
* De *ansträngningar* som krävs för att genomföra rekommendationen. En högre insats motsvarar en mindre totalpoäng för rekommendationen.

Viktningen för varje rekommendation uttrycks som en procentandel av den totala poängen som är tillgänglig för varje fokusområde. Om till exempel en rekommendation i fokusområdet Säkerhet och efterlevnad har poängen 5 %, ökar implementeringen av rekommendationen din totala säkerhets- och efterlevnadspoäng med 5 %.

### <a name="focus-areas"></a>Fokusområden
**Säkerhet och efterlevnad** – Det här fokusområdet visar rekommendationer för potentiella säkerhetshot och överträdelser, företagspolicyer och tekniska, rättsliga och regulatoriska efterlevnadskrav.

**Tillgänglighet och affärskontinuitet** – Det här fokusområdet visar rekommendationer för tjänstens tillgänglighet, infrastrukturens återhämtning och ditt affärsskydd.

**Prestanda och skalbarhet** – Det här fokusområdet visar rekommendationer som hjälper organisationens IT-infrastruktur att växa, se till att it-miljön uppfyller aktuella prestandakrav och kan svara på förändrade infrastrukturbehov.

**Uppgradera, migrera och distribuera** – Det här fokusområdet visar rekommendationer som hjälper dig att uppgradera, migrera och distribuera SQL Server till din befintliga infrastruktur.

**Drift och övervakning** - Det här fokusområdet visar rekommendationer som hjälper dig att effektivisera din IT-drift, implementera förebyggande underhåll och maximera prestanda.

**Ändrings- och konfigurationshantering** – Det här fokusområdet visar rekommendationer som skyddar den dagliga verksamheten, säkerställer att ändringar inte påverkar infrastrukturen negativt, upprättar ändringskontrollprocedurer och spårar och granskar systemkonfigurationer.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Bör du sikta på 100 % inom varje fokusområde?
Inte nödvändigtvis. Rekommendationerna baseras på den kunskap och de erfarenheter som Microsofts tekniker har fått vid tusentals kundbesök. Inga två serverinfrastrukturer är dock desamma och specifika rekommendationer kan vara mer eller mindre relevanta för dig. Vissa säkerhetsrekommendationer kan till exempel vara mindre relevanta om dina virtuella datorer inte exponeras för Internet. Vissa tillgänglighetsrekommendationer kan vara mindre relevanta för tjänster som tillhandahåller ad hoc-datainsamling och rapportering med låg prioritet. Frågor som är viktiga för ett moget företag kan vara mindre viktiga för ett nystartigt företag. Du kanske vill identifiera vilka fokusområden som är dina prioriteringar och sedan titta på hur dina poäng förändras med tiden.

Varje rekommendation innehåller vägledning om varför det är viktigt. Du bör använda den här vägledningen för att utvärdera om det är lämpligt för dig att implementera rekommendationen, med tanke på vilken typ av IT-tjänster du har och din organisations affärsbehov.

## <a name="use-health-check-focus-area-recommendations"></a>Använda rekommendationer om fokusområden i hälsokontroll
Innan du kan använda en utvärderingslösning i Azure Monitor måste du ha lösningen installerad.  När den har installerats kan du visa sammanfattningen av rekommendationer med hjälp av panelen SQL Health Check på **översiktssidan** för Azure Monitor i Azure-portalen.

Visa de sammanfattade efterlevnadsbedömningarna för infrastrukturen och sedan detaljgranska rekommendationer.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Så här visar du rekommendationer för ett fokusområde och vidtar korrigerande åtgärder
1. Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).
2. I Azure Portal klickar du på knappen **Fler tjänster** längst upp till vänster. I listan över resurser skriver du **Monitor**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Monitor**.
3. Välj **Mer**i avsnittet **Insikter** på menyn .  
4. Klicka på panelen **SQL Health Check** på sidan **Översikt.**
5. På sidan **Hälsokontroll** granskar du sammanfattningsinformationen i ett av fokusområdesbladen och klickar sedan på en för att visa rekommendationer för det fokusområdet.
6. På någon av fokusområdenas sidor kan du visa de prioriterade rekommendationerna för din miljö. Klicka på en rekommendation under **Berörda objekt** om du vill visa information om varför rekommendationen görs.<br><br> ![bild av SQL Health Check-rekommendationer](./media/sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. Du kan vidta korrigerande åtgärder som **föreslås**i Föreslagna åtgärder . När objektet har adresserats registrerar senare utvärderingar att rekommenderade åtgärder har vidtagits och att efterlevnadspoängen ökar. Korrigerade objekt visas som **Skickade objekt**.

## <a name="ignore-recommendations"></a>Ignorera rekommendationer
Om du har rekommendationer som du vill ignorera kan du skapa en textfil som Azure Monitor använder för att förhindra att rekommendationer visas i dina utvärderingsresultat.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Så här identifierar du rekommendationer som du ignorerar
1. Klicka på **Loggar**på Azure Monitor-menyn .
2. Använd följande fråga för att lista rekommendationer som har misslyckats för datorer i din miljö.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Här är en skärmdump som visar loggfrågan:<br><br> ![misslyckade rekommendationer](./media/sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Välj rekommendationer som du vill ignorera. Du ska använda värdena för RecommendationId i nästa procedur.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Så här skapar och använder du en IgnoreRecommendations.txt-textfil
1. Skapa en fil med namnet IgnoreRecommendations.txt.
2. Klistra in eller skriv varje RecommendationId för varje rekommendation som du vill att Azure Monitor ska ignorera på en separat rad och sedan spara och stänga filen.
3. Placera filen i följande mapp på varje dator där du vill att Azure Monitor ska ignorera rekommendationer.
   * På datorer med Microsoft Monitoring Agent (ansluten direkt eller via Operations Manager) - *SystemDrive*:\Program\Microsoft Monitoring Agent\Agent
   * På hanteringsservern för Operations Manager - *SystemDrive*:\Program\Microsoft System Center 2012 R2\Operations Manager\Server
   * På hanteringsservern för Operations Manager 2016 – *SystemDrive*:\Program\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Så här kontrollerar du att rekommendationerna ignoreras
1. När nästa schemalagda utvärdering körs markeras som standard de angivna rekommendationerna Ignoreras och visas inte på instrumentpanelen för utvärdering.
2. Du kan använda följande loggsökfrågor för att lista alla ignorerade rekommendationer.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
3. Om du senare bestämmer dig för att du vill se ignorerade rekommendationer tar du bort alla IgnoreRecommendations.txt-filer eller så kan du ta bort Rekommendations-ID:er från dem.

## <a name="sql-health-check-solution-faq"></a>Vanliga frågor och svar om SQL-hälsokontrollösning

*Vilka kontroller utförs av SQL Assessment-lösningen?*

* Följande fråga visar en beskrivning av alla kontroller som för närvarande utförs:

```Kusto
SQLAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
Resultaten kan sedan exporteras till Excel för vidare granskning.


*Hur ofta körs en hälsokontroll?*

* Checken går var sjunde dag.

*Finns det något sätt att konfigurera hur ofta kontrollen körs?*

* Inte just nu.

*Om en annan server upptäcks efter att jag har lagt till SQL Health Check-lösningen, kommer den att kontrolleras?*

* Ja, när det upptäcks det kontrolleras från och med då, var sjunde dag.

*Om en server inaktiveras, när tas den bort från hälsokontrollen?*

* Om en server inte skickar data på tre veckor tas den bort.

*Vad är namnet på den process som gör datainsamlingen?*

* RådgivareAssessment.exe

*Hur lång tid tar det innan data samlas in?*

* Den faktiska datainsamlingen på servern tar cirka 1 timme. Det kan ta längre tid på servrar som har ett stort antal SQL-instanser eller databaser.

*Vilken typ av data samlas in?*

* Följande typer av data samlas in:
  * WMI
  * Register
  * Prestandaräknare
  * SQL dynamiska hanteringsvyer (DMV).

*Finns det något sätt att konfigurera när data samlas in?*

* Inte just nu.

*Varför måste jag konfigurera ett Run As-konto?*

* För SQL Server körs ett litet antal SQL-frågor. För att de ska kunna köras måste behörigheterna Kör som konto med VIEW SERVER STATE till SQL användas.  Dessutom krävs lokala administratörsautentiseringsuppgifter för att fråga WMI.

*Varför visa bara de 10 rekommendationer?*

* I stället för att ge dig en uttömmande överväldigande lista över uppgifter rekommenderar vi att du fokuserar på att ta itu med de prioriterade rekommendationerna först. När du har åtgärdat dem blir ytterligare rekommendationer tillgängliga. Om du föredrar att se den detaljerade listan kan du visa alla rekommendationer med log analytics-loggsökningen.

*Finns det något sätt att ignorera en rekommendation?*

* Ja, se [Ignorera rekommendationer](#ignore-recommendations) ovan.

## <a name="next-steps"></a>Nästa steg
* Logga frågor för att lära dig hur du [analyserar](../log-query/log-query-overview.md) detaljerade SQL Health Check data och rekommendationer.
