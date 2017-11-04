---
title: "Optimera din Active Directory-miljö med Azure Log Analytics | Microsoft Docs"
description: "Du kan använda Active Directory Health Check-lösning för att bedöma risken och hälsotillståndet för dina miljöer regelbundna intervall."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 81eb41b8-eb62-4eb2-9f7b-fde5c89c9b47
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte;banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e78ca1da8cafe93e76d640c0e6d5ad5309655c1b
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2017
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-log-analytics"></a>Optimera din Active Directory-miljö med Active Directory Health Check-lösningen i logganalys

![AD Health Check symbol](./media/log-analytics-ad-assessment/ad-assessment-symbol.png)

Du kan använda Active Directory Health Check-lösning för att bedöma risken och hälsotillståndet för server-miljöer med regelbundna intervall. Den här artikeln hjälper dig att installera och använda lösningen så att du kan vidta åtgärder för möjliga problem.

Den här lösningen ger en prioriterad lista med rekommendationer som är specifika för din distribuerade serverinfrastruktur. Rekommendationerna kategoriseras i fyra fokusområden, som hjälper dig att snabbt förstå risken och vidta lämpliga åtgärder.

Rekommendationerna baseras på kunskap och erfarenhet av Microsoft-tekniker från tusentals kunden besök. Varje rekommendation innehåller information om varför ett problem kan verkligen är viktiga och hur du implementerar de föreslagna ändringarna.

Du kan välja fokusområden som är viktigast för din organisation och spåra din framsteg mot kör en risk ledigt och hälsosam miljö.

När du har lagt till lösningen och en kontroll är slutförd, Sammanfattning visas information för fokusområden på den **AD Health Check** instrumentpanel för infrastrukturen i din miljö. I följande avsnitt beskrivs hur du använder informationen på den **AD Health Check** instrumentpanel, där du kan visa och sedan vidta rekommenderade åtgärder för din serverinfrastruktur för Active Directory.  

![Bild av AD Health Check sida vid sida](./media/log-analytics-ad-assessment/ad-healthcheck-summary-tile.png)

