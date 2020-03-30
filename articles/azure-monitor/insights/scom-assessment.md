---
title: Utvärdera System Center Operations Manager med Azure Monitor
description: Du kan använda hälsokontrolllösningen System Center Operations Manager för att regelbundet bedöma riskerna och hälsan för dina miljöer.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2018
ms.openlocfilehash: 94251dfa2d9fa732912ed20d825e64f542d79188
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055418"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>Optimera din miljö med System Center Operations Manager-hälsokontrollösningen (förhandsversion)

![Hälsokontroll av System Center Operations Manager](./media/scom-assessment/scom-assessment-symbol.png)

Du kan använda hälsokontrolllösningen System Center Operations Manager för att regelbundet bedöma risken och hälsan för hanteringsgruppen för System Center Operations Manager. Den här artikeln hjälper dig att installera, konfigurera och använda lösningen så att du kan vidta korrigerande åtgärder för potentiella problem.

Den här lösningen innehåller en prioriterad lista med rekommendationer som är specifika för din distribuerade serverinfrastruktur. Rekommendationerna kategoriseras över fyra fokusområden, vilket hjälper dig att snabbt förstå risken och vidta korrigerande åtgärder.

Rekommendationerna är baserade på den kunskap och erfarenhet som Microsofts tekniker fått från tusentals kundbesök. Varje rekommendation ger vägledning om varför ett problem kan vara viktigt för dig och hur du implementerar de föreslagna ändringarna.

Du kan välja fokusområden som är viktigast för din organisation och följa dina framsteg mot att driva en riskfri och hälsosam miljö.

När du har lagt till lösningen och en utvärdering har utförts visas sammanfattande information för fokusområden på instrumentpanelen **System Center Operations Manager** för infrastrukturen. I följande avsnitt beskrivs hur du använder informationen på instrumentpanelen **För hälsokontroll av System Center Operations Manager,** där du kan visa och sedan vidta rekommenderade åtgärder för operationshanterarens miljö.

![Lösningspanel för System Center Operations Manager](./media/scom-assessment/log-analytics-scom-healthcheck-tile.png)

![Instrumentpanelen För hälsokontroll av System Center Operations Manager](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>Installera och konfigurera lösningen

Lösningen fungerar med Microsoft System Center 2012 Operations Manager Service Pack 1, Microsoft System Center 2012 R2 Operations Manager, Microsoft System Center 2016 Operations Manager, Microsoft System Center 2016 Operations Manager och Microsoft System Center Operations Manager 1807. En version som stöds av .NET Framework 4.6.2 måste installeras på varje hanteringsserver.

Använd följande information för att installera och konfigurera lösningen.

- Innan du kan använda hälsokontrolllösningen i Logganalys måste du ha lösningen installerad. Installera lösningen från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview).

