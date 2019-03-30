---
title: Optimera din SQL Server-miljö med Azure Monitor | Microsoft Docs
description: Med Azure Monitor kan använda du SQL-hälsokontroll-lösningen för att utvärdera risker och bedöm hälsotillståndet i dina miljöer med regelbundna intervall.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: e297eb57-1718-4cfe-a241-b9e84b2c42ac
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: magoedte
ms.openlocfilehash: 94b23bc29c3c986e6a0cd74e0805b5d47ce35849
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58629126"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-azure-monitor"></a>Optimera din SQL-miljö med lösningen för hälsokontroll för SQL Server i Azure Monitor

![SQL-hälsokontroll symbol](./media/sql-assessment/sql-assessment-symbol.png)

Du kan använda SQL-hälsokontroll-lösningen för att utvärdera risker och bedöm hälsotillståndet i servermiljöer med regelbundna intervall. Den här artikeln beskriver hur du installerar lösningen så att du kan vidta åtgärder för potentiella problem.

Den här lösningen ger en prioriterad lista över rekommendationer som är specifika för din distribuerade serverinfrastruktur. Rekommendationerna kategoriseras i sex fokusområden som hjälper dig att snabbt förstå risken och vidta åtgärder.

Rekommendationerna baseras på kunskap och erfarenhet av Microsoft-tekniker från tusentals kunder besök. Varje rekommendation ger vägledning om varför ett problem kan är viktiga för dig och hur du implementerar de föreslagna ändringarna.

Du kan välja fokusområden som är viktigast för din organisation och spåra förloppet mot att köra en risk kostnadsfria och hälsosam miljö.

När du har lagt till lösningen och en utvärdering är klar, sammanfattande informationen för fokusområden visas på den **SQL-hälsokontroll** instrumentpanel för infrastrukturen i din miljö. I följande avsnitt beskrivs hur du använder informationen på den **SQL-hälsokontroll** instrumentpanelen där du kan visa och sedan vidta rekommenderade åtgärder för din SQL Server-infrastruktur.

![Bild av SQL-hälsokontroll panel](./media/sql-assessment/sql-healthcheck-summary-tile.png)

