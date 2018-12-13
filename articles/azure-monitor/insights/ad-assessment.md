---
title: Optimera din Active Directory-miljö med Azure Log Analytics | Microsoft Docs
description: Du kan använda Active Directory Health Check-lösningen för att utvärdera risker och bedöm hälsotillståndet i dina miljöer med regelbundna intervall.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 81eb41b8-eb62-4eb2-9f7b-fde5c89c9b47
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/27/2017
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 53401745d12bb7e0e1cef843cf743fbe9cc7cfb6
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888849"
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-log-analytics"></a>Optimera din Active Directory-miljö med kontroll av Active Directory hälsotillstånd lösningen i Log Analytics

![AD-hälsokontroll symbol](./media/ad-assessment/ad-assessment-symbol.png)

Du kan använda Active Directory Health Check-lösningen för att utvärdera risker och bedöm hälsotillståndet i servermiljöer med regelbundna intervall. Den här artikeln hjälper dig att installera och använda lösningen så att du kan vidta åtgärder för potentiella problem.

Den här lösningen ger en prioriterad lista över rekommendationer som är specifika för din distribuerade serverinfrastruktur. Rekommendationerna kategoriseras i fyra fokusområden som hjälper dig att snabbt förstå risken och vidta åtgärder.

Rekommendationerna baseras på kunskap och erfarenhet av Microsoft-tekniker från tusentals kunder besök. Varje rekommendation ger vägledning om varför ett problem kan är viktiga för dig och hur du implementerar de föreslagna ändringarna.

Du kan välja fokusområden som är viktigast för din organisation och spåra förloppet mot att köra en risk kostnadsfria och hälsosam miljö.

När du har lagt till lösningen och en kontroll är slutförd, sammanfattande informationen för fokusområden visas på den **AD-hälsokontroll** instrumentpanel för infrastrukturen i din miljö. I följande avsnitt beskrivs hur du använder informationen på den **AD-hälsokontroll** instrumentpanelen där du kan visa och sedan vidta rekommenderade åtgärder för din serverinfrastruktur för Active Directory.  

![Bild av AD-hälsokontroll panel](./media/ad-assessment/ad-healthcheck-summary-tile.png)

