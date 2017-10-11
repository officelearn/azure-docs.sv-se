---
title: "Optimera din Active Directory-miljö med Azure Log Analytics | Microsoft Docs"
description: "Du kan använda Active Directory-bedömning-lösning för att bedöma risken och hälsotillståndet för server-miljöer med regelbundna intervall."
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
ms.date: 08/15/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 97368f0b9e89ffd0cd982b6e8670d5a1f62ad42c
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-assessment-solution-in-log-analytics"></a>Optimera din Active Directory-miljö med Active Directory-bedömning lösningen i logganalys

![AD-bedömning symbol](./media/log-analytics-ad-assessment/ad-assessment-symbol.png)

Du kan använda Active Directory-bedömning-lösning för att bedöma risken och hälsotillståndet för server-miljöer med regelbundna intervall. Den här artikeln hjälper dig att installera och använda lösningen så att du kan vidta åtgärder för möjliga problem.

Den här lösningen ger en prioriterad lista med rekommendationer som är specifika för din distribuerade serverinfrastruktur. Rekommendationerna kategoriseras i fyra fokusområden, som hjälper dig att snabbt förstå risken och vidta lämpliga åtgärder.

Rekommendationerna baseras på kunskap och erfarenhet av Microsoft-tekniker från tusentals kunden besök. Varje rekommendation innehåller information om varför ett problem kan verkligen är viktiga och hur du implementerar de föreslagna ändringarna.

Du kan välja fokusområden som är viktigast för din organisation och spåra din framsteg mot kör en risk ledigt och hälsosam miljö.

När du har lagt till lösningen och en bedömning är slutförd, Sammanfattning visas information för fokusområden på den **AD-bedömning** instrumentpanel för infrastrukturen i din miljö. I följande avsnitt beskrivs hur du använder informationen på den **AD-bedömning** instrumentpanel, där du kan visa och sedan vidta rekommenderade åtgärder för din serverinfrastruktur för Active Directory.

![Bild av SQL-bedömning sida vid sida](./media/log-analytics-ad-assessment/ad-tile.png)

![Bild av SQL-bedömning instrumentpanelen](./media/log-analytics-ad-assessment/ad-assessment.png)

## <a name="installing-and-configuring-the-solution"></a>Installera och konfigurera lösningen
Använd följande information för att installera och konfigurera lösningarna.

* Agenterna måste installeras på domänkontrollanter som är medlemmar i domänen som ska utvärderas.
* Active Directory-bedömning lösningen kräver en version som stöds av .NET Framework 4 (4.5.2 eller senare) installerat på varje dator som har en OMS-agent.
* Lägg till Active Directory-bedömning lösningen till OMS-arbetsyta från [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ADAssessmentOMS?tab=Overview) eller genom att använda processen som beskrivs i [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md).  Det krävs ingen ytterligare konfiguration.

  > [!NOTE]
  > När du har lagt till lösningen läggs filen AdvisorAssessment.exe till servrar med agenter. Konfigurationsdata läsa och sedan skickas till OMS-tjänsten i molnet för bearbetning. Molntjänsten innehåller data att logik tillämpas för mottagna data.
  >
  >

## <a name="active-directory-assessment-data-collection-details"></a>Active Directory Assessment samling information

Active Directory-bedömning samlar in data från följande källor med agenter som du har aktiverat:

- Insamlare för registret
- LDAP-insamlare
- .NET framework
- Händelsen logginsamlare
- Active Directory Service interfaces (ADSI)
- Windows PowerShell
- Filen datainsamlare
- Windows Management Instrumentation (WMI)
- DCDIAG verktyget API
- File Replication Service (NTFRS) API
- Anpassade C#-kod


I följande tabell visas data collection metoder för agenter, om Operations Manager (SCOM) krävs och hur ofta data samlas in av en agent.

| Plattform | Styr Agent | SCOM-agent | Azure Storage | SCOM krävs? | SCOM-agent data som skickas via management-grupp | Frekvens för samlingen |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |7 dagar |

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

## <a name="use-assessment-focus-area-recommendations"></a>Använd assessment fokus området rekommendationer
Innan du kan använda en lösning i OMS måste ha installerat lösningen. Du kan läsa mer om hur du installerar lösningar finns [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md). När den har installerats kan du visa sammanfattning av rekommendationer med hjälp av panelen Assessment på översiktssidan i OMS.

