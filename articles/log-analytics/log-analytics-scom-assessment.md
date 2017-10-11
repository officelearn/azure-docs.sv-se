---
title: "Optimera din miljö för System Center Operations Manager med Azure Log Analytics | Microsoft Docs"
description: "Du kan använda System Center Operations Manager Assessment-lösning för att bedöma risker och hälsotillståndet för server-miljöer med regelbundna intervall."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: tysonn
ms.assetid: 49aad8b1-3e05-4588-956c-6fdd7715cda1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/11/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4992d98397da409f7c1cfbdeb40fdb0cdd0d2f19
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-assessment-preview-solution"></a>Optimera din miljö med System Center Operations Manager Assessment (förhandsgranskning)-lösning

![System Center Operations Manager Assessment symbol](./media/log-analytics-scom-assessment/scom-assessment-symbol.png)

Du kan använda System Center Operations Manager Assessment-lösning för att bedöma risken och hälsotillståndet för ditt System Center Operations Manager server-miljöer med regelbundna intervall. Den här artikeln hjälper dig att installera, konfigurera och använda lösningen så att du kan vidta åtgärder för möjliga problem.

Den här lösningen ger en prioriterad lista med rekommendationer som är specifika för din distribuerade serverinfrastruktur. Rekommendationerna kategoriseras i fyra fokusområden, som hjälper dig att snabbt förstå risken och vidta åtgärder.

Rekommendationer baserat på kunskap och erfarenhet av Microsoft-tekniker från tusentals kunden besök. Varje rekommendation innehåller information om varför ett problem kan verkligen är viktiga och hur du implementerar de föreslagna ändringarna.

Du kan välja fokusområden som är viktigast för din organisation och spåra din framsteg mot kör en risk ledigt och hälsosam miljö.

När du har lagt till lösningen och en bedömning är slutförd, Sammanfattning visas information för fokusområden på den **System Center Operations Manager Assessment** instrumentpanel för din infrastruktur. I följande avsnitt beskrivs hur du använder informationen på den **System Center Operations Manager Assessment** instrumentpanel, där du kan visa och sedan vidta rekommenderade åtgärder för din SCOM-infrastruktur.

![System Center Operations Manager-lösningen sida vid sida](./media/log-analytics-scom-assessment/scom-tile.png)

![System Center Operations Manager Assessment instrumentpanelen](./media/log-analytics-scom-assessment/scom-dashboard01.png)

## <a name="installing-and-configuring-the-solution"></a>Installera och konfigurera lösningen

Lösningen fungerar med Microsoft System Operations Manager 2012 R2 och 2012 SP1.

Använd följande information för att installera och konfigurera lösningen.

 - Innan du kan använda en lösning i OMS måste ha installerat lösningen. Installera lösning från [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview) eller genom att följa instruktionerna i [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md).

 - När du lägger till lösningen till arbetsytan visas bedömning av System Center Operations Manager-panelen på instrumentpanelen ytterligare konfiguration krävs. Klicka på panelen och följ konfigurationsstegen anges på sidan

 ![System Center Operations Manager-instrumentpanelen](./media/log-analytics-scom-assessment/scom-configrequired-tile.png)

 Konfiguration av System Center Operations Manager kan göras via skriptet genom att följa anvisningarna i konfigurationssidan för lösningen i OMS.

 Följ i stället för att konfigurera assessment via SCOM-konsolen, de nedanstående steg i samma ordning
