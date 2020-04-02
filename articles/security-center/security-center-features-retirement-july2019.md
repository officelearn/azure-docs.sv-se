---
title: Pensionering av Security Center-funktioner (juli 2019) | Microsoft-dokument
description: I den här artikeln beskrivs funktionerna i Security Center som drogs tillbaka den 31 juli 2019.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 1f923db5c730f6dec15f4fc211ab7acbd009ac7f
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521423"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Pensionering av Security Center-funktioner (juli 2019)

> [!NOTE]
> I det här dokumentet beskrivs listan över funktioner som har dragits tillbaka från Azure Security Center den 31 juli 2019.
>
>

Vi har gjort flera [förbättringar](https://azure.microsoft.com/updates/?product=security-center) av Azure Security Center under de sex månaderna fram till juli 2019.
Med de här förbättrade funktionerna tog vi bort några redundanta funktioner och relaterade API:er från Security Center den 31 juli 2019.

De flesta av dessa pensionerade funktioner kan ersättas med andra funktioner i Azure Security Center eller Azure Log Analytics. Andra funktioner kan implementeras med [Azure Sentinel (förhandsversion)](https://azure.microsoft.com/services/azure-sentinel/).

Pensionerade Security Center-funktioner är:

- [Instrumentpanelen Händelser](#menu_events)
- [Sök menypost](#menu_search)
- [Visa den klassiska länken Identitet & Access på Identitet och åtkomst (förhandsgranskning)](#menu_classicidentity)
- [Knappen Karta över säkerhetshändelser på karta över säkerhetsvarningar (förhandsgranskning)](#menu_securityeventsmap)
- [Anpassade varningsregler (förhandsgranskning)](#menu_customalerts)
- [Knappen Undersök i säkerhetsvarningar för skydd mot hot](#menu_investigate)
- [En delmängd av säkerhetslösningar](#menu_solutions)
- [Redigera säkerhetskonfigurationer för säkerhetsprinciper](#menu_securityconfigurations)
- [Instrumentpanel för säkerhet och granskning (används ursprungligen i OMS-portalen) för Log Analytics-arbetsytor](#menu_securityomsdashboard)

Den här artikeln innehåller detaljerad information för varje pensionerad funktion och de åtgärder du kan vidta för att implementera ersättningsfunktioner.

## <a name="events-dashboard"></a>Instrumentpanelen Händelser<a name="menu_events"></a>

Security Center använder Log Analytics-agent för att samla in olika säkerhetsrelaterade konfigurationer och händelser från dina datorer. Den lagrar dessa händelser i dina arbetsytor. Med [instrumentpanelen för händelser](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) kan du visa dessa data och ge dig en startpunkt till Log Analytics.

Vi har dragit tillbaka instrumentpanelen för händelser som visades när du valde en arbetsyta:

![Instrumentpanelen Händelser][2]

### <a name="events-dashboard---the-new-experience"></a>Instrumentpanelen händelser – den nya upplevelsen

Vi uppmuntrade dig att använda de inbyggda funktionerna i Azure Log Analytics för att visa anmärkningsvärda händelser på dina arbetsytor.

Om du har skapat anpassade viktiga händelser i Security Center är de tillgängliga. Gå till Välj **Select workspace** > **sparade sökningar på**arbetsyta i Logganalys . Dina data kommer inte att gå förlorade eller ändras. Inbyggda anmärkningsvärda händelser är också tillgängliga från samma skärm i Log Analytics.

![Sparade sökningar på arbetsyta][3]

## <a name="search-menu-entry"></a>Sök menypost<a name="menu_search"></a>

Azure Security Center använder för närvarande Azure Monitor-loggar sökning för att hämta och analysera dina säkerhetsdata. Den här skärmen fungerar som ett fönster till Söksidan för Log Analytics och gör det möjligt för användare att köra sökfrågor på sin valda arbetsyta. Mer information finns i [Sök i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-search). Vi har dragit tillbaka det här sökfönstret:

![Sök sida][4]

### <a name="search-menu-entry---the-new-experience"></a>Sök menypost - den nya upplevelsen

Vi rekommenderar att du använder de inbyggda funktionerna i Azure Log Analytics för att utföra sökfrågor på dina arbetsytor. Gå till Azure Log Analytics och välj **Loggar**.

![Sidan Logga analytics-loggar][5]

## <a name="classic-identity--access-preview"></a>Klassisk identitet & access (förhandsgranskning)<a name="menu_classicidentity"></a>

Den klassiska & Access-upplevelsen i Security Center visar för närvarande en instrumentpanel för identitets- och åtkomstinformation i Logganalys. Så här visar du den här instrumentpanelen:

1. Välj **Visa klassisk identitet & Access**.

   ![Sidan Identitet][6]

1. Visa **instrumentpanelen Identitet & Access**.

    ![Identitetssida - val av arbetsyta][7]

1. Välj en arbetsyta för att öppna instrumentpanelen **Identity & Access** i Logganalys för att visa identitets- och åtkomstinformation på arbetsytan.

   ![Identitetssida - instrumentpanel][8]

Vi har pensionerat alla tre skärmarna som visas i föregående steg. Dina data förblir tillgängliga i säkerhetslösningen Log Analytics och har inte ändrats eller tagits bort.

### <a name="classic-identity--access-preview---the-new-experience"></a>Classic Identity & Access (Preview) - den nya upplevelsen

Log Analytics-instrumentpanelen har visat insikter på en enda arbetsyta. Inbyggda Security Center-funktioner ger dock insyn i alla prenumerationer och alla arbetsytor som är associerade med dem. Du kan komma åt en lättanvänd vy som gör att du kan fokusera på vad som är viktigt med rekommendationer som rangordnas enligt deras Säkra resultat.

Alla funktioner på instrumentpanelen **Identity & Access** i Log Analytics kan nås genom att välja Identitet & åtkomst **(förhandsversion)** i Security Center.

![Identitetssida - klassisk upplevelsepension][9]

## <a name="security-events-map"></a>Karta över säkerhetshändelser<a name="menu_securityeventsmap"></a>

Security Center ger dig en [säkerhetsvarningskarta](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) för att identifiera säkerhetshot. Knappen **Gå till säkerhetshändelser på** den kartan öppnar en instrumentpanel som gör att du kan visa råa säkerhetshändelser på den valda arbetsytan.

Vi tog bort knappen Visa go **to-säkerhetshändelser** och instrumentpanelen per arbetsyta.

![Karta över säkerhetsvarningar - knapp][10]

När du väljer knappen Visa gå **till säkerhetshändelser** öppnades instrumentpanelen (nu pensionerad) hotinformation.

![Instrumentpanelen Hotinformation][11]

När du väljer en arbetsyta för att visa instrumentpanelen för hotinformation öppnade du säkerhetsvarningskartan (nu pensionerad) i Logganalys.

![Karta över säkerhetsvarningar i Logganalys][12]

Dina befintliga data förblir tillgängliga i säkerhetslösningen Log Analytics och har inte ändrats eller tagits bort.

### <a name="security-events-map---the-new-experience"></a>Karta över säkerhetshändelser – den nya upplevelsen

Vi rekommenderar att du använder de varningskartfunktioner som är inbyggda i Security Center: **Karta över säkerhetsvarningar (förhandsgranskning).** Den här funktionen ger en optimerad upplevelse och fungerar över alla prenumerationer och associerade arbetsytor. Det ger dig en vy på hög nivå i hela din miljö och är inte fokuserad på en enda arbetsyta.

## <a name="custom-alert-rules-preview"></a>Anpassade varningsregler (förhandsgranskning)<a name="menu_customalerts"></a>

Vi [drog tillbaka upplevelsen av anpassade aviseringar den](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) 30 juni 2019 eftersom den underliggande infrastrukturen drogs tillbaka. Efter återgivningsdatumet genereras inte längre anpassade säkerhetsaviseringar.
Vi rekommenderar att du aktiverar [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) och återskapar dina anpassade aviseringar där. Alternativt kan du skapa dina aviseringar med Azure Monitor-loggaviseringar.

Så här skapar du anpassade aviseringar med Azure Sentinel:

1. [Öppna Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview) och välj arbetsytan där dina anpassade aviseringar lagras
1. Välj **Analytics** på menyn
1. Följ instruktionerna i följande [självstudie om](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) hur du skapar anpassade aviseringar i Azure Sentinel

Om du inte är intresserad av att använda Azure Sentinel kan du skapa aviseringar med Azure Monitor-loggaviseringar. Instruktioner finns i [Skapa, visa och hantera loggaviseringar med hjälp av Azure Monitor-](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) och [Loggaviseringar i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

![Anpassade aviseringar][13]

Mer information om pensionering av anpassade aviseringar finns [i Anpassade varningsregler i Azure Security Center (förhandsversion)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## <a name="security-alerts-investigation"></a>Utredning av säkerhetsvarningar<a name="menu_investigate"></a>

[Funktionen Utredning](https://docs.microsoft.com/azure/security-center/security-center-investigation) i Security Center hjälper dig att triage en potentiell säkerhetsincident. Funktionen gör att du kan förstå omfattningen av en incident och spåra dess grundorsak. Vi har tagit bort den här funktionen från Security Center eftersom den har ersatts med en förbättrad upplevelse i [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/).

![Säkerhetsincident][14]

När du väljer knappen **Undersök** på en **säkerhetsincidentskärm** öppnar du investigation dashboard (Preview) i Logganalys. Vi har pensionerat utredningsinstrumentpanelen.

Dina befintliga data förblir tillgängliga i säkerhetslösningen Log Analytics och har inte ändrats eller tagits bort.

![Instrumentpanel för undersökningar i Logganalys][15]

### <a name="investigation---the-new-experience"></a>Utredning - den nya upplevelsen

Vi rekommenderar att du övergår till [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) för en omfattande undersökningsupplevelse. Azure Sentinel tillhandahåller kraftfulla sök- och frågeverktyg för att jaga efter säkerhetshot i organisationens datakällor.

## <a name="subset-of-security-solutions"></a>Delmängd av säkerhetslösningar<a name="menu_solutions"></a>

Security Center kan aktivera [integrerade säkerhetslösningar i Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). Vi har dragit tillbaka följande partnerlösningar från Security Center. Dessa lösningar är aktiverade i [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) tillsammans med ett antal ytterligare datakällor.

- [Nästa generations brandväggs- och webbapplikationslösningar](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Integrering av säkerhetslösningar som stöder det gemensamma händelseformatet (FSE)](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsofts avancerade hotanalys](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Efter pensioneringen kan du inte lägga till eller ändra någon av de lösningstyper som nämns i föregående lista, antingen från användargränssnittet eller API:et. Azure Security Center kommer inte längre att upptäcka några nya instanser av dessa partnerlösningar.

Om du har befintliga anslutna lösningar rekommenderar vi att du flyttar till Azure Sentinel.

![Lösningar för säkerhetscenter][16]

## <a name="edit-security-configurations-for-security-policies"></a>Redigera säkerhetskonfigurationer för säkerhetsprinciper<a name="menu_securityconfigurations"></a>

Azure Security Center övervakar säkerhetskonfigurationer genom att använda en uppsättning av [över 150 rekommenderade regler](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) för att härda operativsystemet. Dessa regler gäller brandväggar, granskning, lösenordsprinciper med mera. Om en sårbar konfiguration hittas på en dator, genererar Security Center en säkerhetsrekommendation. På [skärmen Redigera säkerhetskonfiguration](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) kan kunder anpassa standardkonfigurationen för operativsystem i Security Center.

Vi har dragit tillbaka den här förhandsgranskningsfunktionen. Om du vill återställa säkerhetskonfigurationerna till standardvärdena efter återställningsdatumet gör du det via API:et eller Powershell med hjälp av [följande instruktioner](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/Reset%20security%20configurations%20customization).

![Redigera säkerhetskonfigurationer][17]

### <a name="edit-security-configurations---the-new-experience"></a>Redigera säkerhetskonfigurationer – den nya upplevelsen

Vi har för avsikt att aktivera Security Center för att stödja [konfigurationsagenten Gäst.](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) En sådan uppdatering möjliggör en mycket rikare funktionsuppsättning, inklusive stöd för fler operativsystem och integrering av Azure-principer för gästkonfigurationer. När ändringarna har aktiverats kan du också styra konfigurationer i stor skala och tillämpa dem på nya resurser automatiskt.

## <a name="security-and-audit-dashboard-for-log-analytics-workspaces"></a>Instrumentpanel för säkerhet och granskning för log analytics-arbetsytor<a name="menu_securityomsdashboard"></a>

Instrumentpanelen för säkerhet och granskning användes ursprungligen i OMS-portalen. I Logganalys ger instrumentpanelen en översikt över anmärkningsvärda säkerhetshändelser och hot per arbetsyta, en hotinformationskarta och en identitets- och åtkomstbedömning av säkerhetshändelser som sparats på arbetsytan. Vi tog bort instrumentpanelen. Som vi redan har rekommenderat i instrumentpanelsgränssnittet rekommenderar vi att du övergår till Azure Security Center.

![Säkerhetsinstrumentpanel för Logganalys][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Instrumentpanel för säkerhet och granskning – den nya upplevelsen

Vi råder dig att byta till Azure Security Center. Det ger samma säkerhetsöversikt över flera prenumerationer och de arbetsytor som är associerade med dem, plus en rikare funktionsuppsättning.

Du kan hämta de ursprungliga Log Analytics-frågorna som fyller i säkerhets- och granskningsinstrumentpanelen i [GitHub-databasen](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) för Security Center.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/).
- Läs mer om [Azure Sentinel](https://docs.microsoft.com/azure/sentinel).

<!--Image references - events-->
[1]: ./media/security-center-features-retirement-july2019/asc_events_dashboard.png
[2]: ./media/security-center-features-retirement-july2019/asc_events_dashboard_inner.png
[3]: ./media/security-center-features-retirement-july2019/workspace_saved_searches.png
<!--Image references - search-->
[4]: ./media/security-center-features-retirement-july2019/asc_search.png
[5]: ./media/security-center-features-retirement-july2019/workspace_logs.png
<!--Image references - classic identity and access-->
[6]: ./media/security-center-features-retirement-july2019/asc_identity.png
[7]: ./media/security-center-features-retirement-july2019/asc_identity_workspace_selection.png
[8]: ./media/security-center-features-retirement-july2019/loganalytics_dashboard_identity.png
[9]: ./media/security-center-features-retirement-july2019/asc_identity_nobuttonhighlight.png
<!--Image references - alerts map-->
[10]: ./media/security-center-features-retirement-july2019/asc_security_alerts_map.png
[11]: ./media/security-center-features-retirement-july2019/asc_threat_intellignece_dashboard.png
[12]: ./media/security-center-features-retirement-july2019/loganalytics_security_alerts_map.png
<!--Image references - custom alerts-->
[13]: ./media/security-center-features-retirement-july2019/asc_custom_alerts.png
<!--Image references - Investigation-->
[14]: ./media/security-center-features-retirement-july2019/asc-security-incident.png
[15]: ./media/security-center-features-retirement-july2019/loganalytics_investigation_dashboard.png
<!--Image references - Solutions-->
[16]: ./media/security-center-features-retirement-july2019/asc_security_solutions.png
<!--Image references - Edit security configurations-->
[17]: ./media/security-center-features-retirement-july2019/asc_edit_security_configurations.png
<!--Image references - Security dashboard in log analytics-->
[18]: ./media/security-center-features-retirement-july2019/loganalytics_security_dashboard.png