- När du har lagt till lösningen på arbetsytan visas ett meddelande om **att systemcenteransvarigas kontrollkontroll** av System Center Operations Manager på instrumentpanelen visar ett ytterligare meddelande om konfiguration. Klicka på panelen och följ konfigurationsstegen som nämns på sidan

  ![Instrumentpanelen för System Center Operations Manager](./media/scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> Konfiguration av System Center Operations Manager kan göras med hjälp av ett skript genom att följa stegen som nämns i konfigurationssidan för lösningen i Log Analytics.

 Så här konfigurerar du utvärderingen via Operations Manager Operations-konsolen:
1. [Ange hälsokontroll av Körning som för Hälsokontroll av System Center Operations Manager](#operations-manager-run-as-accounts-for-log-analytics)  
2. Konfigurera hälsokontrollregeln för System Center Operations Manager

## <a name="system-center-operations-manager-health-check-data-collection-details"></a>Information om datainsamling i System Center Operations Manager-hälsokontroll

Hälsokontrolllösningen System Center Operations Manager samlar in data från följande källor:

* Register
* Windows Management Instrumentation (WMI)
* Händelseloggen
* Fildata
* Direkt från Operations Manager med PowerShell- och SQL-frågor från en hanteringsserver som du har angett.  

Data samlas in på hanteringsservern och vidarebefordras till Log Analytics var sjunde dag.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Kör som-konton i Operations Manager för Log Analytics

Log Analytics bygger på hanteringspaket för arbetsbelastningar för att tillhandahålla mervärdestjänster. Varje arbetsbelastning kräver arbetsbelastningsspecifika privilegier för att köra hanteringspaket i en annan säkerhetskontext, till exempel ett domänanvändarkonto. Konfigurera ett Operations Manager Run As-konto med privilegierade autentiseringsuppgifter. Mer information finns i [Så här skapar du ett Kör som-konto](https://technet.microsoft.com/library/hh321655(v=sc.12).aspx) i Operations Manager-dokumentationen.

Använd följande information för att ange Operations Manager Run As-kontot för Hälsokontroll av System Center Operations Manager.

### <a name="set-the-run-as-account"></a>Ange Kör som-konto

Kontot Kör som måste uppfylla följande krav innan du fortsätter:

* Ett domänanvändarkonto som är medlem i den lokala administratörsgruppen på alla servrar som stöder alla Operations Manager-roller - Hanteringsserver, SQL Server som är värd för drift-, datalager- och ACS-databasen, Rapportering, Webbkonsol och Gateway-server.
* Administratörsroll för Operations Manager för den ledningsgrupp som bedöms
* Om kontot inte har SQL sysadmin-rättigheter kör du [skriptet](#sql-script-to-grant-granular-permissions-to-the-run-as-account) för att bevilja detaljerade behörigheter till kontot för varje SQL Server-instans som är värd för en eller alla Operations Manager-databaser.

1. Välj navigeringsknappen **Administration** i Drifthanteraren.
2. Klicka på **Konton**under **Kör som konfiguration**.
3. Klicka på **Nästa**på sidan **Introduktion** på sidan Introduktion i guiden **Skapa kör som konto** .
4. På sidan **Allmänna egenskaper** väljer du **Windows** i listan Kör **som konto:**
5. Skriv ett visningsnamn i textrutan **Visningsnamn** och skriv eventuellt en beskrivning i rutan **Beskrivning** och klicka sedan på **Nästa**.
6. På sidan **Distributionssäkerhet** väljer du **Säkrare**.
7. Klicka på **Skapa**.  

Nu när kontot Kör som skapas måste det inriktas på hanteringsservrar i hanteringsgruppen och associeras med en fördefinierad Run As-profil så att arbetsflödena körs med autentiseringsuppgifterna.  

1. Dubbelklicka **Accounts**på det konto som du skapade tidigare i resultatfönstret under **Kör som konfiguration**.
2. Klicka på Lägg **till** för rutan **Markerade datorer** på fliken **Distribution** och lägg till hanteringsservern som kontot ska distribueras till.  Klicka på **OK** två gånger för att spara ändringarna.
3. Klicka på **Profiler**under **Kör som konfiguration**.
4. Sök efter *SCOM:s bedömningsprofil*.
5. Profilnamnet ska vara: *Hälsokontroll av Microsoft System Center Operations Manager Kör som profil*.
6. Högerklicka och uppdatera dess egenskaper och lägg till det nyligen skapade Run As Account som du skapade tidigare.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>SQL-skript för att bevilja detaljerade behörigheter till Kör som-kontot

Kör följande SQL-skript för att bevilja nödvändiga behörigheter till kontot Kör som på SQL Server-instansen som används av Operations Manager som är värd för drift-, datalager- och ACS-databasen.

```
-- Replace <UserName> with the actual user name being used as Run As Account.
USE master

-- Create login for the user, comment this line if login is already created.
CREATE LOGIN [UserName] FROM WINDOWS


--GRANT permissions to user.
GRANT VIEW SERVER STATE TO [UserName]
GRANT VIEW ANY DEFINITION TO [UserName]
GRANT VIEW ANY DATABASE TO [UserName]

-- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
-- NOTE: This command must be run anytime new databases are added to SQL Server instances.
EXEC sp_msforeachdb N'USE [?]; CREATE USER [UserName] FOR LOGIN [UserName];'

Use msdb
GRANT SELECT To [UserName]
Go

--Give SELECT permission on all Operations Manager related Databases

--Replace the Operations Manager database name with the one in your environment
Use [OperationsManager];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager DatawareHouse database name with the one in your environment
Use [OperationsManagerDW];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager Audit Collection database name with the one in your environment
Use [OperationsManagerAC];
GRANT SELECT To [UserName]
GO

--Give db_owner on [OperationsManager] DB
--Replace the Operations Manager database name with the one in your environment
USE [OperationsManager]
GO
ALTER ROLE [db_owner] ADD MEMBER [UserName]

```

### <a name="configure-the-health-check-rule"></a>Konfigurera hälsokontrollregeln

Hanteringspaketet System Center Operations Manager innehåller en regel med namnet *Microsoft System Center Operations Manager Run Health Check Rule*. Den här regeln ansvarar för att köra hälsokontrollen. Om du vill aktivera regeln och konfigurera frekvensen använder du procedurerna nedan.

Som standard är hälsokontrollregeln för Microsoft System Center Operations Manager-körning inaktiverad. Om du vill köra hälsokontrollen måste du aktivera regeln på en hanteringsserver. Följ dessa steg.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Aktivera regeln för en specifik hanteringsserver

1. Sök efter regeln **För** regel i *Microsoft System Center Operations Manager Kör hälsokontrollregel* i regelfönstret i fönstret **Regel.**
2. I sökresultaten markerar du den som innehåller texten *Typ: Management Server*.
3. Högerklicka på regeln och klicka sedan på **Åsidosättningar** > **för ett visst objekt i klassen: Management Server**.
4.  I listan över tillgängliga hanteringsservrar väljer du den hanteringsserver där regeln ska köras.  Detta bör vara samma hanteringsserver som du konfigurerat tidigare för att associera kontot Kör som med.
5.  Se till att du ändrar åsidosättningsvärdet till **Sant** för parametervärdet **Aktiverad.**<br><br> ![parameter för åsidosättning](./media/scom-assessment/rule.png)

    Konfigurera körfrekvensen med hjälp av nästa procedur medan du fortfarande är i det här fönstret.

#### <a name="configure-the-run-frequency"></a>Konfigurera körningsfrekvensen

Bedömningen är konfigurerad för att köras var 10 080:e minut (eller sju dagar) som standard. Du kan åsidosätta värdet till ett minimivärde på 1440 minuter (eller en dag). Värdet representerar det minsta tidsgap som krävs mellan på varandra följande utvärderingskörningar. Om du vill åsidosätta intervallet följer du stegen nedan.

1. Sök **Authoring** efter regeln Microsoft System Center Operations Manager *Run Health Check Rule* i avsnittet Regel i Avsnittet **Regelhantering** i avsnittet Regel i Avsnittet Redigering i Operations Manager.
2. I sökresultaten markerar du den som innehåller texten *Typ: Management Server*.
3. Högerklicka på regeln och klicka sedan på **Åsidosätt regeln** > **för alla objekt i klassen: Management Server**.
4. Ändra **parametervärdet för intervall** till önskat intervallvärde. I exemplet nedan anges värdet till 1440 minuter (en dag).<br><br> ![intervallparameter](./media/scom-assessment/interval.png)<br>  

    Om värdet är inställt på mindre än 1440 minuter körs regeln på ett endagsintervall. I det här exemplet ignorerar regeln intervallvärdet och körs med en frekvens på en dag.


## <a name="understanding-how-recommendations-are-prioritized"></a>Förstå hur rekommendationer prioriteras

Varje rekommendation ges ett viktningsvärde som identifierar rekommendationens relativa betydelse. Endast de tio viktigaste rekommendationerna visas.

### <a name="how-weights-are-calculated"></a>Hur vikter beräknas

Viktningar är aggregerade värden baserade på tre nyckelfaktorer:

- *Sannolikheten för* att ett problem identifieras kommer att orsaka problem. En högre sannolikhet motsvarar en större totalpoäng för rekommendationen.
- *Problemets inverkan* på din organisation om det orsakar ett problem. En högre effekt motsvarar en större totalpoäng för rekommendationen.
- De *ansträngningar* som krävs för att genomföra rekommendationen. En högre insats motsvarar en mindre totalpoäng för rekommendationen.

Viktningen för varje rekommendation uttrycks som en procentandel av den totala poängen som är tillgänglig för varje fokusområde. Om till exempel en rekommendation i fokusområdet Tillgänglighet och affärskontinuitet har poängen 5 %, ökar implementeringen av rekommendationen din totala poäng för tillgänglighet och affärskontinuitet med 5 %.

### <a name="focus-areas"></a>Fokusområden

**Tillgänglighet och affärskontinuitet** – Det här fokusområdet visar rekommendationer för tjänstens tillgänglighet, infrastrukturens återhämtning och ditt affärsskydd.

**Prestanda och skalbarhet** – Det här fokusområdet visar rekommendationer som hjälper organisationens IT-infrastruktur att växa, se till att it-miljön uppfyller aktuella prestandakrav och kan svara på förändrade infrastrukturbehov.

**Uppgradera, migrera och distribuera** – Det här fokusområdet visar rekommendationer som hjälper dig att uppgradera, migrera och distribuera SQL Server till din befintliga infrastruktur.

**Drift och övervakning** - Det här fokusområdet visar rekommendationer som hjälper dig att effektivisera din IT-drift, implementera förebyggande underhåll och maximera prestanda.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Bör du sikta på 100 % inom varje fokusområde?

Inte nödvändigtvis. Rekommendationerna baseras på den kunskap och de erfarenheter som Microsofts tekniker har fått vid tusentals kundbesök. Inga två serverinfrastrukturer är dock desamma och specifika rekommendationer kan vara mer eller mindre relevanta för dig. Vissa säkerhetsrekommendationer kan till exempel vara mindre relevanta om dina virtuella datorer inte exponeras för Internet. Vissa tillgänglighetsrekommendationer kan vara mindre relevanta för tjänster som tillhandahåller ad hoc-datainsamling och rapportering med låg prioritet. Frågor som är viktiga för ett moget företag kan vara mindre viktiga för ett nystartigt företag. Du kanske vill identifiera vilka fokusområden som är dina prioriteringar och sedan titta på hur dina poäng förändras med tiden.

Varje rekommendation innehåller vägledning om varför det är viktigt. Använd den här vägledningen för att utvärdera om det är lämpligt att implementera rekommendationen, med tanke på vilken typ av IT-tjänster du har och organisationens affärsbehov.

## <a name="use-health-check-focus-area-recommendations"></a>Använd rekommendationer för fokusområden för hälsokontroll

Innan du kan använda en hälsokontrolllösning i Log Analytics måste du ha lösningen installerad. Mer information om hur du installerar lösningar finns i [Installera en hanteringslösning](../../azure-monitor/insights/solutions.md). När den har installerats kan du visa sammanfattningen av rekommendationer med hjälp av hälsokontrollpanelen System Center Operations Manager på **översiktssidan** för arbetsytan i Azure-portalen.

Visa de sammanfattade efterlevnadsbedömningarna för infrastrukturen och sedan detaljgranska rekommendationer.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Så här visar du rekommendationer för ett fokusområde och vidtar korrigerande åtgärder
1. Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).
2. I Azure Portal klickar du på knappen **Fler tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.
3. I fönstret Logganalysprenumerationer väljer du en arbetsyta och klickar sedan på **menyalternativet Arbetsytesammanfattning.**  
4. Klicka på **hälsokontrollpanelen För hälsokontroll** av SystemCenter Operations Manager på sidan **Översikt.**
5. På sidan **Hälsokontroll** av System Center Operations Manager granskar du sammanfattningsinformationen i ett av fokusområdesbladen och klickar sedan på en för att visa rekommendationer för det fokusområdet.
6. På någon av fokusområdenas sidor kan du visa de prioriterade rekommendationerna för din miljö. Klicka på en rekommendation under **Berörda objekt** om du vill visa information om varför rekommendationen görs.<br><br> ![fokusområde](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. Du kan vidta korrigerande åtgärder som **föreslås**i Föreslagna åtgärder . När objektet har adresserats registrerar senare utvärderingar att rekommenderade åtgärder har vidtagits och att efterlevnadspoängen ökar. Korrigerade objekt visas som **Skickade objekt**.

## <a name="ignore-recommendations"></a>Ignorera rekommendationer

Om du har rekommendationer som du vill ignorera kan du skapa en textfil som Log Analytics använder för att förhindra att rekommendationer visas i utvärderingsresultaten.

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Så här identifierar du rekommendationer som du vill ignorera
1. Klicka på menyalternativet Logga sök i **Azure-portalen** på arbetsytan Log Analytics för den valda arbetsytan.
2. Använd följande fråga för att lista rekommendationer som har misslyckats för datorer i din miljö.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Om arbetsytan har uppgraderats till det [nya frågespråket Log Analytics](../../azure-monitor/log-query/log-query-overview.md)ändras ovanstående fråga till följande.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Här är en skärmdump som visar frågan om loggsökning:<br><br> ![loggsökning](./media/scom-assessment/scom-log-search.png)<br>

3. Välj rekommendationer som du vill ignorera. Du ska använda värdena för RecommendationId i nästa procedur.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Så här skapar och använder du en IgnoreRecommendations.txt-textfil

1. Skapa en fil med namnet IgnoreRecommendations.txt.
2. Klistra in eller skriv varje RekommendationId för varje rekommendation som du vill att Log Analytics ska ignorera på en separat rad och sedan spara och stänga filen.
3. Placera filen i följande mapp på varje dator där du vill att Logganalys ska ignorera rekommendationer.
4. På Hanteringsservern för Operations Manager - *SystemDrive*:\Program\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Så här kontrollerar du att rekommendationerna ignoreras

1. När nästa schemalagda utvärdering körs markeras som standard de angivna rekommendationerna Ignoreras och visas inte på instrumentpanelen för hälsokontroll.
2. Du kan använda följande loggsökfrågor för att lista alla ignorerade rekommendationer.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Om arbetsytan har uppgraderats till det [nya frågespråket Log Analytics](../../azure-monitor/log-query/log-query-overview.md)ändras ovanstående fråga till följande.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Om du senare bestämmer dig för att du vill se ignorerade rekommendationer tar du bort alla IgnoreRecommendations.txt-filer eller så kan du ta bort Rekommendations-ID:er från dem.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>Vanliga frågor och svar om System Center Operations Manager-hälsokontrollösning

*Jag har lagt till hälsokontrolllösningen på min Log Analytics-arbetsyta. Men jag ser inte rekommendationerna. Varför inte?* När du har lagt till lösningen följer du rekommendationerna på log Analytics-instrumentpanelen.  

- [Ange hälsokontroll av Körning som för Hälsokontroll av System Center Operations Manager](#operations-manager-run-as-accounts-for-log-analytics)  
- [Konfigurera hälsokontrollregeln för System Center Operations Manager](#configure-the-health-check-rule)


*Finns det något sätt att konfigurera hur ofta kontrollen körs?* Ja. Se [Konfigurera körfrekvensen](#configure-the-run-frequency).

*Om en annan server upptäcks efter att jag har lagt till health check-lösningen för System Center Operations Manager, kontrolleras den?* Ja, efter upptäckt kontrolleras den från och med då, som standard var sjunde dag.

*Vad är namnet på den process som gör datainsamlingen?* RådgivareAssessment.exe

*Var körs processen AdvisorAssessment.exe?* AdvisorAssessment.exe körs under HealthService-processen för hanteringsservern där hälsokontrollregeln är aktiverad. Med den processen kan hela din miljö upptäckas genom fjärrdatainsamling.

*Hur lång tid tar det för datainsamling?* Datainsamling på servern tar ungefär en timme. Det kan ta längre tid i miljöer som har många Operations Manager-instanser eller databaser.

*Vad händer om jag ställer in intervallet för bedömningen till mindre än 1440 minuter?* Bedömningen är förkonfigurerad för att köras med högst en gång per dag. Om du åsidosätter intervallvärdet till ett värde som är mindre än 1440 minuter används 1440 minuter som intervallvärde.

*Hur vet man om det finns förutsättningar fel?* Om hälsokontrollen kördes och du inte ser resultat, är det troligt att några av förutsättningarna för kontrollen misslyckades. Du kan köra `Operation Solution=SCOMAssessment` frågor: och `SCOMAssessmentRecommendation FocusArea=Prerequisites` i Loggsökning för att se de misslyckade förutsättningarna.

*Det finns `Failed to connect to the SQL Instance (….).` ett meddelande i nödvändiga fel. Vad är problemet?* AdvisorAssessment.exe, processen som samlar in data, körs under HealthService-processen på hanteringsservern. Som en del av hälsokontrollen försöker processen ansluta till SQL Server där Operations Manager-databasen finns. Det här felet kan uppstå när brandväggsregler blockerar anslutningen till SQL Server-instansen.

*Vilken typ av data samlas in?* Följande typer av data samlas in: - WMI-data - Registerdata - EventLog-data - Operations Manager-data via Windows PowerShell, SQL-frågor och filinformationsinsamlare.

*Varför måste jag konfigurera ett Run As-konto?* Med Operations Manager körs olika SQL-frågor. För att de ska kunna köras måste du använda ett Run As-konto med nödvändiga behörigheter. Dessutom krävs lokala administratörsbehörighet för att fråga WMI.

*Varför visa bara de 10 rekommendationer?* I stället för att ge dig en uttömmande, överväldigande lista över uppgifter rekommenderar vi att du fokuserar på att ta itu med de prioriterade rekommendationerna först. När du har åtgärdat dem blir ytterligare rekommendationer tillgängliga. Om du föredrar att se den detaljerade listan kan du visa alla rekommendationer med hjälp av Loggsökning.

*Finns det något sätt att ignorera en rekommendation?* Ja, se [ignorera rekommendationer](#ignore-recommendations).


## <a name="next-steps"></a>Nästa steg

- [Sökloggar](../../azure-monitor/log-query/log-query-overview.md) om du vill lära dig hur du analyserar detaljerade hälsokontrolldata och rekommendationer för System Center Operations Manager.
