---
title: "Optimera din miljö för System Center Operations Manager med Azure Log Analytics | Microsoft Docs"
description: "Du kan använda System Center Operations Manager Health Check-lösning för att bedöma risken och hälsotillståndet för dina miljöer regelbundna intervall."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: tysonn
ms.assetid: 49aad8b1-3e05-4588-956c-6fdd7715cda1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 86484ca2bc7dc14035f48b8f7b1514a4fc471b74
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2018
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>Optimera din miljö med System Center Operations Manager Health Check (förhandsgranskning)-lösning

![System Center Operations Manager Health Check symbol](./media/log-analytics-scom-assessment/scom-assessment-symbol.png)

Du kan använda System Center Operations Manager Health Check-lösning för att bedöma risken och hälsotillståndet för hanteringsgruppen System Center Operations Manager regelbundna intervall. Den här artikeln hjälper dig att installera, konfigurera och använda lösningen så att du kan vidta åtgärder för möjliga problem.

Den här lösningen ger en prioriterad lista med rekommendationer som är specifika för din distribuerade serverinfrastruktur. Rekommendationerna kategoriseras i fyra fokusområden, som hjälper dig att snabbt förstå risken och vidta åtgärder.

Rekommendationer baserat på kunskap och erfarenhet av Microsoft-tekniker från tusentals kunden besök. Varje rekommendation innehåller information om varför ett problem kan verkligen är viktiga och hur du implementerar de föreslagna ändringarna.

Du kan välja fokusområden som är viktigast för din organisation och spåra din framsteg mot kör en risk ledigt och hälsosam miljö.

När du har lagt till lösningen och en bedömning är utförs, Sammanfattning visas information för fokusområden på den **System Center Operations Manager Health Check** instrumentpanel för din infrastruktur. I följande avsnitt beskrivs hur du använder informationen på den **System Center Operations Manager Health Check** instrumentpanel, där du kan visa och sedan vidta rekommenderade åtgärder för din Operations Manager-miljö.

![System Center Operations Manager-lösningen sida vid sida](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-tile.png)

