---
title: Optimera din Active Directory-miljö med Azure Monitor | Microsoft-dokument
description: Du kan använda Active Directory Health Check-lösningen för att bedöma riskerna och hälsan för dina miljöer med jämna mellanrum.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2019
ms.openlocfilehash: 57c474c8391168702154b71e0c454253ab921dc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667235"
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-azure-monitor"></a>Optimera din Active Directory-miljö med Active Directory-hälsokontrollösningen i Azure Monitor

![SYMBOL FÖR AD-hälsokontroll](./media/ad-assessment/ad-assessment-symbol.png)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Du kan använda Active Directory Health Check-lösningen för att regelbundet bedöma risken och hälsan för servermiljöer. Den här artikeln hjälper dig att installera och använda lösningen så att du kan vidta korrigerande åtgärder för potentiella problem.

Den här lösningen innehåller en prioriterad lista med rekommendationer som är specifika för din distribuerade serverinfrastruktur. Rekommendationerna kategoriseras över fyra fokusområden, vilket hjälper dig att snabbt förstå risken och vidta åtgärder.

Rekommendationerna baseras på den kunskap och erfarenhet som Microsofts tekniker har fått från tusentals kundbesök. Varje rekommendation ger vägledning om varför ett problem kan vara viktigt för dig och hur du implementerar de föreslagna ändringarna.

Du kan välja fokusområden som är viktigast för din organisation och följa dina framsteg mot att driva en riskfri och hälsosam miljö.

När du har lagt till lösningen och en kontroll är klar visas sammanfattande information för fokusområden på instrumentpanelen **AD Health Check** för infrastrukturen i din miljö. I följande avsnitt beskrivs hur du använder informationen på instrumentpanelen **AD Health Check,** där du kan visa och sedan vidta rekommenderade åtgärder för Active Directory-serverinfrastrukturen.  

![bild på panelen AD-hälsokontroll](./media/ad-assessment/ad-healthcheck-summary-tile.png)

