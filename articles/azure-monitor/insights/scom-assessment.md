---
title: Optimera din miljö för System Center Operations Manager med Azure Log Analytics | Microsoft Docs
description: Du kan använda System Center Operations Manager Health Check-lösningen för att utvärdera risker och bedöm hälsotillståndet i dina miljöer med regelbundna intervall.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 49aad8b1-3e05-4588-956c-6fdd7715cda1
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: magoedte
ms.openlocfilehash: fc625192464dce174b4c2a6d8a2a98343519699f
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53186131"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>Optimera din miljö med lösningen för System Center Operations Manager hälsokontroll (förhandsversion)

![Symbol för System Center Operations Manager-hälsokontroll](./media/scom-assessment/scom-assessment-symbol.png)

Du kan använda System Center Operations Manager Health Check-lösningen för att utvärdera risker och hälsotillstånd i din hanteringsgrupp för System Center Operations Manager med regelbundna intervall. Den här artikeln hjälper dig att installera, konfigurera och använda lösningen så att du kan vidta åtgärder för potentiella problem.

Den här lösningen ger en prioriterad lista över rekommendationer som är specifika för din distribuerade serverinfrastruktur. Rekommendationerna kategoriseras i fyra fokusområden som hjälper dig att snabbt förstå risken och vidta åtgärder.

Rekommendationerna baseras på kunskap och erfarenhet av Microsoft-tekniker från tusentals kunder besök. Varje rekommendation ger vägledning om varför ett problem kan är viktiga för dig och hur du implementerar de föreslagna ändringarna.

Du kan välja fokusområden som är viktigast för din organisation och spåra förloppet mot att köra en risk kostnadsfria och hälsosam miljö.

När du har lagt till lösningen och en utvärdering är genomförd, sammanfattande informationen för fokusområden visas på den **System Center Operations Manager Health Check** instrumentpanelen för din infrastruktur. I följande avsnitt beskrivs hur du använder informationen på den **System Center Operations Manager Health Check** instrumentpanelen där du kan visa och sedan vidta rekommenderade åtgärder för din Operations Manager-miljö.

![Panel för System Center Operations Manager-lösning](./media/scom-assessment/log-analytics-scom-healthcheck-tile.png)