![Bild av SQL-hälsokontroll instrumentpanelen](./media/sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Förutsättningar

* Hälsokontrollen för SQL-lösning kräver en version som stöds av .NET Framework 4 vara installerat på varje dator som har den Microsoft Monitoring Agent (MMA) installerat.  MMA-agenten används av System Center 2016 – Operations Manager och Operations Manager 2012 R2 och Azure Monitor.  
* Lösningen har stöd för SQL Server version 2012, 2014 och 2016.
* En Log Analytics-arbetsyta för att lägga till den SQL-hälsokontroll lösningen från Azure marketplace i Azure-portalen.  För att kunna installera lösningen måste du vara administratör eller bidragsgivare i Azure-prenumeration.

  > [!NOTE]
  > När du har lagt till lösningen, läggs filen AdvisorAssessment.exe till servrar med agenter. Konfigurationsdata läsa och sedan skickas till Azure Monitor i molnet för bearbetning. Logiken tillämpas på den mottagna data och Molntjänsten innehåller data.
  >
  >

Om du vill utföra hälsokontroll mot din SQL Server-servrar, kräver de en agent och en anslutning till Azure Monitor med någon av följande metoder:

1. Installera den [Microsoft Monitoring Agent (MMA)](../../azure-monitor/platform/agent-windows.md) om servern inte är redan övervakas av System Center 2016 – Operations Manager eller Operations Manager 2012 R2.
2. Om den är övervakad med System Center 2016 – Operations Manager eller Operations Manager 2012 R2 och hanteringsgruppen är inte integrerat med Azure Monitor, kan servern ha flera värdar med Log Analytics för att samla in data och vidarebefordra till tjänsten och fortfarande vara övervakas av Operations Manager.  
3. I annat fall om Operations Manager-hanteringsgrupp är integrerad med tjänsten, du måste lägga till domänkontrollanterna för insamling av tjänsten anvisningarna under [lägga till datorer som hanteras med agent](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor) när du har aktiverat lösningen i din arbetsyta.  

Agenten på vilka rapporter till en Operations Manager-hanteringsgrupp, samlar in data, SQL-servern vidarebefordrar till dess tilldelade hanteringsserver och sedan skickas direkt från en hanteringsserver till Azure Monitor.  Data skrivs inte till Operations Manager-databaserna.  

Om SQL Server övervakas av Operations Manager, måste du konfigurera en Operations Manager kör som-konto. Se [Operations Manager kör som-konton för Azure Monitor](#operations-manager-run-as-accounts-for-log-analytics) nedan för mer information.

## <a name="sql-health-check-data-collection-details"></a>SQL-hälsokontroll data samling information
SQL-hälsokontroll samlar in data från följande källor med hjälp av agent som du har aktiverat:

* Windows Management Instrumentation (WMI)
* Register
* Prestandaräknare
* SQL Server dynamic management visa resultat

Data som samlas in på SQL Server och vidarebefordras till logganalys var sjunde dag.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Operations Manager kör som-konton för Log Analytics
Log Analytics använder gruppen Operations Manager agent och en hanteringsserver för att samla in och skicka data till Log Analytics-tjänsten. Log Analytics bygger på hanteringspaket för arbetsbelastningar att ge mervärde tjänster. Varje arbetsbelastning kräver belastningsspecifikt behörighet att köra hanteringspaket i en säkerhetskontext, till exempel ett domänanvändarkonto. Du måste ange autentiseringsuppgifter genom att konfigurera en Operations Manager kör som-konto.

Använd följande information för att ställa in Operations Manager kör som-kontot för SQL-hälsokontroll.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Ange kör som-kontot för SQL-hälsokontroll
 Om du redan använder SQL Server-hanteringspaketet, bör du använda den kör som-konfigurationen.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Konfigurera SQL kör som-kontot i Operations-konsolen
> [!NOTE]
> Som standard arbetsflöden i management pack som körs i säkerhetskontexten för kontot Lokalt System. Om du använder Microsoft Monitoring Agent är direkt anslutna till tjänsten i stället rapporter direkt till en Operations Manager-hanteringsgrupp, hoppa över steg 1 till 5 nedan och köra T-SQL eller PowerShell-exempel, ange NT AUTHORITY\SYSTEM som den användarnamn.
>
>

1. Öppna driftkonsolen i Operations Manager, och klicka sedan på **Administration**.
2. Under **kör som-konfiguration**, klickar du på **profiler**, och öppna **SQL utvärdering av kör som-profil**.
3. På den **kör som-konton** klickar du på **Lägg till**.
4. Välj en Windows kör som-konto som innehåller de autentiseringsuppgifter som krävs för SQL Server, eller klicka på **New** att skapa en.

   > [!NOTE]
   > Kör som-kontotyp måste vara Windows. Kör som-kontot måste också ingå i gruppen lokala administratörer på alla Windows-servrar som är värd för SQL Server-instanser.
   >
   >
5. Klicka på **Spara**.
6. Ändra och kör följande T-SQL-exempel på varje SQL Server-instans för att tilldela minsta möjliga behörigheter som krävs för Kör som-kontot för hälsokontrollen. Dock behöver du inte göra det om ett kör som-konto redan är en del av serverrollen sysadmin på SQL Server-instanser.

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

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Konfigurera SQL kör som-kontot med hjälp av Windows PowerShell
Öppna ett PowerShell-fönster och kör följande skript när du har uppdaterat den med din information:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Förstå hur rekommendationer är prioriterade
Varje rekommendation ges ett värde-värde som identifierar den relativa prioriteten för rekommendationen. Bara de tio viktigaste rekommendationerna visas.

### <a name="how-weights-are-calculated"></a>Hur vikterna beräknas
Viktningar är aggregerade värden baserat på tre viktiga faktorer:

* Den *sannolikheten* ett identifierat problem medför problem. En högre sannolikhet motsvarar en större övergripande poäng för rekommendationen.
* Den *inverkan* av problemet i din organisation om det uppstår ett problem. En högre inverkan motsvarar en större övergripande poäng för rekommendationen.
* Den *arbete* krävs för att implementera rekommendationen. En högre ansträngning motsvarar en mindre övergripande poäng för rekommendationen.

Värde för varje rekommendation uttrycks som en procentandel av den sammanlagda poängen för varje fokusområde. Till exempel om en rekommendation i Fokusområde säkerhets- och har ett resultat på 5%, ökar implementera den här rekommendationen din övergripande poäng av 5% för säkerhet och efterlevnad.

### <a name="focus-areas"></a>Fokusområden
**Säkerhet och efterlevnad** – den här Fokusområde visar rekommendationer för potentiella säkerhetshot och överträdelser företagets principer och krav på teknisk, juridisk och regelmässig efterlevnad.

**Tillgänglighet och affärskontinuitet** – den här Fokusområde visar rekommendationer för tjänstens tillgänglighet, återhämtning för din infrastruktur och verksamhetsskydd.

**Prestanda och skalbarhet** – den här Fokusområde visar rekommendationer för att din organisations IT-infrastruktur växa, se till att din IT-miljö uppfyller aktuella prestandakrav och kan svara på förändrade infrastruktur behov.

**Uppgradering, migrering och distribution** – den här Fokusområde visar rekommendationer som hjälper dig att uppgradera, migrera och distribuera SQL Server till din befintliga infrastruktur.

**Åtgärder och övervakning** – den här Fokusområde visar rekommendationer för att effektivisera din IT-verksamhet, implementera förebyggande underhåll och maximera prestanda.

**Ändrings- och konfigurationshantering** – den här Fokusområde visar rekommendationer för att skydda dagliga uppgifter, se till att ändringar inte negativt påverkar din infrastruktur, upprätta procedurer för ändringskontroll, och för att spåra och granska systemkonfigurationer.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Bör du försöka poäng 100% i varje Fokusområde?
Inte nödvändigtvis. Rekommendationerna baseras på de kunskaper och erfarenheter som gjorts av Microsofts tekniker till tusentals kunder besök. Men ingen två server-infrastrukturer är desamma och specifika rekommendationer kan vara mer eller mindre relevant för dig. Exempelvis kanske vissa säkerhetsrekommendationer mindre relevant om dina virtuella datorer inte exponeras för Internet. Vissa rekommendationer för tillgänglighet kan vara mindre användbart för tjänster som erbjuder låg prioritet ad hoc-insamling och rapportering. Problem som är viktiga för en mogen affärsverksamhet som kan vara mindre viktiga för en startfil. Du kanske vill identifiera vilka fokusområden som är dina prioriteringar och titta på hur ditt resultat ändras med tiden.

Varje rekommendation innehåller information om varför det är viktigt. Du bör använda den här vägledningen för att utvärdera om implementerar rekommendationen är lämplig för dig, baserat på typen av dina IT-tjänster och affärsbehoven för din organisation.

## <a name="use-health-check-focus-area-recommendations"></a>Använd rekommendationer för hälsokontroll fokus-området
Innan du kan använda en lösning i Azure Monitor, måste du ha installerat lösningen.  När den har installerats, du kan visa en sammanfattning av rekommendationer med hjälp av SQL-hälsokontroll panelen på den **översikt** sidan för Azure Monitor i Azure-portalen.

Visa de sammanfattade efterlevnad utvärderingarna för din infrastruktur och sedan gå till rekommendationer.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Visa rekommendationer för en Fokusområde och vidta åtgärder
1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. I Azure Portal klickar du på knappen **Fler tjänster** längst upp till vänster. I listan över resurser skriver du **Monitor**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Monitor**.
3. I den **Insights** avsnittet i menyn och välj **mer**.  
4. På den **översikt** klickar du på den **SQL-hälsokontroll** panelen.
5. På den **hälsokontrollen** granskar den sammanfattande informationen i något av bladen fokus område och klicka sedan på ett om du vill visa rekommendationer för den fokusområde.
6. På någon av sidorna fokus området, kan du visa prioriterade rekommendationer för din miljö. Klicka på en rekommendation under **påverkade objekt** att visa information om varför rekommendationen görs.<br><br> ![Bild av SQL-hälsokontroll rekommendationer](./media/sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. Du kan vidta korrigerande åtgärder som föreslås i **föreslagna åtgärder**. När objektet har utförts, registrera senare utvärderingar som rekommenderar åtgärder som utförts och din kompatibilitetspoäng ökar. Korrigerad objekt visas som **skickas objekt**.

## <a name="ignore-recommendations"></a>Ignorera rekommendationer
Om du har synpunkter som du vill ignorera kan du skapa en textfil som Azure Monitor använder för att förhindra rekommendationer visas i din utvärdering av resultaten.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Att identifiera rekommendationer som kommer att ignoreras
1. I Azure Monitor-menyn klickar du på **loggar**.
2. Använd följande fråga för att lista över rekommendationer som har misslyckats för datorer i din miljö.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Här är en skärmbild som visar log-frågan:<br><br> ![misslyckade rekommendationer](./media/sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Välj rekommendationer som du vill ignorera. Du använder värdena för RecommendationId i nästa procedur.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Du skapar och använder en IgnoreRecommendations.txt textfil
1. Skapa en fil med namnet IgnoreRecommendations.txt.
2. Klistra in eller ange varje RecommendationId för varje rekommendation som du vill att Azure Monitor för att ignorera på separata rader och sedan spara och stäng filen.
3. Placera filen i följande mapp på varje dator där du vill att Azure Monitor för att ignorera rekommendationer.
   * På datorer med Microsoft Monitoring Agent (anslutet direkt eller via Operations Manager) - *SystemDrive*: \Program\Microsoft Monitoring Agent\Agent
   * På hanteringsservern för Operations Manager - *SystemDrive*: \Program\Microsoft System Center 2012 R2\Operations Manager\Server
   * På hanteringsservern för Operations Manager 2016 - *SystemDrive*: \Program\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Kontrollera att rekommendationer ignoreras
1. I nästa schemalagda utvärdering körs som standard var sjunde dag, de angivna rekommendationerna markeras ignoreras och visas inte på instrumentpanelen för utvärdering av.
2. Du kan använda följande Loggsökning frågor för att lista alla ignorerade rekommendationer.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
3. Om du senare bestämmer att du vill ta bort IgnoreRecommendations.txt filer om du vill visa ignorerade rekommendationer, eller du kan ta bort RecommendationIDs från dem.

## <a name="sql-health-check-solution-faq"></a>SQL-hälsokontroll lösning vanliga frågor och svar
*Hur ofta körs en hälsokontroll?*

* Kontrollen körs var sjunde dag.

*Finns det ett sätt att konfigurera hur ofta kontrollen körs?*

* Inte just nu.

*Om en annan server identifieras när jag har lagt till den SQL-hälsokontroll lösningen så kommer det kontrolleras?*

* Ja, när den identifieras de kontrolleras från sedan, var sjunde dag.

*Om en server är inaktiverad, när den tas bort från hälsokontrollen?*

* Om en server inte lämnar data för tre veckor, tas den bort.

*Vad är namnet på processen som gör datainsamlingen?*

* AdvisorAssessment.exe

*Hur lång tid tar det för de data som samlas in?*

* Den faktiska datainsamlingen på servern tar ungefär 1 timme. Det kan ta längre tid på servrar som har ett stort antal SQL-instanser eller databaser.

*Vilken typ av data som samlas in?*

* Följande typer av data har samlats in:
  * WMI
  * Register
  * Prestandaräknare
  * SQL dynamiska hanteringsvyer (DMV).

*Finns det ett sätt att konfigurera när data har samlats in?*

* Inte just nu.

*Varför måste jag konfigurera en Kör som-konto?*

* För SQL Server körs ett litet antal SQL-frågor. Ett kör som-konto med åtkomstbehörighet för VIEW SERVER STATE SQL måste användas i ordning att köra.  Dessutom för att fråga WMI krävs lokal administratörsbehörighet.

*Varför visas endast de översta 10 rekommendationerna?*

* Istället för att ge dig en fullständig överväldigande förteckning av uppgifter, rekommenderar vi att du kan fokusera på adressering prioriterade rekommendationer först. När du har gått dem blir ytterligare rekommendationer tillgängliga. Om du vill se en detaljerad lista kan visa du alla rekommendationer med hjälp av Log Analytics-loggsökning.

*Finns det ett sätt att ignorera en rekommendation?*

* Ja, se [Ignorera rekommendationer](#ignore-recommendations) ovan.

## <a name="next-steps"></a>Nästa steg
* [Loggar frågor](../log-query/log-query-overview.md) att lära dig hur du analyserar detaljerade SQL-hälsokontroll data och rekommendationer.