Visa sammanfattade efterlevnad bedömningar för din infrastruktur och gå till rekommendationer.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Visa rekommendationer för en Fokusområde och vidta åtgärder
1. På den **översikt** klickar du på den **Assessment** panelen för din serverinfrastruktur.
2. På den **Assessment** , Granska sammanfattningen i ett fokus området blad och klickar sedan på en om du vill visa rekommendationer för området fokus.
3. På någon av sidorna fokus område, kan du visa prioriterad rekommendationer för din miljö. Klicka på en rekommendation enligt **påverkade objekt** att visa information om varför rekommendationen görs.  
    ![Bild av Assessment rekommendationer](./media/log-analytics-ad-assessment/ad-focus.png)
4. Du kan vidta åtgärder i **föreslagna åtgärder**. När objektet har behandlats senare bedömningar poster som rekommenderade åtgärder som utförts och kompatibilitet poängen ökar. Korrigerade objekt visas som **skickades objekt**.

## <a name="ignore-recommendations"></a>Ignorera rekommendationer
Om du har rekommendationer som du vill ignorera, kan du skapa en textfil som OMS använder för att förhindra rekommendationer visas i sökresultatet assessment.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Att identifiera rekommendationer som kommer att ignorera
1. Logga in på din arbetsyta och öppna loggen sökning. Använd följande fråga för att lista över rekommendationer som har misslyckats för datorer i din miljö.

   ```
   Type=ADAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
   ```
>[!NOTE]
> Om ditt arbetsområde har uppgraderats till den [nya Log Analytics-frågespråket](log-analytics-log-search-upgrade.md), sedan frågan ovan skulle ändra till följande.
>
> `ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

   Här är en skärmbild som visar loggen sökfråga: ![misslyckades rekommendationer](./media/log-analytics-ad-assessment/ad-failed-recommendations.png)
2. Välj rekommendationer som du vill ignorera. Du ska använda värdena för RecommendationId i nästa procedur.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Du skapar och använder en IgnoreRecommendations.txt textfil
1. Skapa en fil med namnet IgnoreRecommendations.txt.
2. Klistra in eller ange varje RecommendationId för varje rekommendation som du vill logganalys Ignorera på en separat rad och spara och stäng filen.
3. Placera filen i följande mapp på varje dator där du vill att OMS att ignorera rekommendationer.
   * På datorer med Microsoft Monitoring Agent (ansluten direkt eller via Operations Manager) - *SystemDrive*: \Program\Microsoft övervakning Agent\Agent
   * På hanteringsservern för Operations Manager - *SystemDrive*: \Program\Microsoft System Center 2012 R2\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Kontrollera att rekommendationer ignoreras
I nästa schemalagda utvärdering körs som standard var sjunde dag, de angivna rekommendationerna markeras *ignorerade* och visas inte på instrumentpanelen assessment.

1. Du kan använda följande loggen sökfrågor för att lista alla rekommendationer som ignoreras.

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
>[!NOTE]
> Om ditt arbetsområde har uppgraderats till den [nya Log Analytics-frågespråket](log-analytics-log-search-upgrade.md), sedan frågan ovan skulle ändra till följande.
>
> `ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

2. Om du senare bestämmer att du vill se ignoreras rekommendationer, ta bort IgnoreRecommendations.txt filer eller du kan ta bort RecommendationIDs från dem.

## <a name="ad-assessment-solutions-faq"></a>Lösningar för AD-bedömning vanliga frågor och svar
*Hur ofta körs en utvärdering?*

* Utvärderingen körs var sjunde dag.

*Finns det ett sätt att konfigurera hur ofta bedömning körs?*

* Inte just nu.

*Om en annan server för upptäcks när jag har lagt till en lösning, används den för att bedöma?*

* Ja, när det upptäcks att det bedöms från sedan, var sjunde dag.

*Om en server tas ur drift, när den tas bort från bedömningen?*

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
* Använd [logga sökningar i logganalys](log-analytics-log-searches.md) att visa detaljerade data för AD-bedömning och rekommendationer.