![System Center Operations Manager Health Check instrumentpanelen](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>Installera och konfigurera lösningen

Lösningen fungerar med Microsoft System Operations Manager 2012 Service Pack (SP) 1 och 2012 R2.

Använd följande information för att installera och konfigurera lösningen.

 - Innan du kan använda Health Check-lösning i logganalys, måste du ha lösningen installerad. Installera lösning från [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview).

 - När du lägger till lösningen till arbetsytan i **System Center Operations Manager Health Check** panel på instrumentpanelen visar ett meddelande om ytterligare konfiguration krävs. Klicka på panelen och följ konfigurationsstegen anges på sidan

 ![System Center Operations Manager-instrumentpanelen](./media/log-analytics-scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> Konfiguration av System Center Operations Manager kan göras med hjälp av ett skript genom att följa anvisningarna i konfigurationssidan för lösningen i logganalys.

 Utför stegen nedan om du vill konfigurera assessment via Operations Manager Operations-konsolen i följande ordning:
1. [Ange kör som-kontot för System Center Operations Manager Health Check](#operations-manager-run-as-accounts-for-log-analytics)  
2. [Konfigurera System Center Operations Manager Health Check-regel](#configure-the-assessment-rule)

## <a name="system-center-operations-manager-assessment-data-collection-details"></a>System Center Operations Manager assessment data samling information

System Center Operations Manager-bedömning samlar in data från följande källor:

* Register
* Windows Management Instrumentation (WMI)
* Händelseloggen
* Fildata
* Med hjälp av PowerShell och SQL-frågor från en hanteringsserver som du har angett direkt från Operations Manager.  

Data samlas in på hanteringsservern och vidarebefordras till logganalys var sjunde dag.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Operations Manager kör som-konton för logganalys

Log Analytics bygger på hanteringspaket för arbetsbelastningar att ge mervärde tjänster. Varje arbetsbelastning kräver belastningsspecifikt behörighet att köra hanteringspaket i en annan säkerhetskontext, till exempel ett domänanvändarkonto. Konfigurera en Operations Manager kör som-konto med autentiseringsuppgifter. För ytterligare information, se [hur du skapar ett kör som-konto](https://technet.microsoft.com/library/hh321655(v=sc.12).aspx) i Operations Manager-dokumentationen.

Använd följande information för att ange Operations Manager kör som-kontot för System Center Operations Manager Health Check.

### <a name="set-the-run-as-account"></a>Ange kör som-konto

Kör som-kontot måste uppfylla följande krav innan du fortsätter:

* Ett domänanvändarkonto som är medlem i gruppen lokala administratörer på alla servrar som stöder någon roll för Operations Manager - hanteringsservern, SQL Server som värd för den operativa, datalagret och ACS-databasen, rapportering, Web console och Gateway-servern.
* Åtgärden Managers administratörsroll för hanteringsgruppen som ska utvärderas
* Om kontot inte har sysadmin-rättigheter för SQL kör du den [skriptet](#sql-script-to-grant-granular-permissions-to-the-run-as-account) att ge detaljerade behörigheter till kontot på varje SQL Server-instans som är värd för en eller alla av Operations Manager-databaserna.

1. I Operations Manager-konsolen väljer du den **Administration** navigeringsknapp.
2. Under **kör som-konfiguration**, klickar du på **konton**.
3. I den **skapa kör som-konto** guiden klickar du på den **introduktion** klickar **nästa**.
4. På den **allmänna egenskaper** väljer **Windows** i den **kör som-kontotypen:** lista.
5. Skriv ett namn i den **visningsnamn** text och eventuellt en beskrivning i den **beskrivning** rutan och klicka på **nästa**.
6. På den **Distributionssäkerhet** väljer **säkrare**.
7. Klicka på **Skapa**.  

Nu när kör som-kontot har skapats måste mål-hanteringsservrar i hanteringsgruppen och som är associerade med en fördefinierad kör som-profil så arbetsflöden körs med autentiseringsuppgifterna.  

1. Under **kör som-konfiguration**, **konton**, dubbelklicka på det konto som du skapade tidigare i resultatfönstret.
2. På den **Distribution** klickar du på **Lägg till** för den **valda datorer** och ange om du vill distribuera kontot på hanteringsservern.  Klicka på **OK** två gånger för att spara ändringarna.
3. Under **kör som-konfiguration**, klickar du på **profiler**.
4. Sök efter den *SCOM Assessment profil*.
5. Namnet på profilen bör vara: *Microsoft System Center Advisor SCOM Assessment kör som-profilen*.
6. Högerklicka på och uppdatera dess egenskaper och lägga till nyligen har skapat kör som-kontot du skapade tidigare.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>SQL-skript för att ge detaljerade behörigheter till Kör som-konto

Kör följande SQL-skript för att bevilja behörighet till Kör som-konto på SQL Server-instansen som används av Operations Manager som är värd för den operativa, datalagret och ACS-databasen.

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

### <a name="configure-the-health-check-rule"></a>Konfigurera health check regel

Management pack för lösning för System Center Operations Manager Health Check innehåller en regel med namnet *Microsoft System Center Advisor SCOM Assessment kör Assessment regeln*. Den här regeln är ansvarig för att köra hälsotillståndskontroll. Använd procedurerna nedan om du vill aktivera regeln och ange hur ofta.

Microsoft System Center Advisor SCOM Assessment kör Assessment regeln är inaktiverad som standard. Om du vill köra hälsotillståndskontroll måste du aktivera regeln på en hanteringsserver. Använd följande steg.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Aktivera regeln för en viss hanteringsserver

1. I den **redigering** arbetsytan i Operations Manager Operations-konsolen, Sök efter regeln *Microsoft System Center Advisor SCOM Assessment kör Assessment regeln* i den **regler** fönstret.
2. I sökresultaten väljer du det alternativ som innehåller texten *typ: hanteringsservern*.
3. Högerklicka på regeln och klickar sedan på **åsidosätter** > **för ett specifikt objekt i klassen: hanteringsservern**.
4.  Välj hanteringsservern där regeln ska köras i listan tillgängliga management-servrar.  Detta bör vara samma hanteringsserver som du tidigare har konfigurerat för att koppla kör som-kontot med.
5.  Se till att du ändrar åsidosättningsvärde till **SANT** för den **aktiverad** parametervärdet.<br><br> ![åsidosätt parametern](./media/log-analytics-scom-assessment/rule.png)

    Konfigurera kör frekvensen med nästa procedur fortfarande i det här fönstret.

#### <a name="configure-the-run-frequency"></a>Konfigurera kör frekvens

Bedömning är konfigurerad för att köras var 10 080 minuter (eller sju dagar) som standard. Du kan åsidosätta värdet till ett minsta värde för 1440 minuter (eller en dag). Värdet som representerar minimitid mellanrummet krävs mellan på varandra följande assessment körs. Följ anvisningarna nedan om du vill åsidosätta intervallet.

1. I den **redigering** arbetsytan i Operations Manager-konsolen, Sök efter regeln *Microsoft System Center Advisor SCOM Assessment kör Assessment regeln* i den **regler** avsnittet.
2. I sökresultaten väljer du det alternativ som innehåller texten *typ: hanteringsservern*.
3. Högerklicka på regeln och klickar sedan på **Åsidosätt regeln** > **för alla objekt i klassen: hanteringsservern**.
4. Ändra den **intervall** parametervärde för din önskade intervall. I exemplet nedan anges värdet till 1 440 minuter (en dag).<br><br> ![intervall för parametern](./media/log-analytics-scom-assessment/interval.png)<br>  

    Om värdet anges till mindre än 1 440 minuter kör regeln på ett intervall med en dag. I det här exemplet regeln ignoreras värdet för intervallet och körs med en frekvens som en dag.


## <a name="understanding-how-recommendations-are-prioritized"></a>Förstå hur rekommendationer prioriteras

Varje rekommendation är ett givet värde som identifierar en avvägning mellan kraven för rekommendationen. Endast de 10 viktigaste rekommendationerna visas.

### <a name="how-weights-are-calculated"></a>Hur vikterna beräknas

Viktningar är sammanställda värden som baseras på tre viktiga faktorer:

- Den *sannolikhet* att ett problem som identifieras ska orsaka problem. En högre sannolikhet är lika med en större övergripande poäng för rekommendationen.
- Den *inverkan* av problemet i din organisation om det uppstår ett problem. En högre inverkan är lika med en större övergripande poäng för rekommendationen.
- Den *arbete* krävs för att implementera denna rekommendation. Det är lika med en mindre övergripande poäng för rekommendationen högre prestanda.

Värde för varje rekommendation uttrycks i procent av den sammanlagda poängen för varje fokusområde. Till exempel ökar implementera denna rekommendation om en rekommendation i området för tillgänglighet och affärskontinuitet fokus har ett resultat på 5%, din övergripande tillgänglighet och affärskontinuitet poäng av 5%.

### <a name="focus-areas"></a>Fokusområden

**Tillgänglighet och affärskontinuitet** -fokus visas här rekommendationer för tjänstetillgänglighet, återhämtning för din infrastruktur och business-skydd.

**Prestanda och skalbarhet** -fokus visas här rekommendationer som hjälper din organisations IT-infrastruktur växer, se till att din IT-miljö uppfyller aktuella prestandakrav och kan svara på förändrade infrastruktur behov.

**Uppgradering och migrering distribution** -fokus visas här rekommendationer när du uppgraderar, migrera och distribuera SQL Server till den befintliga infrastrukturen.

**Åtgärder och övervakning** -fokus visas här rekommendationer för att effektivisera IT-avdelningen, implementera förebyggande underhåll och maximera prestanda.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Bör du försöka poängsätta 100% överallt fokus i?

Inte nödvändigtvis. Rekommendationerna baseras på kunskaper och erfarenheter som gjorts av Microsoft-tekniker över tusentals kunden besök. Dock inga två server-infrastrukturen är samma och specifika rekommendationer kan vara mer eller mindre relevant för dig. Exempelvis kanske vissa säkerhetsrekommendationer mindre relevant om de virtuella datorerna inte utsätts för Internet. Vissa tillgänglighet rekommendationer kan vara relevanta för tjänster som erbjuder med låg prioritet ad hoc-insamling och rapportering. Problem som är viktiga för en mogen företag kanske mindre viktiga för en Startup. Du kanske vill identifiera vilka fokusområden är dina prioriteter och titta på hur ditt resultat förändras över tid.

Varje rekommendation innehåller information om varför det är viktigt. Använd den här vägledningen för att utvärdera om implementera rekommendationen är lämplig för dig, baserat på typ av din IT-tjänster och affärsbehoven för din organisation.

## <a name="use-health-check-focus-area-recommendations"></a>Använd hälsokontroll fokus området rekommendationer

Innan du kan använda en lösning för kontroll av hälsotillstånd i logganalys, måste du ha lösningen installerad. Du kan läsa mer om hur du installerar lösningar finns [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md). När den har installerats kan du visa sammanfattning av rekommendationer med hjälp av System Center Operations Manager Health Check-panelen på översiktssidan i OMS-portalen.

Visa sammanfattade efterlevnad bedömningar för din infrastruktur och gå till rekommendationer.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Visa rekommendationer för en Fokusområde och vidta åtgärder
1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. I Azure Portal klickar du på knappen **Fler tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.
3. Välj en arbetsyta i fönstret logganalys prenumerationer och klicka sedan på den **OMS-portalen** panelen.  
4. På den **översikt** klickar du på den **System Center Operations Manager Health Check** panelen.
5. På den **System Center Operations Manager Health Check** , Granska sammanfattningen i ett fokus området blad och klickar sedan på en om du vill visa rekommendationer för området fokus.
6. På någon av sidorna fokus område, kan du visa prioriterad rekommendationer för din miljö. Klicka på en rekommendation enligt **påverkade objekt** att visa information om varför rekommendationen görs.<br><br> ![Fokusområde](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. Du kan vidta åtgärder i **föreslagna åtgärder**. När objektet har behandlats registrerar senare bedömningar den rekommenderade åtgärder som utförts och kompatibilitet poängen ökar. Korrigerade objekt visas som **skickades objekt**.

## <a name="ignore-recommendations"></a>Ignorera rekommendationer

Om du har rekommendationer som du vill ignorera, kan du skapa en textfil som logganalys används för att förhindra rekommendationer visas i sökresultatet assessment.

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Att identifiera rekommendationer som du vill ignorera
1. I Azure-portalen på sidan för logganalys-arbetsytan för den valda arbetsytan klickar du på den **loggen Sök** panelen.
2. Använd följande fråga för att lista över rekommendationer som har misslyckats för datorer i din miljö.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Om ditt arbetsområde har uppgraderats till den [nya Log Analytics-frågespråket](log-analytics-log-search-upgrade.md), sedan frågan ovan skulle ändra till följande.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Här är en skärmbild som visar frågan loggen:<br><br> ![loggsökning](./media/log-analytics-scom-assessment/scom-log-search.png)<br>

3. Välj rekommendationer som du vill ignorera. Du ska använda värdena för RecommendationId i nästa procedur.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Du skapar och använder en IgnoreRecommendations.txt textfil

1. Skapa en fil med namnet IgnoreRecommendations.txt.
2. Klistra in eller ange varje RecommendationId för varje rekommendation som du vill logganalys Ignorera på en separat rad och spara och stäng filen.
3. Placera filen i följande mapp på varje dator där du vill att logganalys att ignorera rekommendationer.
4. På hanteringsservern för Operations Manager - *SystemDrive*: \Program\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Kontrollera att rekommendationer ignoreras

1. När nästa schemalagda utvärdering körs som standard var sjunde dag, angivna rekommendationerna markeras ignoreras och visas inte på instrumentpanelen för hälsotillstånd kontroll.
2. Du kan använda följande loggen sökfrågor för att lista alla rekommendationer som ignoreras.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Om ditt arbetsområde har uppgraderats till den [nya Log Analytics-frågespråket](log-analytics-log-search-upgrade.md), sedan frågan ovan skulle ändra till följande.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Om du senare bestämmer att du vill se ignoreras rekommendationer, ta bort IgnoreRecommendations.txt filer eller du kan ta bort RecommendationIDs från dem.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>System Center Operations Manager Health Check lösning vanliga frågor och svar

*Jag har lagt till Health Check-lösningen till logganalys-arbetsytan. Men du ser inte rekommendationer. Varför inte?* Använda följande steg vy rekommendationer när du lägger till lösningen på logganalys-instrumentpanelen.  

- [Ange kör som-kontot för System Center Operations Manager Health Check](#operations-manager-run-as-accounts-for-log-analytics)  
- [Konfigurera System Center Operations Manager Health Check-regel](#configure-the-health-check-rule)


*Finns det ett sätt att konfigurera hur ofta kontrollen körs?* Ja. Se [konfigurera kör frekvensen](#configure-the-run-frequency).

*Om en annan server identifieras när jag har lagt till System Center Operations Manager Assessment lösningen, kommer den kontrolleras?* Ja, efter identifieringen kontrolleras det kommer som standard var sjunde dag.

*Vad är namnet på processen som gör datainsamlingen?* AdvisorAssessment.exe

*Där körs AdvisorAssessment.exe processen?* AdvisorAssessment.exe körs under processen HealthService för hanteringsservern där health check regeln är aktiverad. Med den här processen uppnås identifiering av hela miljön genom insamling av fjärråtkomst.

*Hur lång tid tar det för insamling av data?* Insamling av data på servern tar ungefär en timme. Det kan ta längre tid i miljöer som har många instanser för Operations Manager eller databaser.

*Vad händer om jag ange intervallet av bedömningen till mindre än 1 440 minuter?* Utvärderingen har förkonfigurerats så att köras på högst en gång per dag. Om du åsidosätter intervallvärdet till ett värde mindre än 1 440 minuter använder bedömningen 1440 minuter som värdet för intervallet.

*Hur vet jag om det finns nödvändiga fel?* Om hälsotillståndskontroll kördes och du inte ser resultaten, är det troligt att vissa av de nödvändiga förutsättningarna för kontrollen misslyckades. Du kan köra frågor: `Operation Solution=SCOMAssessment` och `SCOMAssessmentRecommendation FocusArea=Prerequisites` i loggen Sök om du vill se förutsättningarna som misslyckades.

*Det finns en `Failed to connect to the SQL Instance (….).` meddelandet i nödvändiga fel. Vad är problemet?* AdvisorAssessment.exe, processen som samlar in data, som körs under processen HealthService på hanteringsservern. Som en del av hälsotillståndskontroll, processen som försöker ansluta till SQL Server där Operations Manager-databasen finns. Det här felet kan inträffa när brandväggsregler blockera anslutningen till SQL Server-instansen.

*Vilken typ av data som samlas in?* Följande typer av data har samlats in: - WMI-data – data - EventLog-data – Operations Manager registerdata via Windows PowerShell, SQL-frågor och filen information insamlaren.

*Varför måste jag konfigurera ett kör som-konto?* Olika SQL-frågor körs med Operations Manager. För att kunna köra måste du använda ett kör som-konto med tillräcklig behörighet. Dessutom krävs lokal administratörsbehörighet för att fråga WMI.

*Varför visas endast topp 10 rekommendationer?* I stället för att ge dig en fullständig, överväldigande lista över aktiviteter, rekommenderar vi att du fokusera på adressering prioriterad rekommendationerna först. När du åtgärda dem blir ytterligare rekommendationer tillgängliga. Du kan visa alla rekommendationer loggen sökning om du vill se en detaljerad lista.

*Finns det ett sätt att ignorera en rekommendation?* Ja, finns det [Ignorera rekommendationer](#Ignore-recommendations).


## <a name="next-steps"></a>Nästa steg

- [Söka i loggar](log-analytics-log-searches.md) att lära dig att analysera detaljerad information för System Center Operations Manager Health Check och rekommendationer.