![Bild av AD-hälsokontroll instrumentpanelen](./media/ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Förutsättningar

* Kontroll av Active Directory Health-lösning kräver en version som stöds av .NET Framework 4.5.2 eller ovan som installerats på alla datorer som har den Microsoft Monitoring Agent (MMA) installerat.  MMA-agenten används av System Center 2016 – Operations Manager och Operations Manager 2012 R2 och Log Analytics-tjänsten.
* Lösningen har stöd för domänkontrollanter som kör Windows Server 2008 och 2008 R2, Windows Server 2012 och 2012 R2 och Windows Server 2016.
* En Log Analytics-arbetsyta för att lägga till Active Directory Health Check-lösningen från Azure marketplace i Azure-portalen.  Det krävs ingen ytterligare konfiguration.

  > [!NOTE]
  > När du har lagt till lösningen, läggs filen AdvisorAssessment.exe till servrar med agenter. Konfigurationsdata läsa och sedan skickas till Log Analytics-tjänsten i molnet för bearbetning. Logiken tillämpas på den mottagna data och Molntjänsten innehåller data.
  >
  >

Om du vill utföra hälsokontroll mot domänkontrollanter som är medlemmar i domänen som ska utvärderas kräver de en agent och en anslutning till Log Analytics med någon av följande metoder:

1. Installera den [Microsoft Monitoring Agent (MMA)](../../azure-monitor/platform/agent-windows.md) om domänkontrollanten inte redan övervakas av System Center 2016 – Operations Manager eller Operations Manager 2012 R2.
2. Om den är övervakad med System Center 2016 – Operations Manager eller Operations Manager 2012 R2 och hanteringsgruppen är inte integrerat med Log Analytics-tjänsten, domänkontrollanten kan ha flera värdar med Log Analytics för att samla in data och vidarebefordra dem till den tjänsten och fortfarande att övervakas av Operations Manager.  
3. I annat fall om Operations Manager-hanteringsgrupp är integrerad med tjänsten, du måste lägga till domänkontrollanterna för insamling av tjänsten anvisningarna under [lägga till datorer som hanteras med agent](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-log-analytics) när du har aktiverat lösningen i din arbetsyta.  

Agenten på domänkontrollanten vilka rapporter till en Operations Manager-hanteringsgrupp, samlar in data, vidarebefordrar till dess tilldelade hanteringsserver och sedan skickas direkt från en hanteringsserver till Log Analytics-tjänsten.  Data skrivs inte till Operations Manager-databaserna.  

## <a name="active-directory-health-check-data-collection-details"></a>Active Directory-hälsokontroll data samling information

Kontroll av Active Directory hälsotillstånd samlar in data från följande källor med hjälp av agent som du har aktiverat:

- Register
- LDAP
- .NET Framework
- Händelseloggen
- Active Directory Service interfaces (ADSI)
- Windows PowerShell
- Fildata
- Windows Management Instrumentation (WMI)
- DCDIAG verktyget API
- File Replication Service (NTFRS) API: et
- Anpassad C#-kod

Data som samlas in på domänkontrollanten och vidarebefordras till logganalys var sjunde dag.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Förstå hur rekommendationer är prioriterade
Varje rekommendation ges ett värde-värde som identifierar den relativa prioriteten för rekommendationen. Endast de 10 viktigaste rekommendationerna visas.

### <a name="how-weights-are-calculated"></a>Hur vikterna beräknas
Viktningar är aggregerade värden baserat på tre viktiga faktorer:

* Den *sannolikheten* att ett problem som identifieras orsakar problem. En högre sannolikhet motsvarar en större övergripande poäng för rekommendationen.
* Den *inverkan* av problemet i din organisation om det uppstår ett problem. En högre inverkan motsvarar en större övergripande poäng för rekommendationen.
* Den *arbete* krävs för att implementera rekommendationen. En högre ansträngning motsvarar en mindre övergripande poäng för rekommendationen.

Värde för varje rekommendation uttrycks som en procentandel av den sammanlagda poängen för varje fokusområde. Till exempel ökar implementera den här rekommendationen om en rekommendation i Fokusområde säkerhets- och har ett resultat på 5%, din övergripande poäng av 5% för säkerhet och efterlevnad.

### <a name="focus-areas"></a>Fokusområden
**Säkerhet och efterlevnad** – den här Fokusområde visar rekommendationer för potentiella säkerhetshot och överträdelser företagets principer och krav på teknisk, juridisk och regelmässig efterlevnad.

**Tillgänglighet och affärskontinuitet** – den här Fokusområde visar rekommendationer för tjänstens tillgänglighet, återhämtning för din infrastruktur och verksamhetsskydd.

**Prestanda och skalbarhet** – den här Fokusområde visar rekommendationer för att din organisations IT-infrastruktur växa, se till att din IT-miljö uppfyller aktuella prestandakrav och kan svara på förändrade infrastruktur behov.

**Uppgradering, migrering och distribution** – den här Fokusområde visar rekommendationer som hjälper dig att uppgradera, migrera och distribuera Active Directory till din befintliga infrastruktur.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Bör du försöka poäng 100% i varje Fokusområde?
Inte nödvändigtvis. Rekommendationerna baseras på de kunskaper och erfarenheter som gjorts av Microsofts tekniker till tusentals kunder besök. Men ingen två server-infrastrukturer är desamma och specifika rekommendationer kan vara mer eller mindre relevant för dig. Exempelvis kanske vissa säkerhetsrekommendationer mindre relevant om dina virtuella datorer inte exponeras för Internet. Vissa rekommendationer för tillgänglighet kan vara mindre användbart för tjänster som erbjuder låg prioritet ad hoc-insamling och rapportering. Problem som är viktiga för en mogen affärsverksamhet som kan vara mindre viktiga för en startfil. Du kanske vill identifiera vilka fokusområden som är dina prioriteringar och titta på hur ditt resultat ändras med tiden.

Varje rekommendation innehåller information om varför det är viktigt. Du bör använda den här vägledningen för att utvärdera om implementerar rekommendationen är lämplig för dig, baserat på typen av dina IT-tjänster och affärsbehoven för din organisation.

## <a name="use-health-check-focus-area-recommendations"></a>Använd hälsokontrollen fokus området rekommendationer
När den har installerats kan du visa sammanfattning av rekommendationer med hjälp av panelen hälsokontroll på sidan lösning i Azure-portalen.

Visa de sammanfattade efterlevnad utvärderingarna för din infrastruktur och sedan gå till rekommendationer.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Visa rekommendationer för en Fokusområde och vidta åtgärder
3. Klicka på den **översikt** panelen för Log Analytics-arbetsytan i Azure-portalen.
4. På den **översikt** klickar du på den **kontroll av Active Directory hälsotillstånd** panelen.
5. På den **hälsokontrollen** granskar den sammanfattande informationen i något av bladen fokus område och klicka sedan på ett om du vill visa rekommendationer för den fokusområde.
6. På någon av sidorna fokus området, kan du visa prioriterade rekommendationer för din miljö. Klicka på en rekommendation under **påverkade objekt** att visa information om varför rekommendationen görs.<br><br> ![Bild av hälsokontroll rekommendationer](./media/ad-assessment/ad-healthcheck-dashboard-02.png)
7. Du kan vidta korrigerande åtgärder som föreslås i **föreslagna åtgärder**. När objektet har utförts, senare utvärderingar poster som rekommenderade åtgärder som utförts och din kompatibilitetspoäng ökar. Korrigerad objekt visas som **skickas objekt**.

## <a name="ignore-recommendations"></a>Ignorera rekommendationer
Om du har synpunkter som du vill ignorera kan du skapa en textfil som Log Analytics använder för att förhindra rekommendationer visas i din utvärdering av resultaten.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Att identifiera rekommendationer som kommer att ignoreras
1. I Azure-portalen på sidan Log Analytics-arbetsyta för din valda arbetsyta klickar du på den **Loggsökning** panelen.
2. Använd följande fråga för att lista över rekommendationer som har misslyckats för datorer i din miljö.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Här är en skärmbild som visar loggsökningsfrågan:<br><br> ![misslyckade rekommendationer](./media/ad-assessment/ad-failed-recommendations.png)

3. Välj rekommendationer som du vill ignorera. Du använder värdena för RecommendationId i nästa procedur.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Du skapar och använder en IgnoreRecommendations.txt textfil
1. Skapa en fil med namnet IgnoreRecommendations.txt.
2. Klistra in eller ange varje RecommendationId för varje rekommendation som du vill Log Analytics för att ignorera på separata rader och sedan spara och stäng filen.
3. Placera filen i följande mapp på varje dator där du vill att Log Analytics för att ignorera rekommendationer.
   * På datorer med Microsoft Monitoring Agent (anslutet direkt eller via Operations Manager) - *SystemDrive*: \Program\Microsoft Monitoring Agent\Agent
   * På hanteringsservern för Operations Manager 2012 R2 - *SystemDrive*: \Program\Microsoft System Center 2012 R2\Operations Manager\Server
   * På hanteringsservern för Operations Manager 2016 - *SystemDrive*: \Program\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Kontrollera att rekommendationer ignoreras
I nästa schemalagda hälsotillstånd kontroll körs som standard var sjunde dag, de angivna rekommendationerna markeras *ignoreras* och kommer inte att visas på instrumentpanelen.

1. Du kan använda följande Loggsökning frågor för att lista alla ignorerade rekommendationer.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Om du senare bestämmer att du vill ta bort IgnoreRecommendations.txt filer om du vill visa ignorerade rekommendationer, eller du kan ta bort RecommendationIDs från dem.

## <a name="ad-health-check-solutions-faq"></a>AD Health Kontrollera lösningar för vanliga frågor och svar
*Hur ofta körs en hälsokontroll?*

* Kontrollen körs var sjunde dag.

*Finns det ett sätt att konfigurera hur ofta hälsokontrollen körs?*

* Inte just nu.

*Om en annan server för identifieras när jag har lagt till en lösning för health kontroll ska kontrolleras*

* Ja, när den identifieras de kontrolleras från sedan, var sjunde dag.

*Om en server är inaktiverad, när den tas bort från hälsokontrollen?*

* Om en server inte lämnar data för tre veckor, tas den bort.

*Vad är namnet på processen som gör datainsamlingen?*

* AdvisorAssessment.exe

*Hur lång tid tar det för de data som samlas in?*

* Den faktiska datainsamlingen på servern tar ungefär 1 timme. Det kan ta längre tid på servrar som har ett stort antal Active Directory-servrar.

*Finns det ett sätt att konfigurera när data har samlats in?*

* Inte just nu.

*Varför visas endast de översta 10 rekommendationerna?*

* Istället för att ge dig en fullständig överväldigande förteckning av uppgifter, rekommenderar vi att du kan fokusera på adressering prioriterade rekommendationer först. När du har gått dem blir ytterligare rekommendationer tillgängliga. Om du vill se en detaljerad lista kan visa du alla rekommendationer med hjälp av Loggsökning.

*Finns det ett sätt att ignorera en rekommendation?*

* Ja, se [Ignorera rekommendationer](#ignore-recommendations) ovan.

## <a name="next-steps"></a>Nästa steg
* Använd [Loggsökningar i Log Analytics](../../azure-monitor/log-query/log-query-overview.md) och lär dig att analysera detaljerad AD-hälsokontroll data och rekommendationer.