1. [Ange kör som-kontot för System Center Operations Manager](#operations-manager-run-as-accounts-for-oms)  
2. [Konfigurera System Center Operations Manager Assessment-regel](#configure-the-assessment-rule)

## <a name="system-center-operations-manager-assessment-data-collection-details"></a>System Center Operations Manager assessment data samling information

System Center Operations Manager-bedömning samlar in WMI-data, registerdata, EventLog data, Operations Manager data via Windows PowerShell, SQL-frågor, filen information insamlaren med den server som du har aktiverat.

I följande tabell visas data collection metoder för System Center Operations Manager, och hur ofta data samlas in av en agent.

| Plattform | Styr Agent | SCOM-agent | Azure Storage | SCOM krävs? | SCOM-agent data som skickas via management-grupp | Frekvens för samlingen |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | | | | &#8226; | | sju dagar |

## <a name="operations-manager-run-as-accounts-for-oms"></a>Operations Manager kör som-konton för OMS

OMS bygger på hanteringspaket för arbetsbelastningar att ge mervärde tjänster. Varje arbetsbelastning kräver belastningsspecifikt behörighet att köra hanteringspaket i en annan säkerhetskontext, till exempel ett domänkonto. Konfigurera en Operations Manager kör som-konto för att tillhandahålla autentiseringsuppgifter.

Använd följande information för att ange Operations Manager kör som-konto för System Center Operations Manager.

### <a name="set-the-run-as-account"></a>Ange kör som-konto

1. I Operations Manager-konsolen går du till den **Administration** fliken.
2. Under den **kör som-konfiguration**, klickar du på **konton**.
3. Skapa kör som-kontot, följande via guiden skapar ett windowskonto. Kontot som ska användas är den som identifierats och att alla nödvändiga komponenter nedan:

    >[!NOTE]
    Kör som-kontot måste uppfylla följande krav:
    - En domänmedlem konto i den lokala gruppen Administratörer på alla servrar i miljön (alla Operations Manager-roller - hanteringsservern, OpsMgr-databasen, datalagret, rapportering, webbkonsolen, Gateway)
    - Åtgärden Managers administratörsroll för hanteringsgruppen som ska utvärderas
    - Köra den [skriptet](#sql-script-to-grant-granular-permissions-to-the-run-as-account) att ge detaljerade behörigheter till kontot för SQL-instans som används av Operations Manager.
      Obs: Om det här kontot har redan sysadmin-behörighet, går vidare körningen av skriptet.

4. Under **Distributionssäkerhet**väljer **säkrare**.
5. Ange den hanteringsserver där kontot är distribuerade.
3. Gå tillbaka till det kör som-konfiguration och klicka på **profiler**.
4. Sök efter den *SCOM Assessment profil*.
5. Namnet på profilen bör vara: *Microsoft System Center Advisor SCOM Assessment kör som-profilen*.
6. Högerklicka på och uppdatera dess egenskaper och lägga till nyligen har skapat kör som-kontot du skapade i steg 3.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>SQL-skript för att ge detaljerade behörigheter till Kör som-konto

Kör följande SQL-skript för att bevilja behörighet till Kör som-konto för SQL-instans som används av Operations Manager.

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


### <a name="configure-the-assessment-rule"></a>Konfigurera assessment-regel

Management pack för System Center Operations Manager Assessment-lösning innehåller en regel med namnet *Microsoft System Center Advisor SCOM Assessment kör Assessment regeln*. Den här regeln är ansvarig för att köra bedömningen. Använd procedurerna nedan om du vill aktivera regeln och ange hur ofta.

Microsoft System Center Advisor SCOM Assessment kör Assessment regeln är inaktiverad som standard. Om du vill köra bedömningen, måste du aktivera regeln på en hanteringsserver. Använd följande steg.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Aktivera regeln för en viss hanteringsserver

1. I den **redigering** arbetsytan i Operations Manager-konsolen, Sök efter regeln *Microsoft System Center Advisor SCOM Assessment kör Assessment regeln* i den **regler** fönstret.
2. I sökresultaten väljer du det alternativ som innehåller texten *typ: hanteringsservern*.
3. Högerklicka på regeln och klickar sedan på **åsidosätter** > **för ett specifikt objekt i klassen: hanteringsservern**.
4.  Välj hanteringsservern där regeln ska köras i listan tillgängliga management-servrar.
5.  Se till att du ändrar åsidosättningsvärde till **SANT** för den **aktiverad** parametervärdet.  
    ![åsidosätt parametern](./media/log-analytics-scom-assessment/rule.png)

Konfigurera hur ofta kör med nästa procedur vid fortfarande i det här fönstret.

#### <a name="configure-the-run-frequency"></a>Konfigurera kör frekvens

Bedömning är konfigurerad för att köras var 10 080 minuter (eller sju dagar) standardintervallet. Du kan åsidosätta värdet till ett minsta värde för 1440 minuter (eller en dag). Värdet som representerar minimitid mellanrummet krävs mellan på varandra följande assessment körs. Följ anvisningarna nedan om du vill åsidosätta intervallet.

1. I den **redigering** arbetsytan i Operations Manager-konsolen, Sök efter regeln *Microsoft System Center Advisor SCOM Assessment kör Assessment regeln* i den **regler** fönstret.
2. I sökresultaten väljer du det alternativ som innehåller texten *typ: hanteringsservern*.
3. Högerklicka på regeln och klickar sedan på **Åsidosätt regeln** > **för alla objekt i klassen: hanteringsservern**.
4. Ändra den **intervall** parametervärde för din önskade intervall. I exemplet nedan anges värdet till 1 440 minuter (en dag).  
    ![intervall för parametern](./media/log-analytics-scom-assessment/interval.png)  
    Om värdet anges till mindre än 1 440 minuter sedan körs regeln vid ett intervall med en dag. I det här exemplet regeln ignoreras värdet för intervallet och körs med en frekvens som en dag.


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

**Prestanda och skalbarhet** -fokus visas här rekommendationer som hjälper din organisations IT-infrastruktur växer, se till att din IT-miljö uppfyller aktuella prestandakrav och kan svara på förändrade infrastrukturbehov.

**Uppgradering och migrering distribution** -fokus visas här rekommendationer när du uppgraderar, migrera och distribuera SQL Server till den befintliga infrastrukturen.

**Åtgärder och övervakning** -fokus visas här rekommendationer för att effektivisera IT-avdelningen, implementera förebyggande underhåll och maximera prestanda.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Bör du försöka poängsätta 100% överallt fokus i?

Inte nödvändigtvis. Rekommendationerna baseras på kunskaper och erfarenheter som gjorts av Microsoft-tekniker över tusentals kunden besök. Dock inga två server-infrastrukturen är samma och specifika rekommendationer kan vara mer eller mindre relevant för dig. Exempelvis kanske vissa säkerhetsrekommendationer mindre relevant om de virtuella datorerna inte utsätts för Internet. Vissa tillgänglighet rekommendationer kan vara relevanta för tjänster som erbjuder med låg prioritet ad hoc-insamling och rapportering. Problem som är viktiga för en mogen företag kanske mindre viktiga för en Startup. Du kanske vill identifiera vilka fokusområden är dina prioriteter och titta på hur ditt resultat förändras över tid.

Varje rekommendation innehåller information om varför det är viktigt. Använd den här vägledningen för att utvärdera om implementera rekommendationen är lämplig för dig, baserat på typ av din IT-tjänster och affärsbehoven för din organisation.

## <a name="use-assessment-focus-area-recommendations"></a>Använd assessment fokus området rekommendationer

Innan du kan använda en lösning i OMS måste ha installerat lösningen. Du kan läsa mer om hur du installerar lösningar finns [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md). När den har installerats kan du visa sammanfattning av rekommendationer med hjälp av System Center Operations Manager Assessment panelen på översiktssidan i OMS.

Visa sammanfattade efterlevnad bedömningar för din infrastruktur och gå till rekommendationer.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Visa rekommendationer för en Fokusområde och vidta åtgärder

1. På den **översikt** klickar du på den **System Center Operations Manager Assessment** panelen.
2. På den **System Center Operations Manager Assessment** , Granska sammanfattningen i ett fokus området blad och klickar sedan på en om du vill visa rekommendationer för området fokus.
3. På någon av sidorna fokus område, kan du visa prioriterad rekommendationer för din miljö. Klicka på en rekommendation enligt **påverkade objekt** att visa information om varför rekommendationen görs.  
    ![Fokusområde](./media/log-analytics-scom-assessment/focus-area.png)
4. Du kan vidta åtgärder i **föreslagna åtgärder**. När objektet har behandlats registrerar senare bedömningar den rekommenderade åtgärder som utförts och kompatibilitet poängen ökar. Korrigerade objekt visas som **skickades objekt**.

## <a name="ignore-recommendations"></a>Ignorera rekommendationer

Om du har rekommendationer som du vill ignorera, kan du skapa en textfil som OMS används för att förhindra rekommendationer visas i sökresultatet assessment.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Att identifiera rekommendationer som du vill ignorera

1. Logga in på din arbetsyta och öppna loggen sökning. Använd följande fråga för att lista över rekommendationer som har misslyckats för datorer i din miljö.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Här är en skärmbild som visar frågan loggen:  
    ![loggsökning](./media/log-analytics-scom-assessment/scom-log-search.png)

2. Välj rekommendationer som du vill ignorera. Du ska använda värdena för RecommendationId i nästa procedur.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Du skapar och använder en IgnoreRecommendations.txt textfil

1. Skapa en fil med namnet IgnoreRecommendations.txt.
2. Klistra in eller ange varje RecommendationId för varje rekommendation som du vill OMS Ignorera på en separat rad och spara och stäng filen.
3. Placera filen i följande mapp på varje dator där du vill att OMS att ignorera rekommendationer.
4. På hanteringsservern för Operations Manager - *SystemDrive*: \Program\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Kontrollera att rekommendationer ignoreras

1. När nästa schemalagda utvärdering körs som standard var sjunde dag, angivna rekommendationerna markeras ignoreras och kommer inte att visas på instrumentpanelen assessment.
2. Du kan använda följande loggen sökfrågor för att lista alla rekommendationer som ignoreras.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

3. Om du senare bestämmer att du vill se ignoreras rekommendationer, ta bort IgnoreRecommendations.txt filer eller du kan ta bort RecommendationIDs från dem.

## <a name="system-center-operations-manager-assessment-solution-faq"></a>System Center Operations Manager lösning vanliga frågor och svar

*Jag har lagt till lösningen på min OMS-arbetsyta. Men du ser inte rekommendationer. Varför inte?* Använd följande steg vy rekommendationer när du lägger till lösningen på OMS-instrumentpanelen.  

- [Ange kör som-kontot för System Center Operations Manager](#operations-manager-run-as-accounts-for-oms)  
- [Konfigurera System Center Operations Manager Assessment-regel](#configure-the-assessment-rule)


*Finns det ett sätt att konfigurera hur ofta bedömning körs?* Ja. Se [konfigurera kör frekvensen](#configure-the-run-frequency).

*Om en annan server identifieras när jag har lagt till System Center Operations Manager Assessment lösningen, kommer utvärderas det?* Ja, efter identifiering, det bedöms fr.o.m--som standard var sjunde dag.

*Vad är namnet på processen som gör datainsamlingen?* AdvisorAssessment.exe

*Där körs AdvisorAssessment.exe processen?* AdvisorAssessment.exe körs under HealthService för hanteringsservern där assessment regeln är aktiverad. Med den här processen uppnås identifiering av hela miljön genom insamling av fjärråtkomst.

*Hur lång tid tar det för insamling av data?* Insamling av data på servern tar ungefär en timme. Det kan ta längre tid i miljöer som har många instanser för Operations Manager eller databaser.

*Vad händer om jag ange intervallet av bedömningen till mindre än 1 440 minuter?* Utvärderingen har förkonfigurerats så att köras på högst en gång per dag. Om du åsidosätter intervallvärdet till ett värde mindre än 1 440 minuter använder bedömningen 1440 minuter som värdet för intervallet.

*Hur vet jag om det finns fel som krävs?* Om bedömningen kördes och du inte ser resultaten, är det troligt att vissa av kraven för att bedöma misslyckades. Du kan köra frågor: `Type=Operation Solution=SCOMAssessment` och `Type=SCOMAssessmentRecommendation FocusArea=Prerequisites` i loggen Sök om du vill se misslyckade kraven.

*Det finns en `Failed to connect to the SQL Instance (….).` meddelandet i förutvärdering fel. Vad är problemet?* AdvisorAssessment.exe, processen som samlar in data, körs under HealthService för hanteringsservern. Som en del av utvärderingen, processen som försöker ansluta till SQL Server där Operations Manager-databasen finns. Det här felet kan inträffa när brandväggsregler blockera anslutningen till SQL Server-instansen.

*Vilken typ av data som samlas in?* Följande typer av data har samlats in: - WMI-data – data - EventLog-data – Operations Manager registerdata via Windows PowerShell, SQL-frågor och filen information insamlaren.

*Varför måste jag konfigurera ett kör som-konto?* Olika SQL-frågor körs för en Operations Manager-server. För att kunna köra måste du använda ett kör som-konto med tillräcklig behörighet. Dessutom krävs lokal administratörsbehörighet för att fråga WMI.

*Varför visas endast topp 10 rekommendationer?* I stället för att ge dig en fullständig, överväldigande lista över aktiviteter, rekommenderar vi att du fokusera på adressering prioriterad rekommendationerna först. När du åtgärda dem blir ytterligare rekommendationer tillgängliga. Du kan visa alla rekommendationer loggen sökning om du vill se en detaljerad lista.

*Finns det ett sätt att ignorera en rekommendation?* Ja, finns det [Ignorera rekommendationer](#Ignore-recommendations).


## <a name="next-steps"></a>Nästa steg

- [Söka i loggar](log-analytics-log-searches.md) att visa detaljerad information för System Center Operations Manager bedömning och rekommendationer.
