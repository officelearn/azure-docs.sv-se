---
title: "Optimera SQL Server-miljön med Azure Log Analytics | Microsoft Docs"
description: "Du kan använda SQL Health Check-lösning med Azure logganalys bedöma risker och hälsotillståndet för dina miljöer regelbundna intervall."
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
ms.date: 10/27/2017
ms.author: magoedte;banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ec66c322550ac3a7729dc1fddc8c026fb4ec1895
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2017
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-log-analytics"></a>Optimera SQL-miljön med SQL Server Health Check-lösningen i logganalys

![SQL Health Check symbol](./media/log-analytics-sql-assessment/sql-assessment-symbol.png)

Du kan använda SQL Health Check-lösning för att bedöma risken och hälsotillståndet för server-miljöer med regelbundna intervall. Den här artikeln beskriver hur du installerar lösningen så att du kan vidta åtgärder för möjliga problem.

Den här lösningen ger en prioriterad lista med rekommendationer som är specifika för din distribuerade serverinfrastruktur. Rekommendationerna kategoriseras i sex fokusområden som hjälper dig att snabbt förstå risken och vidta åtgärder.

Rekommendationer baserat på kunskap och erfarenhet av Microsoft-tekniker från tusentals kunden besök. Varje rekommendation innehåller information om varför ett problem kan verkligen är viktiga och hur du implementerar de föreslagna ändringarna.

Du kan välja fokusområden som är viktigast för din organisation och spåra din framsteg mot kör en risk ledigt och hälsosam miljö.

När du har lagt till lösningen och en bedömning är slutförd, Sammanfattning visas information för fokusområden på den **SQL Health Check** instrumentpanel för infrastrukturen i din miljö. I följande avsnitt beskrivs hur du använder informationen på den **SQL Health Check** instrumentpanel, där du kan visa och sedan vidta rekommenderade åtgärder för SQL Server-infrastrukturen.

![Bild av SQL Health Check sida vid sida](./media/log-analytics-sql-assessment/sql-healthcheck-summary-tile.png)