![Instrumentpanel för System Center Operations Manager-hälsokontroll](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>Installera och konfigurera lösningen

Den här lösningen fungerar med Microsoft System Operations Manager 2012 Service Pack (SP) 1 och 2012 R2.

Använd följande information för att installera och konfigurera lösningen.

 - Innan du kan använda lösningen för hälsokontroll i Log Analytics, måste du ha installerat lösningen. Installera lösningen från [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview).

 - När du lägger till lösningen till arbetsytan, den **System Center Operations Manager Health Check** panelen på instrumentpanelen visar ett meddelande om ytterligare konfiguration krävs. Klicka på panelen och följ konfigurationsstegen på sidan

 ![Instrumentpanel för System Center Operations Manager](./media/scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> Konfiguration av System Center Operations Manager kan göras med hjälp av ett skript genom att följa stegen i konfigurationssidan för lösningen i Log Analytics.

 Utför stegen nedan för att konfigurera utvärderingen via Operations Manager Operations-konsolen, i följande ordning:
1. [Ange kör som-kontot för System Center Operations Manager hälsokontroll](#operations-manager-run-as-accounts-for-log-analytics)  
2. [Konfigurera System Center Operations Manager Health Check-regel](#configure-the-assessment-rule)

## <a name="system-center-operations-manager-assessment-data-collection-details"></a>System Center Operations Manager data samling information om utvärdering

System Center Operations Manager-utvärderingen samlar in data från följande källor:

* Register
* Windows Management Instrumentation (WMI)
* Händelseloggen
* Fildata
* Direkt från Operations Manager med PowerShell och SQL-frågor från en hanteringsserver som du har angett.  

Data som samlas in på hanteringsservern och vidarebefordras till logganalys var sjunde dag.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Operations Manager kör som-konton för Log Analytics

Log Analytics bygger på hanteringspaket för arbetsbelastningar att ge mervärde tjänster. Varje arbetsbelastning kräver belastningsspecifikt behörighet att köra hanteringspaket i en säkerhetskontext, till exempel ett domänanvändarkonto. Konfigurera en Operations Manager kör som-konto med autentiseringsuppgifter. Mer information finns i [så här skapar du ett kör som-konto](https://technet.microsoft.com/library/hh321655(v=sc.12).aspx) i Operations Manager-dokumentationen.

Använd följande information för att ställa in Operations Manager kör som-kontot för System Center Operations Manager Health Check.

### <a name="set-the-run-as-account"></a>Ange kör som-konto

Kör som-kontot måste uppfylla följande krav innan du fortsätter:

* Ett domänanvändarkonto som är medlem i gruppen lokala administratörer på alla servrar som stöder en roll för Operations Manager - hanteringsservern, SQL Server som är värd för driftdatabasen, informationslagret och ACS-databasen, rapportering, Web console och Gateway-servern.
* Operation Manager-administratörsrollen för hanteringsgruppen som utvärderas
* Om kontot inte har SQL sysadmin-rättigheter, kör du den [skriptet](#sql-script-to-grant-granular-permissions-to-the-run-as-account) att tilldela detaljerade behörigheter till kontot på varje SQL Server-instansen som är värd för en eller alla av Operations Manager-databaserna.

1. I Operations Manager-konsolen väljer du den **Administration** navigeringsknapp.
2. Under **kör som-konfiguration**, klickar du på **konton**.
3. I den **skapa kör som konto** guiden på den **introduktion** klickar du på **nästa**.
4. På den **allmänna egenskaper** väljer **Windows** i den **typ av kör som-konto:** lista.
5. Skriv ett namn i den **visningsnamn** text rutan och du kan också ange en beskrivning i den **beskrivning** rutan och klicka sedan på **nästa**.
6. På den **Distributionssäkerhet** väljer **säkrare**.
7. Klicka på **Skapa**.  

Nu när kör som-kontot har skapats måste mål hanteringsservrar i hanteringsgruppen och som är associerade med en fördefinierade kör som-profil så att arbetsflöden ska köras med autentiseringsuppgifter.  

1. Under **kör som-konfiguration**, **konton**, i resultatfönstret dubbelklickar du på det konto som du skapade tidigare.
2. På den **Distribution** fliken **Lägg till** för den **valda datorer** lägger till hanteringsservern för att distribuera kontot på.  Klicka på **OK** två gånger för att spara dina ändringar.
3. Under **kör som-konfiguration**, klickar du på **profiler**.
4. Sök efter den *SCOM-utvärdering profil*.
5. Namnet på profilen bör vara: *SCOM-utvärdering för Microsoft System Center Advisor kör som-profil*.
6. Högerklicka på och uppdatera dess egenskaper och lägga till nyligen skapade kör som-kontot du skapade tidigare.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>SQL-skript för att tilldela detaljerade behörigheter till Kör som-konto

Kör följande SQL-skript för att bevilja behörigheter som krävs för att kör som-kontot för SQL Server-instansen som används av Operations Manager som är värd för driftdatabasen, informationslagret och ACS-databasen.

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

### <a name="configure-the-health-check-rule"></a>Konfigurera kontrollen hälsoregeln

Management pack för lösning för System Center Operations Manager Health Check innehåller en regel med namnet *Microsoft System Center Advisor SCOM-utvärdering kör utvärdering av regel*. Den här regeln är ansvarig för att köra hälsokontrollen. Om du vill aktivera regeln och ange hur ofta, följer du anvisningarna nedan.

Microsoft System Center Advisor SCOM-utvärdering kör utvärdering av regeln är inaktiverad som standard. Om du vill köra hälsokontroller, måste du aktivera regeln på en hanteringsserver. Använd följande steg.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Aktivera regeln för en viss hanteringsserver

1. I den **redigering** arbetsyta i Operations Manager Operations-konsolen, Sök efter regeln *Microsoft System Center Advisor SCOM-utvärdering kör utvärdering av regeln* i den **regler** fönstret.
2. I sökresultaten väljer du det som innehåller texten *typ: Hanteringsservern*.
3. Högerklicka på regeln och klickar sedan på **åsidosätter** > **för ett specifikt objekt i klassen: Hanteringsservern**.
4.  Välj management-servern där regeln ska köras i listan tillgängliga servrar.  Det bör vara samma hanteringsserver som du konfigurerade tidigare för att associera kör som-kontot med.
5.  Se till att du ändrar åsidosättningsvärde till **SANT** för den **aktiverad** parametervärde.<br><br> ![åsidosätt parametern](./media/scom-assessment/rule.png)

    Konfigurera kör frekvensen med nästa procedur fortfarande i det här fönstret.

#### <a name="configure-the-run-frequency"></a>Ange kör ofta

Utvärderingen är konfigurerad för att köras var 10 080 minuter (eller sju dagar) som standard. Du kan åsidosätta värdet till minsta värdet 1 440 minuter (eller en dag). Värdet som representerar minimitid klyftan krävs mellan efterföljande utvärdering körs. Följ anvisningarna nedan om du vill åsidosätta intervallet.

1. I den **redigering** arbetsyta i Operations Manager-konsolen, Sök efter regeln *Microsoft System Center Advisor SCOM-utvärdering kör utvärdering av regeln* i den **regler** avsnittet.
2. I sökresultaten väljer du det som innehåller texten *typ: Hanteringsservern*.
3. Högerklicka på regeln och klickar sedan på **Åsidosätt regeln** > **för alla objekt i klassen: Hanteringsservern**.
4. Ändra den **intervall** din önskade intervallvärdet parametervärde. I exemplet nedan anges värdet till 1 440 minuter (en dag).<br><br> ![intervall för parametern](./media/scom-assessment/interval.png)<br>  

    Om värdet anges till mindre än 1 440 minuter, körs regeln på ett intervall för en dag. I det här exemplet regeln ignorerar intervallvärdet och kör en frekvens på en dag.


## <a name="understanding-how-recommendations-are-prioritized"></a>Förstå hur rekommendationer är prioriterade

Varje rekommendation ges ett värde-värde som identifierar den relativa prioriteten för rekommendationen. Endast de 10 viktigaste rekommendationerna visas.

### <a name="how-weights-are-calculated"></a>Hur vikterna beräknas

Viktningar är aggregerade värden baserat på tre viktiga faktorer:

- Den *sannolikheten* ett identifierat problem medför problem. En högre sannolikhet motsvarar en större övergripande poäng för rekommendationen.
- Den *inverkan* av problemet i din organisation om det uppstår ett problem. En högre inverkan motsvarar en större övergripande poäng för rekommendationen.
- Den *arbete* krävs för att implementera rekommendationen. En högre ansträngning motsvarar en mindre övergripande poäng för rekommendationen.

Värde för varje rekommendation uttrycks som en procentandel av den sammanlagda poängen för varje fokusområde. Till exempel ökar implementera den här rekommendationen om en rekommendation i Fokusområde tillgänglighet och affärskontinuitet har ett resultat på 5%, din övergripande poäng med 5% för tillgänglighet och affärskontinuitet.

### <a name="focus-areas"></a>Fokusområden

**Tillgänglighet och affärskontinuitet** – den här Fokusområde visar rekommendationer för tjänstens tillgänglighet, återhämtning för din infrastruktur och verksamhetsskydd.

**Prestanda och skalbarhet** – den här Fokusområde visar rekommendationer för att din organisations IT-infrastruktur växa, se till att din IT-miljö uppfyller aktuella prestandakrav och kan svara på förändrade infrastruktur behov.

**Uppgradering, migrering och distribution** – den här Fokusområde visar rekommendationer som hjälper dig att uppgradera, migrera och distribuera SQL Server till din befintliga infrastruktur.

**Åtgärder och övervakning** – den här Fokusområde visar rekommendationer för att effektivisera din IT-verksamhet, implementera förebyggande underhåll och maximera prestanda.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Bör du försöka poäng 100% i varje Fokusområde?

Inte nödvändigtvis. Rekommendationerna baseras på de kunskaper och erfarenheter som gjorts av Microsofts tekniker till tusentals kunder besök. Men ingen två server-infrastrukturer är desamma och specifika rekommendationer kan vara mer eller mindre relevant för dig. Exempelvis kanske vissa säkerhetsrekommendationer mindre relevant om dina virtuella datorer inte exponeras för Internet. Vissa rekommendationer för tillgänglighet kan vara mindre användbart för tjänster som erbjuder låg prioritet ad hoc-insamling och rapportering. Problem som är viktiga för en mogen affärsverksamhet som kan vara mindre viktiga för en startfil. Du kanske vill identifiera vilka fokusområden som är dina prioriteringar och titta på hur ditt resultat ändras med tiden.

Varje rekommendation innehåller information om varför det är viktigt. Använd den här vägledningen för att utvärdera om implementerar rekommendationen är lämplig för dig, baserat på typen av dina IT-tjänster och affärsbehoven för din organisation.

## <a name="use-health-check-focus-area-recommendations"></a>Använd hälsokontrollen fokus området rekommendationer

Innan du kan använda en lösning för kontroll av hälsotillstånd i Log Analytics, måste du ha installerat lösningen. Läsa mer om hur du installerar lösningar, se [installera en lösning för](../../azure-monitor/insights/solutions.md). När den har installerats, du kan visa en sammanfattning av rekommendationer med hjälp av System Center Operations Manager Health Check-panel på den **översikt** för din arbetsyta i Azure-portalen.

Visa de sammanfattade efterlevnad utvärderingarna för din infrastruktur och sedan gå till rekommendationer.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Visa rekommendationer för en Fokusområde och vidta åtgärder
1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. I Azure Portal klickar du på knappen **Fler tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.
3. Välj en arbetsyta i fönstret Log Analytics-prenumerationer och klicka sedan på den **arbetsytan Sammanfattning** menyalternativ.  
4. På den **översikt** klickar du på den **System Center Operations Manager Health Check** panelen.
5. På den **System Center Operations Manager Health Check** granskar den sammanfattande informationen i något av bladen fokus område och klicka sedan på ett om du vill visa rekommendationer för den fokusområde.
6. På någon av sidorna fokus området, kan du visa prioriterade rekommendationer för din miljö. Klicka på en rekommendation under **påverkade objekt** att visa information om varför rekommendationen görs.<br><br> ![Fokusområde](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. Du kan vidta korrigerande åtgärder som föreslås i **föreslagna åtgärder**. När objektet har utförts, registrera senare utvärderingar som rekommenderar åtgärder som utförts och din kompatibilitetspoäng ökar. Korrigerad objekt visas som **skickas objekt**.

## <a name="ignore-recommendations"></a>Ignorera rekommendationer

Om du har synpunkter som du vill ignorera kan du skapa en textfil som Log Analytics använder för att förhindra rekommendationer visas i din utvärdering av resultaten.

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Att identifiera rekommendationer som du vill ignorera
1. I Azure-portalen på sidan Log Analytics-arbetsyta för din valda arbetsyta klickar du på den **Loggsökning** menyalternativ.
2. Använd följande fråga för att lista över rekommendationer som har misslyckats för datorer i din miljö.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Om din arbetsyta har uppgraderats till den [nya Log Analytics-frågespråket](../../azure-monitor/log-query/log-query-overview.md), och sedan frågan ovan skulle ändras till följande.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Här är en skärmbild som visar loggsökningsfrågan:<br><br> ![loggsökning](./media/scom-assessment/scom-log-search.png)<br>

3. Välj rekommendationer som du vill ignorera. Du använder värdena för RecommendationId i nästa procedur.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Du skapar och använder en IgnoreRecommendations.txt textfil

1. Skapa en fil med namnet IgnoreRecommendations.txt.
2. Klistra in eller ange varje RecommendationId för varje rekommendation som du vill Log Analytics för att ignorera på separata rader och sedan spara och stäng filen.
3. Placera filen i följande mapp på varje dator där du vill att Log Analytics för att ignorera rekommendationer.
4. På hanteringsservern för Operations Manager - *SystemDrive*: \Program\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Kontrollera att rekommendationer ignoreras

1. I nästa schemalagda utvärdering körs som standard var sjunde dag, de angivna rekommendationerna markeras ignoreras och visas inte kontrollera instrumentpanelen för.
2. Du kan använda följande Loggsökning frågor för att lista alla ignorerade rekommendationer.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Om din arbetsyta har uppgraderats till den [nya Log Analytics-frågespråket](../../azure-monitor/log-query/log-query-overview.md), och sedan frågan ovan skulle ändras till följande.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Om du senare bestämmer att du vill ta bort IgnoreRecommendations.txt filer om du vill visa ignorerade rekommendationer, eller du kan ta bort RecommendationIDs från dem.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>Lösningen för System Center Operations Manager-hälsokontroll vanliga frågor och svar

*Jag har lagt till lösningen för hälsokontroll till på min Log Analytics-arbetsyta. Men jag ser inte rekommendationerna. Varför inte?* Använd följande steg vy rekommendationer när du lägger till lösningen på Log Analytics-instrumentpanelen.  

- [Ange kör som-kontot för System Center Operations Manager hälsokontroll](#operations-manager-run-as-accounts-for-log-analytics)  
- [Konfigurera System Center Operations Manager Health Check-regel](#configure-the-health-check-rule)


*Finns det ett sätt att konfigurera hur ofta kontrollen körs?* Ja. Se [ange kör ofta](#configure-the-run-frequency).

*Om en annan server identifieras när jag har lagt till System Center Operations Manager Assessment-lösningen, kommer det kontrolleras?* Ja, efter identifiering som den är markerad kommer som standard var sjunde dag.

*Vad är namnet på processen som gör datainsamlingen?* AdvisorAssessment.exe

*Där fungerar AdvisorAssessment.exe processen?* AdvisorAssessment.exe körs under HealthService-processen på hanteringsservern där kontrollen hälsoregeln är aktiverat. Med den här processen, uppnås identifiering av hela miljön via remote datainsamling.

*Hur lång tid tar det för insamling av data?* Insamling av data på servern tar ungefär en timme. Det kan ta längre tid i miljöer som har många instanser för Operations Manager eller databaser.

*Vad händer om jag ange intervallet för utvärderingen till mindre än 1 440 minuter?* Utvärderingen är förinställd på att köras på högst en gång per dag. Om du åsidosätter intervallvärdet till ett värde mindre än 1 440 minuter använder utvärderingen 1 440 minuter som värdet för intervallet.

*Hur vet jag om det finns nödvändiga fel?* Om hälsokontrollen kördes och du inte ser resultaten, är det troligt att vissa av de nödvändiga förutsättningarna för kontrollen misslyckades. Du kan köra frågor: `Operation Solution=SCOMAssessment` och `SCOMAssessmentRecommendation FocusArea=Prerequisites` i Loggsökning för att se förutsättningarna som misslyckades.

*Det finns en `Failed to connect to the SQL Instance (….).` meddelande i nödvändiga fel. Vad är problemet?* AdvisorAssessment.exe, processen som samlar in data, körs under HealthService-processen på hanteringsservern. Som en del av hälsokontrollen, processen som försöker ansluta till SQL Server där Operations Manager-databasen finns. Det här felet kan inträffa när brandväggsregler som blockerar anslutningen till SQL Server-instansen.

*Vilken typ av data som samlas in?* Följande typer av data har samlats in: - data från WMI - registret data - EventLog-data – Operations Manager-data via Windows PowerShell, SQL-frågor och filen information insamlaren.

*Varför måste jag konfigurera en Kör som-konto?* Med Operations Manager kan körs olika SQL-frågor. I ordning att köra, måste du använda ett kör som-konto med tillräcklig behörighet. Dessutom krävs behörighet som lokal administratör för att fråga WMI.

*Varför visas endast de översta 10 rekommendationerna?* Istället för att ge dig en fullständig, överväldigande lista över aktiviteter, rekommenderar vi att du kan fokusera på adressering prioriterade rekommendationer först. När du har gått dem blir ytterligare rekommendationer tillgängliga. Om du vill se en detaljerad lista kan visa du alla rekommendationer med hjälp av Loggsökning.

*Finns det ett sätt att ignorera en rekommendation?* Ja, se den [Ignorera rekommendationer](#Ignore-recommendations).


## <a name="next-steps"></a>Nästa steg

- [Söka loggarna](../../azure-monitor/log-query/log-query-overview.md) och lär dig att analysera detaljerad information för System Center Operations Manager hälsokontrollen och rekommendationer.
