---
title: Dra tillbaka Security Center funktioner (2019 juli) | Microsoft Docs
description: I den här artikeln beskrivs de funktioner i Security Center som har dragits tillbaka den 31 juli 2019.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 4a760fe1e5df7cf614a68f8fa8a05926326d3edf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80583225"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Dra tillbaka Security Center funktioner (2019 juli)

> [!NOTE]
> Det här dokumentet innehåller en lista över funktioner som har dragits tillbaka från Azure Security Center den 31 juli 2019.
>
>

Vi har gjort flera [förbättringar](https://azure.microsoft.com/updates/?product=security-center) för Azure Security Center under de sex månaderna, vilket ledde till 2019 juli.
Med dessa förbättrade funktioner tog vi bort några redundanta funktioner och relaterade API: er från Security Center den 31 juli 2019.

De flesta av dessa kasse rings funktioner kan ersättas med andra funktioner i Azure Security Center eller Azure Log Analytics. Andra funktioner kan implementeras med hjälp av [Azure Sentinel (för hands version)](https://azure.microsoft.com/services/azure-sentinel/).

Föråldrade Security Center-funktioner är:

- [Instrument panel för händelser](#menu_events)
- [Sök meny post](#menu_search)
- [Visa klassisk identitets & åtkomst länk för identitet och åtkomst (för hands version)](#menu_classicidentity)
- [Knapp för säkerhets händelser på karta för säkerhets aviseringar (för hands version)](#menu_securityeventsmap)
- [Anpassade aviserings regler (förhands granskning)](#menu_customalerts)
- [Undersök knapp i säkerhets aviseringar för skydd mot hot](#menu_investigate)
- [En delmängd av säkerhetslösningar](#menu_solutions)
- [Redigera säkerhetskonfigurationer för säkerhets principer](#menu_securityconfigurations)
- [Instrument panel för säkerhet och granskning (som ursprungligen användes i OMS-portalen) för Log Analytics arbets ytor](#menu_securityomsdashboard)

Den här artikeln innehåller detaljerad information för varje tillbakadragen funktion och de steg som du kan vidta för att implementera ersättnings funktioner.

## <a name="events-dashboard"></a>Instrument panel för händelser<a name="menu_events"></a>

Security Center använder Log Analytics agent för att samla in olika säkerhetsrelaterade konfigurationer och händelser från dina datorer. Dessa händelser lagras i dina arbets ytor. På [instrument panelen händelser](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) kan du visa dessa data och du får en start punkt för Log Analytics.

Den instrument panel för händelser som visades när du valde en arbets yta drogs tillbaka:

![Instrument panel för händelser][2]

### <a name="events-dashboard---the-new-experience"></a>Instrument panel för händelser – den nya upplevelsen

Vi rekommenderar att du använder de inbyggda funktionerna i Azure Log Analytics för att Visa viktiga händelser på dina arbets ytor.

Om du har skapat anpassade viktiga händelser i Security Center kommer de att vara tillgängliga. I Log Analytics går du till **Välj Sparad arbets yta**  >  **sparade sökningar**. Dina data går förlorade eller ändras inte. Interna viktiga händelser är också tillgängliga från samma skärm i Log Analytics.

![Sparade sökningar i arbets ytan][3]

## <a name="search-menu-entry"></a>Sök meny post<a name="menu_search"></a>

Azure Security Center använder för närvarande Azure Monitor loggar sökning för att hämta och analysera dina säkerhets data. Den här skärmen fungerar som ett fönster för att Log Analytics Sök sidan och gör det möjligt för användare att köra Sök frågor på den valda arbets ytan. Mer information finns i [Azure Security Center search](https://docs.microsoft.com/azure/security-center/security-center-search). Vi drog tillbaka det här Sök fönstret:

![Sök sida][4]

### <a name="search-menu-entry---the-new-experience"></a>Sök meny post – den nya upplevelsen

Vi rekommenderar att du använder Azure Log Analytics inbyggda funktioner för att utföra Sök frågor på dina arbets ytor. Gå till Azure Log Analytics och välj **loggar**.

![Sidan Log Analytics loggar][5]

## <a name="classic-identity--access-preview"></a>Klassisk identitets & åtkomst (för hands version)<a name="menu_classicidentity"></a>

Den klassiska identitets & åtkomst upplevelsen i Security Center visar för närvarande en instrument panel med identitets-och åtkomst information i Log Analytics. Så här visar du den här instrument panelen:

1. Välj **Visa klassisk identitets & åtkomst**.

   ![Sidan identitet][6]

1. Visa **identitets & åtkomst instrument panel**.

    ![Sidan identitet-val av arbets yta][7]

1. Välj en arbets yta för att öppna **identitets & åtkomst** instrument panel i Log Analytics för att Visa identitets-och åtkomst information på din arbets yta.

   ![Sidan identitet-instrument panel][8]

Vi har dragit tillbaka alla tre skärmarna som visas i föregående steg. Dina data är tillgängliga i Log Analytics säkerhetslösningen och har inte ändrats eller tagits bort.

### <a name="classic-identity--access-preview---the-new-experience"></a>Klassisk identitets & åtkomst (för hands version) – den nya upplevelsen

Instrument panelen för Log Analytics har visat insikter på en enskild arbets yta. Men interna Security Center funktioner ger insyn i alla prenumerationer och alla arbets ytor som är associerade med dem. Du kan komma åt en lättanvänd vy som du kan använda för att fokusera på det som är viktigt med rekommendationer rangordnade enligt deras säkra poäng.

Alla funktioner i **identitets & åtkomst** instrument panel i Log Analytics kan nås genom att välja **identitet & åtkomst (för hands version)** i Security Center.

![Sidan identitet – förbrukad klassisk upplevelse][9]

## <a name="security-events-map"></a>Karta över säkerhets händelser<a name="menu_securityeventsmap"></a>

Security Center ger dig en säkerhets aviserings karta som hjälper dig att identifiera säkerhetshot. Knappen **gå till säkerhets händelse översikt** i den kartan öppnar en instrument panel som gör att du kan visa rå säkerhets händelser på den valda arbets ytan.

Vi har tagit bort knappen **gå till säkerhets händelse mappning** och instrument panelen per arbets yta.

![Karta över säkerhets aviseringar – knapp][10]

När du väljer **mappnings knappen gå till säkerhets händelser** öppnas den (nu drog) instrument panel för hot information.

![Instrumentpanelen Hotinformation][11]

När du väljer en arbets yta för att visa instrument panelen för hot information öppnar du skärmen (nu drog tillbaka) säkerhets aviserings mappning (för hands version) i Log Analytics.

![Säkerhets aviseringar mappas i Log Analytics][12]

Dina befintliga data är tillgängliga i Log Analytics säkerhetslösningen och har inte ändrats eller tagits bort.

### <a name="security-events-map---the-new-experience"></a>Säkerhets händelser mappar – den nya upplevelsen

Vi rekommenderar att du använder funktionen aviserings mappning inbyggd i Security Center: **säkerhets aviserings mappning (för hands version)**. Den här funktionen ger en optimerad upplevelse och fungerar över alla prenumerationer och associerade arbets ytor. Det ger dig en övergripande vy över din miljö och fokuserar inte på en enskild arbets yta.

## <a name="custom-alert-rules-preview"></a>Anpassade aviserings regler (förhands granskning)<a name="menu_customalerts"></a>

Vi [drog tillbaka den anpassade aviserings upplevelsen](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) den 30 juni 2019 eftersom den underliggande infrastrukturen drogs tillbaka. Efter indragnings datumet genereras inga anpassade säkerhets aviseringar längre.
Vi rekommenderar att du aktiverar [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) och återskapar dina anpassade aviseringar där. Du kan också skapa aviseringar med Azure Monitor logg aviseringar.

Så här skapar du anpassade aviseringar med Azure Sentinel:

1. [Öppna Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview) och välj den arbets yta där dina anpassade aviseringar lagras
1. Välj **analys** på menyn
1. Följ anvisningarna i följande [självstudie](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) om hur du skapar anpassade aviseringar i Azure Sentinel

Om du inte är intresse rad av att använda Azure Sentinel kan du skapa aviseringar med Azure Monitor logg aviseringar. Instruktioner finns i [skapa, Visa och hantera logg aviseringar med hjälp av Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) och [logg aviseringar i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

![Anpassade aviseringar][13]

Mer information om hur du drar tillbaka anpassade aviseringar finns [i anpassade aviserings regler i Azure Security Center (för hands version)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## <a name="security-alerts-investigation"></a>Undersökning av säkerhets aviseringar<a name="menu_investigate"></a>

Undersöknings funktionen i Security Center hjälper dig att prioritering en potentiell säkerhets incident. Funktionen gör att du kan förstå omfattningen av en incident och spåra dess rotor saken. Vi har tagit bort den här funktionen från Security Center eftersom den har ersatts av en förbättrad upplevelse i [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/).

![Säkerhetsincident][14]

När du väljer knappen **Undersök** från en **säkerhets incident** skärm öppnar du instrument panelen för undersökningen (för hands version) i Log Analytics. Vi drog undersökningen på undersöknings instrument panelen.

Dina befintliga data är tillgängliga i Log Analytics säkerhetslösningen och har inte ändrats eller tagits bort.

![Undersöknings instrument panel i Log Analytics][15]

### <a name="investigation---the-new-experience"></a>Undersökning – den nya upplevelsen

Vi rekommenderar att du övergår till [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) för en omfattande gransknings upplevelse. Azure Sentinel tillhandahåller kraftfulla Sök-och specialverktyg som söker efter säkerhetshot i din organisations data källor.

## <a name="subset-of-security-solutions"></a>Delmängd av säkerhetslösningar<a name="menu_solutions"></a>

Security Center kan aktivera [integrerade säkerhetslösningar i Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). Vi drog tillbaka följande partner lösningar från Security Center. Dessa lösningar är aktiverade i [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) tillsammans med ett antal ytterligare data källor.

- [Nästa generations brand vägg och lösningar för webb program brand väggar](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Integrering av säkerhetslösningar som stöder common Event format (CEF)](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsofts avancerade hotanalys](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Efter pensionering kan du inte lägga till eller ändra någon av de lösnings typer som anges i föregående lista, antingen från användar gränssnittet eller API: et. Azure Security Center kommer inte längre att upptäcka några nya instanser av dessa partner lösningar.

Om du har befintliga anslutna lösningar rekommenderar vi att du flyttar till Azure Sentinel.

![Security Center-lösningar][16]

## <a name="edit-security-configurations-for-security-policies"></a>Redigera säkerhetskonfigurationer för säkerhets principer<a name="menu_securityconfigurations"></a>

Azure Security Center övervakar säkerhetskonfigurationer genom att använda en uppsättning av [över 150 rekommenderade regler](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) för att härda operativsystemet. Dessa regler gäller brand väggar, granskning, lösen ords principer med mera. Om en sårbar konfiguration hittas på en dator, genererar Security Center en säkerhetsrekommendation. På sidan [Redigera säkerhets konfiguration](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) kan kunderna anpassa standard säkerhets konfigurationen för operativ systemet i Security Center.

Vi drog tillbaka den här förhands gransknings funktionen. Om du vill återställa säkerhetskonfigurationerna till standardvärdena efter indragnings datumet gör du det via API eller PowerShell med hjälp av [följande instruktioner](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/Reset%20security%20configurations%20customization).

![Redigera säkerhetskonfigurationer][17]

### <a name="edit-security-configurations---the-new-experience"></a>Redigera säkerhetskonfigurationer – den nya upplevelsen

Vi planerar att aktivera Security Center för att stödja [gäst konfigurations agenten](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration). En sådan uppdatering gör att en mycket rikare funktion har angetts, inklusive stöd för fler operativ system och integrering av Azures principer för gästa konfigurationer. När ändringarna har Aktiver ATS kan du också kontrol lera konfigurationerna i skala och tillämpa dem på nya resurser automatiskt.

## <a name="security-and-audit-dashboard-for-log-analytics-workspaces"></a>Instrument panel för säkerhet och granskning för Log Analytics arbets ytor<a name="menu_securityomsdashboard"></a>

Instrument panelen för säkerhet och granskning användes ursprungligen i OMS-portalen. I Log Analytics tillhandahåller instrument panelen en översikt per arbets yta med viktiga säkerhets händelser och hot, en hot informations karta och en identitets-och-åtkomst-bedömning av säkerhets händelser som sparas i arbets ytan. Vi har tagit bort instrument panelen. Som vi redan rekommenderar i instrument panels gränssnittet, rekommenderar vi att du går över till Azure Security Center.

![Log Analytics säkerhets instrument panel][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Instrument panel för säkerhet och granskning – den nya upplevelsen

Vi rekommenderar att du växlar till Azure Security Center. Den ger samma säkerhets översikt över flera prenumerationer och de arbets ytor som är kopplade till dem, plus en rikare funktions uppsättning.

Du kan hämta de ursprungliga Log Analytics frågor som fyller på instrument panelen säkerhet och granskning i [GitHub-lagringsplatsen](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) för Security Center.

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