![bild av instrumentpanelen AD Health Check](./media/ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Krav

* Active Directory Health Check-lösningen kräver en version av .NET Framework 4.6.2 eller senare installerad på varje dator som har Log Analytics-agenten för Windows (kallas även MICROSOFT Monitoring Agent (MMA)) installerad.  Agenten används av System Center 2016 - Operations Manager, Operations Manager 2012 R2 och Azure Monitor.
* Lösningen stöder domänkontrollanter som kör Windows Server 2008 och 2008 R2, Windows Server 2012 och 2012 R2 och Windows Server 2016.
* En Log Analytics-arbetsyta för att lägga till Active Directory Health Check-lösningen från Azure-marknadsplatsen i Azure-portalen. Det krävs ingen ytterligare konfiguration.

  > [!NOTE]
  > När du har lagt till lösningen läggs filen AdvisorAssessment.exe till på servrar med agenter. Konfigurationsdata läss och skickas sedan till Azure Monitor i molnet för bearbetning. Logik tillämpas på mottagna data och molntjänsten registrerar data.
  >
  >

För att utföra hälsokontrollen mot domänkontrollanterna som är medlemmar i domänen som ska utvärderas kräver varje domänkontrollant i den domänen en agent och anslutning till Azure Monitor med någon av följande metoder som stöds:

1. Installera [Log Analytics-agenten för Windows](../../azure-monitor/platform/agent-windows.md) om domänkontrollanten inte redan övervakas av System Center 2016 - Operations Manager eller Operations Manager 2012 R2.
2. Om den övervakas med System Center 2016 - Operations Manager eller Operations Manager 2012 R2 och hanteringsgruppen inte är integrerad med Azure Monitor, kan domänkontrollanten vara flera hem med Azure Monitor för att samla in data och vidarebefordra till tjänsten och ändå övervakas av Operations Manager.  
3. Annars, om din Operations Manager-hanteringsgrupp är integrerad med tjänsten, måste du lägga till domänkontrollanterna för datainsamling av tjänsten enligt stegen under [Lägg till agenthanterade datorer](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor) när du har aktiverat lösningen på arbetsytan.  

Agenten på domänkontrollanten som rapporterar till en Hanteringsgrupp för Operations Manager, samlar in data, vidarebefordrar till den tilldelade hanteringsservern och sedan skickas direkt från en hanteringsserver till Azure Monitor.  Data skrivs inte till Operations Manager-databaserna.  

## <a name="active-directory-health-check-data-collection-details"></a>Information om datainsamling i Active Directory-hälsokontroll

Active Directory Health Check samlar in data från följande källor med hjälp av agenten som du har aktiverat:

- Register
- LDAP
- .NET Framework
- Händelseloggen
- Active Directory Service-gränssnitt (ADSI)
- Windows PowerShell
- Fildata
- Windows Management Instrumentation (WMI)
- API för DCDIAG-verktyg
- NTFRS-API (File Replication Service)
- Anpassad C#-kod

Data samlas in på domänkontrollanten och vidarebefordras till Azure Monitor var sjunde dag.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Förstå hur rekommendationer prioriteras

Varje rekommendation ges ett viktningsvärde som identifierar rekommendationens relativa betydelse. Endast de tio viktigaste rekommendationerna visas.

### <a name="how-weights-are-calculated"></a>Hur vikter beräknas

Viktningar är aggregerade värden baserade på tre nyckelfaktorer:

* *Sannolikheten för* att ett problem som identifierats orsakar problem. En högre sannolikhet motsvarar en större totalpoäng för rekommendationen.
* *Problemets inverkan* på din organisation om det orsakar ett problem. En högre effekt motsvarar en större totalpoäng för rekommendationen.
* De *ansträngningar* som krävs för att genomföra rekommendationen. En högre insats motsvarar en mindre totalpoäng för rekommendationen.

Viktningen för varje rekommendation uttrycks som en procentandel av den totala poängen som är tillgänglig för varje fokusområde. Om till exempel en rekommendation i fokusområdet Säkerhet och efterlevnad har poängen 5 % ökar implementeringen av rekommendationen din totala säkerhets- och efterlevnadspoäng med 5 %.

### <a name="focus-areas"></a>Fokusområden

**Säkerhet och efterlevnad** – Det här fokusområdet visar rekommendationer för potentiella säkerhetshot och överträdelser, företagspolicyer och tekniska, rättsliga och regulatoriska efterlevnadskrav.

**Tillgänglighet och affärskontinuitet** – Det här fokusområdet visar rekommendationer för tjänstens tillgänglighet, infrastrukturens återhämtning och ditt affärsskydd.

**Prestanda och skalbarhet** – Det här fokusområdet visar rekommendationer som hjälper organisationens IT-infrastruktur att växa, se till att it-miljön uppfyller aktuella prestandakrav och kan svara på förändrade infrastrukturbehov.

**Uppgradera, migrera och distribuera** – Det här fokusområdet visar rekommendationer som hjälper dig att uppgradera, migrera och distribuera Active Directory till din befintliga infrastruktur.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Bör du sikta på 100 % inom varje fokusområde?

Inte nödvändigtvis. Rekommendationerna baseras på den kunskap och de erfarenheter som Microsofts tekniker har fått vid tusentals kundbesök. Inga två serverinfrastrukturer är dock desamma och specifika rekommendationer kan vara mer eller mindre relevanta för dig. Vissa säkerhetsrekommendationer kan till exempel vara mindre relevanta om dina virtuella datorer inte exponeras för Internet. Vissa tillgänglighetsrekommendationer kan vara mindre relevanta för tjänster som tillhandahåller ad hoc-datainsamling och rapportering med låg prioritet. Frågor som är viktiga för ett moget företag kan vara mindre viktiga för ett nystartigt företag. Du kanske vill identifiera vilka fokusområden som är dina prioriteringar och sedan titta på hur dina poäng förändras med tiden.

Varje rekommendation innehåller vägledning om varför det är viktigt. Du bör använda den här vägledningen för att utvärdera om det är lämpligt för dig att implementera rekommendationen, med tanke på vilken typ av IT-tjänster du har och din organisations affärsbehov.

## <a name="use-health-check-focus-area-recommendations"></a>Använd rekommendationer för fokusområden för hälsokontroll

När den har installerats kan du visa sammanfattningen av rekommendationer med hjälp av hälsokontrollpanelen på lösningssidan i Azure-portalen.

Visa de sammanfattade efterlevnadsbedömningarna för infrastrukturen och sedan detaljgranska rekommendationer.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Så här visar du rekommendationer för ett fokusområde och vidtar korrigerande åtgärder

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

1. Klicka på panelen **Hälsokontroll av Active Directory** på sidan **Översikt.**

2. På sidan **Hälsokontroll** granskar du sammanfattningsinformationen i ett av fokusområdesbladen och klickar sedan på en för att visa rekommendationer för det fokusområdet.

3. På någon av fokusområdenas sidor kan du visa de prioriterade rekommendationerna för din miljö. Klicka på en rekommendation under **Berörda objekt** om du vill visa information om varför rekommendationen görs.

    ![bild av rekommendationer för hälsokontroll](./media/ad-assessment/ad-healthcheck-dashboard-02.png)

4. Du kan vidta korrigerande åtgärder som **föreslås**i Föreslagna åtgärder . När objektet har adresserats, senare utvärderingar poster som rekommenderade åtgärder vidtogs och din efterlevnad poäng kommer att öka. Korrigerade objekt visas som **Skickade objekt**.

## <a name="ignore-recommendations"></a>Ignorera rekommendationer

Om du har rekommendationer som du vill ignorera kan du skapa en textfil som Azure Monitor använder för att förhindra att rekommendationer visas i dina utvärderingsresultat.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Så här identifierar du rekommendationer som du ignorerar

[!INCLUDE [azure-monitor-log-queries](../../../includes/azure-monitor-log-queries.md)]

Använd följande fråga för att lista rekommendationer som har misslyckats för datorer i din miljö.

```
ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
```

Här är en skärmdump som visar loggfrågan:<

![misslyckade rekommendationer](media/ad-assessment/ad-failed-recommendations.png)

Välj rekommendationer som du vill ignorera. Du ska använda värdena för RecommendationId i nästa procedur.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Så här skapar och använder du en IgnoreRecommendations.txt-textfil

1. Skapa en fil med namnet IgnoreRecommendations.txt.

2. Klistra in eller skriv varje RecommendationId för varje rekommendation som du vill att Azure Monitor ska ignorera på en separat rad och sedan spara och stänga filen.

3. Placera filen i följande mapp på varje dator där du vill att Azure Monitor ska ignorera rekommendationer.

   * På datorer med Microsoft Monitoring Agent (ansluten direkt eller via Operations Manager) - *SystemDrive*:\Program\Microsoft Monitoring Agent\Agent
   * På hanteringsservern För OperationsHanteraren 2012 R2 - *SystemDrive*:\Program\Microsoft System Center 2012 R2\Operations Manager\Server
   * På hanteringsservern för Operations Manager 2016 – *SystemDrive*:\Program\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Så här kontrollerar du att rekommendationerna ignoreras

När nästa schemalagda hälsokontroll körs *markeras* som standard ignorerade och visas inte på instrumentpanelen som standard.

1. Du kan använda följande loggfrågor för att lista alla ignorerade rekommendationer.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Om du senare bestämmer dig för att du vill se ignorerade rekommendationer tar du bort alla IgnoreRecommendations.txt-filer eller så kan du ta bort Rekommendations-ID:er från dem.

## <a name="ad-health-check-solutions-faq"></a>Vanliga frågor och svar om AD-hälsokontrollösningar

*Vilka kontroller utförs av AD Assessment-lösningen?*

* Följande fråga visar en beskrivning av alla kontroller som för närvarande utförs:

```Kusto
ADAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
Resultaten kan sedan exporteras till Excel för vidare granskning.

*Hur ofta körs en hälsokontroll?*

* Checken går var sjunde dag.

*Finns det något sätt att konfigurera hur ofta hälsokontrollen körs?*

* Inte just nu.

*Om en annan server för upptäcks efter att jag har lagt till en lösning för hälsokontroll, kommer det att kontrolleras*

* Ja, när det upptäcks det kontrolleras från och med då, var sjunde dag.

*Om en server inaktiveras, när tas den bort från hälsokontrollen?*

* Om en server inte skickar data på tre veckor tas den bort.

*Vad är namnet på den process som gör datainsamlingen?*

* RådgivareAssessment.exe

*Hur lång tid tar det innan data samlas in?*

* Den faktiska datainsamlingen på servern tar cirka 1 timme. Det kan ta längre tid på servrar som har ett stort antal Active Directory-servrar.

*Finns det något sätt att konfigurera när data samlas in?*

* Inte just nu.

*Varför visa bara de 10 rekommendationer?*

* I stället för att ge dig en uttömmande överväldigande lista över uppgifter rekommenderar vi att du fokuserar på att ta itu med de prioriterade rekommendationerna först. När du har åtgärdat dem blir ytterligare rekommendationer tillgängliga. Om du föredrar att se den detaljerade listan kan du visa alla rekommendationer med hjälp av en loggfråga.

*Finns det något sätt att ignorera en rekommendation?*

* Ja, se [Ignorera rekommendationer](#ignore-recommendations) ovan.

## <a name="next-steps"></a>Nästa steg

Använd [Azure Monitor-loggfrågor](../log-query/log-query-overview.md) för att lära dig hur du analyserar detaljerade AD Health Check-data och rekommendationer.