![Bild av SQL Health Check instrumentpanelen](./media/log-analytics-sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Krav

* SQL Health Check-lösning kräver en version som stöds av .NET Framework 4 installeras på varje dator som har den Microsoft Monitoring Agent (MMA) installerad.  MMA agent används av System Center 2016 - Operations Manager och Operations Manager 2012 R2 och Log Analytics-tjänsten.  
* Lösningen har stöd för SQL Server version 2012, 2014 och 2016.
* En logganalys-arbetsytan att lägga till SQL Health Check-lösning från Azure marketplace i Azure-portalen.  Du måste vara administratör eller deltagare i Azure-prenumeration för att kunna installera lösningen. 

  > [!NOTE]
  > När du har lagt till lösningen läggs filen AdvisorAssessment.exe till servrar med agenter. Konfigurationsdata läsa och sedan skickas till Log Analytics-tjänsten i molnet för bearbetning. Molntjänsten innehåller data att logik tillämpas för mottagna data.
  >
  >

Om du vill utföra hälsokontroll mot SQL Server-servrar, kräver de en agent och anslutningen till logganalys med någon av följande metoder:

1. Installera den [Microsoft Monitoring Agent (MMA)](log-analytics-windows-agents.md) om servern inte är redan övervakas av System Center 2016 - Operations Manager eller Operations Manager 2012 R2.
2. Om den är övervakad med System Center 2016 - Operations Manager eller Operations Manager 2012 R2 och hanteringsgruppen inte är integrerad med Log Analytics-tjänsten, är att servern multihomed med logganalys samla in data och vidarebefordra till tjänsten och fortfarande övervakas av Operations Manager.  
3. Annars om hanteringsgruppen för Operations Manager är integrerat med tjänsten, du måste lägga till domänkontrollanterna för insamling av tjänsten följande under [lägga till datorer som hanteras med agent](log-analytics-om-agents.md#connecting-operations-manager-to-oms) när du har aktiverat lösning på arbetsytan.  

Agenten på SQL Server att en Operations Manager-hanteringsgrupp samlar in data, vidarebefordrar till dess tilldelade hanteringsserver och skickas sedan direkt från en hanteringsserver till Log Analytics-tjänsten.  Data skrivs inte till Operations Manager-databaserna.  

Om SQL Server övervakas av Operations Manager, måste du konfigurera en Operations Manager kör som-konto. Se [Operations Manager kör som-konton för Log Analytics](#operations-manager-run-as-accounts-for-log-analytics) nedan för mer information.

## <a name="sql-health-check-data-collection-details"></a>SQL Health Check samling information
SQL Health Check samlar in data från följande källor med agenten som du har aktiverat: 

* Windows Management Instrumentation (WMI) 
* Registret 
* Prestandaräknare
* SQL Server hantering av dynamiska visa resultat 

Data samlas in på SQL Server och vidarebefordras till logganalys var sjunde dag.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Operations Manager kör som-konton för logganalys
Log Analytics använder Operations Manager-agent och hantering av gruppen för att samla in och skicka data till logganalys-tjänsten. Log Analytics-versioner på hanteringspaket för arbetsbelastningar att ge mervärde tjänster. Varje arbetsbelastning kräver belastningsspecifikt behörighet att köra hanteringspaket i en annan säkerhetskontext, till exempel ett domänanvändarkonto. Du måste ange autentiseringsuppgifter genom att konfigurera en Operations Manager kör som-konto.

Använd följande information för att ange Operations Manager kör som-kontot för SQL Health Check.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Ange kör som-konto för SQL Health Check
 Om du redan använder SQL Server management pack, använder du den kör som-konfigurationen.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Så här konfigurerar du SQL kör som-kontot i Operations-konsolen
> [!NOTE]
> Som standard arbetsflöden i management pack som körs i säkerhetskontexten för kontot Lokalt System. Om du använder Microsoft Monitoring Agent anslutna direkt till tjänsten snarare än reporting direkt hoppa över steg 1 – 5 nedan i en Operations Manager-hanteringsgrupp och köra T-SQL eller PowerShell-exempel, ange NT AUTHORITY\SYSTEM som den användarnamn.
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
6. Ändra och kör följande T-SQL-exempel på varje SQL Server-instans för att bevilja lägsta behörigheter som krävs för Kör som-kontot att kontrollera hälsotillståndet. Dock behöver du inte göra det om ett kör som-konto redan är en del av serverrollen sysadmin på SQL Server-instanser.

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

## <a name="use-health-check-focus-area-recommendations"></a>Använd Health Check fokus området rekommendationer
Innan du kan använda en lösning i logganalys, måste du ha lösningen installerad.  När den har installerats kan du visa sammanfattning av rekommendationer med hjälp av SQL Health Check-panelen på sidan lösning i Azure-portalen.

Visa sammanfattade efterlevnad bedömningar för din infrastruktur och gå till rekommendationer.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Visa rekommendationer för en Fokusområde och vidta åtgärder
1. Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com). 
2. I Azure-portalen klickar du på **fler tjänster** hittades i det nedre vänstra hörnet. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **logga Analytics**.
3. Välj en arbetsyta i fönstret logganalys prenumerationer och klicka sedan på den **OMS-portalen** panelen.  
4. På den **översikt** klickar du på den **SQL Health Check** panelen. 
5. På den **Health Check** , Granska sammanfattningen i ett fokus området blad och klickar sedan på en om du vill visa rekommendationer för området fokus.
6. På någon av sidorna fokus område, kan du visa prioriterad rekommendationer för din miljö. Klicka på en rekommendation enligt **påverkade objekt** att visa information om varför rekommendationen görs.<br><br> ![Bild av SQL Health Check rekommendationer](./media/log-analytics-sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. Du kan vidta åtgärder i **föreslagna åtgärder**. När objektet har behandlats registrerar senare bedömningar den rekommenderade åtgärder som utförts och kompatibilitet poängen ökar. Korrigerade objekt visas som **skickades objekt**.

## <a name="ignore-recommendations"></a>Ignorera rekommendationer
Om du har rekommendationer som du vill ignorera, kan du skapa en textfil som OMS använder för att förhindra rekommendationer visas i sökresultatet assessment.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Att identifiera rekommendationer som kommer att ignorera
1. I Azure-portalen på sidan för logganalys-arbetsytan för den valda arbetsytan klickar du på den **loggen Sök** panelen.
2. Använd följande fråga för att lista över rekommendationer som har misslyckats för datorer i din miljö.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Om ditt arbetsområde har uppgraderats till den [nya Log Analytics-frågespråket](log-analytics-log-search-upgrade.md), sedan frågan ovan skulle ändra till följande.
    >
    > `SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Här är en skärmbild som visar frågan loggen:<br><br> ![misslyckade rekommendationer](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Välj rekommendationer som du vill ignorera. Du ska använda värdena för RecommendationId i nästa procedur.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Du skapar och använder en IgnoreRecommendations.txt textfil
1. Skapa en fil med namnet IgnoreRecommendations.txt.
2. Klistra in eller ange varje RecommendationId för varje rekommendation som du vill logganalys Ignorera på en separat rad och spara och stäng filen.
3. Placera filen i följande mapp på varje dator där du vill att logganalys att ignorera rekommendationer.
   * På datorer med Microsoft Monitoring Agent (ansluten direkt eller via Operations Manager) - *SystemDrive*: \Program\Microsoft övervakning Agent\Agent
   * På hanteringsservern för Operations Manager - *SystemDrive*: \Program\Microsoft System Center 2012 R2\Operations Manager\Server
   * På hanteringsservern för Operations Manager 2016 - *SystemDrive*: \Program\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Kontrollera att rekommendationer ignoreras
1. När nästa schemalagda utvärdering körs som standard var sjunde dag, angivna rekommendationerna markeras ignoreras och kommer inte att visas på instrumentpanelen assessment.
2. Du kan använda följande loggen sökfrågor för att lista alla rekommendationer som ignoreras.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Om ditt arbetsområde har uppgraderats till den [nya Log Analytics-frågespråket](log-analytics-log-search-upgrade.md), sedan frågan ovan skulle ändra till följande.
    >
    > `SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Om du senare bestämmer att du vill se ignoreras rekommendationer, ta bort IgnoreRecommendations.txt filer eller du kan ta bort RecommendationIDs från dem.

## <a name="sql-health-check-solution-faq"></a>SQL Health Check lösning vanliga frågor och svar
*Hur ofta körs hälsokontrollen?*

* Kontrollen körs var sjunde dag.

*Finns det ett sätt att konfigurera hur ofta kontrollen körs?*

* Inte just nu.

*Om en annan server identifieras när jag har lagt till SQL Health Check-lösning, den kontrolleras?*

* Ja, när det upptäcks checkas från sedan, var sjunde dag.

*Om en server tas ur drift, när den tas bort från hälsotillståndskontroll?*

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

* I stället för att ge dig en överväldigande uttömmande förteckning över aktiviteter, rekommenderar vi att du fokusera på adressering prioriterad rekommendationerna först. När du åtgärda dem blir ytterligare rekommendationer tillgängliga. Du kan visa alla rekommendationer logganalys loggen sökning om du vill se en detaljerad lista.

*Finns det ett sätt att ignorera en rekommendation?*

* Ja, se [Ignorera rekommendationer](#ignore-recommendations) ovan.

## <a name="next-steps"></a>Nästa steg
* [Söka i loggar](log-analytics-log-searches.md) att lära dig att analysera detaljerad SQL Health Check information och rekommendationer.