![Bild av AD Health Check instrumentpanelen](./media/log-analytics-ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Krav

* Active Directory Health Check-lösning kräver en version som stöds av .NET Framework 4.5.2 eller senare installerat på varje dator som har den Microsoft Monitoring Agent (MMA) installerad.  MMA agent används av System Center 2016 - Operations Manager och Operations Manager 2012 R2 och Log Analytics-tjänsten. 
* Lösningen har stöd för domänkontrollanter som kör Windows Server 2008 och 2008 R2, Windows Server 2012 och 2012 R2 och Windows Server 2016.
* En logganalys-arbetsytan att lägga till Active Directory Health Check-lösning från Azure marketplace i Azure-portalen.  Det krävs ingen ytterligare konfiguration.

  > [!NOTE]
  > När du har lagt till lösningen läggs filen AdvisorAssessment.exe till servrar med agenter. Konfigurationsdata läsa och sedan skickas till Log Analytics-tjänsten i molnet för bearbetning. Molntjänsten innehåller data att logik tillämpas för mottagna data.
  >
  >

Om du vill utföra hälsokontroll mot domänkontrollanter som är medlemmar i domänen som ska utvärderas kräver de en agent och anslutningen till logganalys med någon av följande metoder:

1. Installera den [Microsoft Monitoring Agent (MMA)](log-analytics-windows-agents.md) om domänkontrollanten inte redan övervakas av System Center 2016 - Operations Manager eller Operations Manager 2012 R2.
2. Om den är övervakad med System Center 2016 - Operations Manager eller Operations Manager 2012 R2 och hanteringsgruppen inte är integrerad med Log Analytics-tjänsten, domänkontrollanten kan vara multi-homed med logganalys samla in data och vidarebefordra till den tjänsten och fortfarande övervakas av Operations Manager.  
3. Annars om hanteringsgruppen för Operations Manager är integrerat med tjänsten, du måste lägga till domänkontrollanterna för insamling av tjänsten följande under [lägga till datorer som hanteras med agent](log-analytics-om-agents.md#connecting-operations-manager-to-oms) när du har aktiverat lösning på arbetsytan.  

Agenten på en domänkontrollant att en Operations Manager-hanteringsgrupp samlar in data, vidarebefordrar till dess tilldelade hanteringsserver och skickas sedan direkt från en hanteringsserver till Log Analytics-tjänsten.  Data skrivs inte till Operations Manager-databaserna.  

## <a name="active-directory-health-check-data-collection-details"></a>Active Directory Health Check samling information

Active Directory Health Check samlar in data från följande källor med agenten som du har aktiverat:

- Registret 
- LDAP 
- .NET framework
- Händelseloggen 
- Active Directory Service interfaces (ADSI)
- Windows PowerShell
- Fildata 
- Windows Management Instrumentation (WMI)
- DCDIAG verktyget API
- File Replication Service (NTFRS) API
- Anpassade C#-kod

Data samlas in på domänkontrollanten och vidarebefordras till logganalys var sjunde dag.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Förstå hur rekommendationer prioriteras
Varje rekommendation är ett givet värde som identifierar en avvägning mellan kraven för rekommendationen. Endast de 10 viktigaste rekommendationerna visas.

### <a name="how-weights-are-calculated"></a>Hur vikterna beräknas
Viktningar är sammanställda värden som baseras på tre viktiga faktorer:

* Den *sannolikhet* att ett problem som identifieras orsakar problem. En högre sannolikhet är lika med en större övergripande poäng för rekommendationen.
* Den *inverkan* av problemet i din organisation om det uppstår ett problem. En högre inverkan är lika med en större övergripande poäng för rekommendationen.
* Den *arbete* krävs för att implementera denna rekommendation. Det är lika med en mindre övergripande poäng för rekommendationen högre prestanda.

Värde för varje rekommendation uttrycks i procent av den sammanlagda poängen för varje fokusområde. Till exempel ökar implementera denna rekommendation om en rekommendation i området för säkerhet och efterlevnad fokus har ett resultat på 5%, din övergripande säkerhet och efterlevnad poäng av 5%.

### <a name="focus-areas"></a>Fokusområden
**Säkerhet och efterlevnad** -fokus visas här rekommendationer för potentiella säkerhetshot och överträdelser, företagets principer och krav på teknisk, juridisk och regelmässig efterlevnad.

**Tillgänglighet och affärskontinuitet** -fokus visas här rekommendationer för tjänstetillgänglighet, återhämtning för din infrastruktur och business-skydd.

**Prestanda och skalbarhet** -fokus visas här rekommendationer som hjälper din organisations IT-infrastruktur växer, se till att din IT-miljö uppfyller aktuella prestandakrav och kan svara på förändrade infrastrukturbehov.

**Uppgradera migrering och distribution** -fokus visas här rekommendationer när du uppgraderar, migrera och distribuera Active Directory till din befintliga infrastruktur.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Bör du försöka poängsätta 100% överallt fokus i?
Inte nödvändigtvis. Rekommendationerna baseras på kunskaper och erfarenheter som gjorts av Microsoft-tekniker över tusentals kunden besök. Dock inga två server-infrastrukturen är samma och specifika rekommendationer kan vara mer eller mindre relevant för dig. Exempelvis kanske vissa säkerhetsrekommendationer mindre relevant om de virtuella datorerna inte utsätts för Internet. Vissa tillgänglighet rekommendationer kan vara relevanta för tjänster som erbjuder med låg prioritet ad hoc-insamling och rapportering. Problem som är viktiga för en mogen företag kanske mindre viktiga för en Startup. Du kanske vill identifiera vilka fokusområden är dina prioriteter och titta på hur ditt resultat förändras över tid.

Varje rekommendation innehåller information om varför det är viktigt. Du bör använda den här vägledningen för att utvärdera om implementera rekommendationen är lämplig för dig, baserat på typ av din IT-tjänster och affärsbehoven för din organisation.

## <a name="use-health-check-focus-area-recommendations"></a>Använd hälsokontroll fokus området rekommendationer
När den har installerats kan du visa sammanfattning av rekommendationer med hjälp av Health Check-panelen på sidan lösning i Azure-portalen.

Visa sammanfattade efterlevnad bedömningar för din infrastruktur och gå till rekommendationer.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Visa rekommendationer för en Fokusområde och vidta åtgärder
1. Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com). 
2. I Azure-portalen klickar du på **fler tjänster** hittades i det nedre vänstra hörnet. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **logga Analytics**.
3. Välj en arbetsyta i fönstret logganalys prenumerationer och klicka sedan på den **OMS-portalen** panelen.  
4. På den **översikt** klickar du på den **AD Health Check** panelen. 
5. På den **Health Check** , Granska sammanfattningen i ett fokus området blad och klickar sedan på en om du vill visa rekommendationer för området fokus.
6. På någon av sidorna fokus område, kan du visa prioriterad rekommendationer för din miljö. Klicka på en rekommendation enligt **påverkade objekt** att visa information om varför rekommendationen görs.<br><br> ![Bild av Health Check rekommendationer](./media/log-analytics-ad-assessment/ad-healthcheck-dashboard-02.png)
7. Du kan vidta åtgärder i **föreslagna åtgärder**. När objektet har behandlats senare bedömningar poster som rekommenderade åtgärder som utförts och kompatibilitet poängen ökar. Korrigerade objekt visas som **skickades objekt**.

## <a name="ignore-recommendations"></a>Ignorera rekommendationer
Om du har rekommendationer som du vill ignorera kan skapa du en textfil som Log Analytics för att förhindra rekommendationer visas i sökresultatet assessment.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Att identifiera rekommendationer som kommer att ignorera
1. I Azure-portalen på sidan för logganalys-arbetsytan för den valda arbetsytan klickar du på den **loggen Sök** panelen.
2. Använd följande fråga för att lista över rekommendationer som har misslyckats för datorer i din miljö.

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```
    >[!NOTE]
    > Om ditt arbetsområde har uppgraderats till den [nya Log Analytics-frågespråket](log-analytics-log-search-upgrade.md), sedan frågan ovan skulle ändra till följande.
    >
    > `ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Här är en skärmbild som visar frågan loggen:<br><br> ![misslyckade rekommendationer](./media/log-analytics-ad-assessment/ad-failed-recommendations.png)

3. Välj rekommendationer som du vill ignorera. Du ska använda värdena för RecommendationId i nästa procedur.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Du skapar och använder en IgnoreRecommendations.txt textfil
1. Skapa en fil med namnet IgnoreRecommendations.txt.
2. Klistra in eller ange varje RecommendationId för varje rekommendation som du vill logganalys Ignorera på en separat rad och spara och stäng filen.
3. Placera filen i följande mapp på varje dator där du vill att logganalys att ignorera rekommendationer.
   * På datorer med Microsoft Monitoring Agent (ansluten direkt eller via Operations Manager) - *SystemDrive*: \Program\Microsoft övervakning Agent\Agent
   * På hanteringsservern för Operations Manager 2012 R2 - *SystemDrive*: \Program\Microsoft System Center 2012 R2\Operations Manager\Server 
   * På hanteringsservern för Operations Manager 2016 - *SystemDrive*: \Program\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Kontrollera att rekommendationer ignoreras
I nästa schemalagda health check körs som standard var sjunde dag, de angivna rekommendationerna markeras *ignorerade* och kommer inte att visas på instrumentpanelen.

1. Du kan använda följande loggen sökfrågor för att lista alla rekommendationer som ignoreras.

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
    >[!NOTE]
    > Om ditt arbetsområde har uppgraderats till den [nya Log Analytics-frågespråket](log-analytics-log-search-upgrade.md), sedan frågan ovan skulle ändra till följande.
    >
    > `ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

2. Om du senare bestämmer att du vill se ignoreras rekommendationer, ta bort IgnoreRecommendations.txt filer eller du kan ta bort RecommendationIDs från dem.

## <a name="ad-health-check-solutions-faq"></a>AD Health Check lösningar vanliga frågor och svar
*Hur ofta körs hälsokontrollen?*

* Kontrollen körs var sjunde dag.

*Finns det ett sätt att konfigurera hur ofta hälsotillståndskontroll körs?*

* Inte just nu.

*Om en annan server för identifieras när jag har lagt till en kontroll lösning hälsotillstånd ska kontrolleras*

* Ja, när det upptäcks checkas från sedan, var sjunde dag.

*Om en server tas ur drift, när den tas bort från hälsotillståndskontroll?*

* Om en server inte lämnar data 3 veckor tas bort.

*Vad är namnet på processen som gör datainsamlingen?*

* AdvisorAssessment.exe

*Hur lång tid tar det för data som samlas in?*

* Samlingen faktiska data på servern tar ungefär en timme. Det kan ta längre tid på servrar som har ett stort antal Active Directory-servrar.

*Finns det ett sätt att konfigurera när data samlas in?*

* Inte just nu.

*Varför visas endast topp 10 rekommendationer?*

* I stället för att ge dig en överväldigande uttömmande förteckning över aktiviteter, rekommenderar vi att du fokusera på adressering prioriterad rekommendationerna först. När du åtgärda dem blir ytterligare rekommendationer tillgängliga. Du kan visa alla rekommendationer loggen sökning om du vill se en detaljerad lista.

*Finns det ett sätt att ignorera en rekommendation?*

* Ja, se [Ignorera rekommendationer](#ignore-recommendations) ovan.

## <a name="next-steps"></a>Nästa steg
* Använd [logga sökningar i logganalys](log-analytics-log-searches.md) information om hur du analysera detaljerad AD Health Check data och rekommendationer.
