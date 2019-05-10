---
title: Tillbakadragning av Security Center-funktioner (juli 2019) | Microsoft Docs
description: Den här artikeln beskriver funktioner i Security Center som kommer att dras tillbaka den 31 juli 2019.
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 614dabe842c7fe99da3ddb486c27003c79382ea1
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231385"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Tillbakadragning av Security Center-funktioner (juli 2019)

Vi har gjort flera [förbättringar](https://azure.microsoft.com/updates/?product=security-center) till Azure Security Center under de senaste 6 månaderna.  
Med förbättrade funktioner vi tar bort ett antal redundant funktioner, samt relaterade API: er från Security Center 31 juli 2019.  

De flesta tillbakadragna funktioner kan ersättas med nya funktioner i Azure Security Center eller Log Analytics; och ett par funktioner kan implementeras med hjälp av [Azure Sentinel (förhandsversion)](https://azure.microsoft.com/services/azure-sentinel/), nyligen har tillkännagivits.

Lista över funktioner som dras tillbaka från Security Center innehåller:

- [Instrumentpanelen för händelser](#menu_events)
- [Sök menypost](#menu_search)
- [Visa klassisk identitet och åtkomst länk på identitet och åtkomst (förhandsversion)](#menu_classicidentity)
- [Säkerhetshändelser mappa knappen säkerhetskarta aviseringar (förhandsversion)](#menu_securityeventsmap)
- [Anpassade aviseringsregler (förhandsversion)](#menu_customalerts)
- [Undersöka knappen i threat protection säkerhetsaviseringar](#menu_investigate)
- [En delmängd av säkerhetslösningar](#menu_solutions)
- [Redigera säkerhetskonfigurationerna för säkerhetsprinciper](#menu_securityconfigurations)
- [Säkerhet och granskning instrumentpanelen (ursprungligen används i OMS-portalen) för Log Analytics-arbetsytor.](#menu_securityomsdashboard)

Den nedan finns detaljerad information för varje tillbakadragna funktion och de steg du kan vidta med hjälp av funktioner för ersättning.

## Instrumentpanelen för händelser<a name="menu_events"></a>
Security Center använder Microsoft Monitoring Agent att samla in olika säkerhetsrelaterade konfigurationer och händelser från dina virtuella datorer och lagrar dessa händelser i dina arbetsytor. Den [instrumentpanelen för händelser](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) kan du visa dessa data och ger i princip en annan startpunkt till Log Analytics.

Vi rekommenderar att instrumentpanelen för händelser kommer att dras tillbaka:

![Händelser urvalsskärm för arbetsytan][1]

Instrumentpanelen för händelser som visas när användaren klickar på en arbetsyta, kommer också att dras tillbaka:

![Instrumentpanelen för händelser][2]

### <a name="events-dashboard---new-experience"></a>Instrumentpanelen för händelser – nya upplevelsen

Kunder uppmuntras att använda Log Analytics inbyggda funktioner för att visa viktiga händelser på sina arbetsytor.
Om du redan har skapat anpassade viktiga händelser från Security Center kan dessa är tillgängliga via Log analytics -> Välj Arbetsyta -> sparade sökningar. Dina data inte tappas bort eller ändras. Interna viktiga händelser är också tillgängliga från samma skärm.

![Arbetsytan sparade sökningar][3]

## Sök menypost<a name="menu_search"></a>
Azure Security Center använder för närvarande Azure Monitor-loggar search för att hämta och analysera dina säkerhetsdata. Den här skärmen i stort sett fungerar som en fasad för Log Analytics ”[Search](https://docs.microsoft.com/azure/security-center/security-center-search)” sidan – tillåter att användare kör sökfrågor på den valda arbetsytan. Vi rekommenderar att den här sökfönstret kommer att dras tillbaka:

![Sidan objektsökning][4]

### <a name="search-menu-entry---new-experience"></a>Sök menypost - nya upplevelsen

Uppmuntras kunderna att använda **Log Analytics** inbyggda funktioner för att utföra sökningar på sina arbetsytor. Om du vill göra det, kan de gå till Log analytics i Azure och välja: ”Loggar”:

![Sidan för log Analytics-loggar][5]

## Klassisk identitet och åtkomst (förhandsversion)<a name="menu_classicidentity"></a>
”Klassisk” upplevelse för identitet och åtkomst i Security Center hade tillhandahålla ett sätt för kunderna att visa sin identitet och åtkomst till relaterad information i log analytics. Detta gjordes genom att följa klick nedan:

Klicka på ”Visa klassisk identitet och åtkomst”

![Sidan][6]

Tillsammans med den här sidan öppnas skärmen ”instrumentpanelen identitet och åtkomst”:

![Sidan - val av arbetsyta][7]

Klicka på arbetsytan öppnar ”identitets- och åtkomsthantering” log analytics instrumentpanelen där kunder kan visa identitet och åtkomst till information på arbetsytan:

![Sidan - instrumentpanel][8]

Vi rekommenderar att alla tre skärmar ovan kommer att dras tillbaka. Dina data ska förblir tillgängliga i log analytics-säkerhetslösning och inte ändras eller tas bort.
De ursprungliga Log Analytics-frågor som används för att fylla instrumentpanelen identitet och åtkomst finns i Security Center [GitHub-lagringsplatsen](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards).

### <a name="classic-identity--access-preview---new-experience"></a>Klassisk identitet och åtkomst (förhandsversion), nya upplevelsen
Även om Log analytics-instrumentpanelen har tillhandahållit insikter på en viss arbetsyta, de inbyggda funktionerna i Security Center tillhandahåller insyn i alla prenumerationer och alla arbetsytor som är associerade med dem, i ett enkelt sätt – att använda vy där du kan fokusera på vad viktigt, enligt den säkra poängen för din identitet och åtkomst rekommendationer.
Alla funktioner i identitets- och åtkomsthantering Log analytics-instrumentpanelen kan nås genom att välja ”identitet och åtkomst (förhandsgranskning)” i Security Center:

![Identitet sidan – klassiska uppleva pensionering][9]

## Mappa säkerhetshändelser<a name="menu_securityeventsmap"></a>
Security Center kan du med en [kartan](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) som hjälper dig identifiera säkerhetshot mot miljön. Knappen ”Gå till händelser säkerhetskarta' i som mappar leder till en instrumentpanel som gör för att visa rå säkerhetshändelser på den valda arbetsytan.
Knappen, tillsammans med per arbetsyta-instrumentpanelen tas bort efter utfasningen.

![Security aviseringskarta - knappen][10]

När du klickar på ”Gå till händelser säkerhetskarta” öppnas idag instrumentpanelen för hotinformation. Instrumentpanelen för hotinformation kommer att dras tillbaka.  

![Instrumentpanelen för hotinformation][11]

När du väljer en arbetsyta för att visa dess instrumentpanel för hotinformation säkerhetsvarningar map(Preview) skärmen *i Log Analytics* öppnas. Den här skärmen kommer att dras tillbaka.

![Säkerhetskarta för aviseringar i Log Analytics][12]

Dina befintliga data ska förblir tillgängliga i log analytics-säkerhetslösning och inte ändras eller tas bort.
De ursprungliga Log Analytics-frågor som används för att fylla i instrumentpanelen för Hotinformation som är tillgängliga i Security Center [GitHub-lagringsplatsen](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards).

### <a name="security-events-map---new-experience"></a>Händelser säkerhetskarta - nya upplevelsen
Vi rekommenderar våra kunder att använda funktionen aviseringar kartan är inbyggda i Security Center – ”säkerhetsaviseringar mappa (förhandsversion)”. Detta ger en optimerad upplevelse och fungerar över alla prenumerationer och associerade arbetsytor, så att ett makro visa hela din miljö och inte fokusera på en enda arbetsyta.

## Anpassade aviseringsregler (förhandsversion)<a name="menu_customalerts"></a>
Den anpassade aviseringar Experience [dras tillbaka](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) den 30 juni 2019 på grund av tillbakadragning av den underliggande infrastrukturen den är byggd på. I tidsramen tills utfasningen användare kommer att kunna redigera befintliga anpassade aviseringsregler men kommer inte att kunna lägga till nya. Användare bör aktivera [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) med ett klick onboarding att automatiskt migrera sina befintliga aviseringar och skapa nya eller också återskapa sina aviseringar med Azure Monitor loggaviseringar.

Om du vill behålla dina befintliga aviseringar och migrera dem till Azure Sentinel, starta Sentinel-Azure. Välj arbetsytan där din anpassade aviseringar lagras som första steg och välj sedan menyalternativet 'Analytics' för att automatiskt migrera dina aviseringar.

![Anpassade aviseringar][13]

Kunder som inte vill Kom igång med Azure Sentinel uppmuntras att återskapa sina aviseringar med Azure Monitor log-aviseringar. Mer information finns på: [skapa, visa och hantera aviseringar med Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log). Anvisningar för hur du skapar aviseringar, se: [Loggaviseringar i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

Mer information om tillbakadragande av anpassade aviseringar finns i den [Security Center anpassade aviseringar dokumentation](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Säkerhetsundersökning för aviseringar<a name="menu_investigate"></a>
[Undersökningsfunktionen](https://docs.microsoft.com/azure/security-center/security-center-investigation) i Security Center kan du sortera, Förstå omfattningen och spåra rotorsaken till en potentiell säkerhetsincident. Den här funktionen tas bort från Security Center som den har ersatts med en bättre upplevelse i [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/).

![Säkerhetsincident][14]

När du klickar på knappen ”Undersök”, öppnas ”(förhandsversion)” i Log Analytics Undersökningsinstrumentpanelen. Instrumentpanel för undersökning kommer att dras tillbaka.  
Dina befintliga data ska förblir tillgängliga i Log Analytics-lösningen för säkerhet och inte ändras eller tas bort.

![Instrumentpanel för undersökning i Log Analytics][15]

### <a name="investigation---new-experience"></a>Undersökning – nya upplevelsen

Vi rekommenderar våra kunder att publicera [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) drivs av aviseringarna jakt funktionen för en omfattande felsökningsfunktionen. Azure Sentinel innehåller kraftfulla jakt Sök och fråga i Verktyg för jakt efter säkerhetshot via din organisations datakällor.  

## Delmängd av säkerhetslösningar<a name="menu_solutions"></a>

Security Center har möjlighet att aktivera [integrerade säkerhetslösningar i Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). Följande partnerlösningar skulle tas bort och stöds i [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/), tillsammans med ännu fler datakällor.

- Nästa generations brandvägg och Web application Firewall lösningar (Azure Sentinel [dokumentation](https://docs.microsoft.com/azure/sentinel/connect-data-sources))
- Integrering av säkerhetslösningar som stöder Common event format (CEF) (Azure Sentinel [dokumentation](https://docs.microsoft.com/azure/sentinel/connect-common-event-format))
- Microsoft Advanced Threat Analytics (Azure Sentinel [dokumentation](https://docs.microsoft.com/azure/sentinel/connect-azure-atp))
- Azure AD Identity Protection (Azure Sentinel [dokumentation](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection))

Följande utfasningen, kommer användare inte att kunna lägga till nya eller ändra befintliga anslutna lösningar av de typer som nämns ovan, från både Användargränssnittet och API: et.
Befintliga anslutna lösningar uppmuntras att flytta till Azure Sentinel i slutet av den här perioden.

![Security centers lösningar][16]

## Redigera säkerhetskonfigurationerna för säkerhetsprinciper<a name="menu_securityconfigurations"></a>
Azure Security Center övervakar säkerhetskonfigurationer genom att använda en uppsättning [över 150 rekommenderas regler](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) för att härda Operativsystemet, inklusive regler relaterade till brandväggar, granskning, lösenordsprinciper med mera. Om en dator har en sårbara konfiguration, genererar Security Center en säkerhetsrekommendation. Den [redigeringsskärmen security configuration](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) gör att kunder kan anpassa den OS standardkonfigurationen för säkerhet i Security Center.

Den här funktionen har varit i förhandsversion och kommer att dras tillbaka.

![Redigera säkerhetskonfigurationer][17]

### <a name="edit-security-configurations---new-experience"></a>Redigera säkerhetskonfigurationerna - nya upplevelsen

Security Center har stöd för den [configuration gästagenten](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) inom en snar framtid, vilket gör att en mycket mer omfattande funktionsuppsättning – bland annat stöd för ytterligare operativsystem och integrering med gäst-konfiguration med Azure-principer ( i gäst-principer). Detta ger också möjlighet att styra i stor skala och tillämpa på nya resurser automatiskt.

## Säkerhet och granskning instrumentpanelen (ursprungligen används i OMS-portalen) för Log Analytics-arbetsytor<a name="menu_securityomsdashboard"></a>

Security-instrumentpanelen i Log analytics innehåller en per arbetsyta-översikt över viktiga säkerhetshändelser och hot, en karta för hotinformation och utvärdering av identitet och åtkomst för säkerhetshändelser som sparas i arbetsytan. Instrumentpanelen tas bort framöver. Som vi redan rekommenderas i instrumentpanelen Användargränssnittet, bör våra användare använda Azure Security Center framöver.

![Log analytics security-instrumentpanelen][18]

### <a name="security--audit-dashboard---new-experience"></a>Säkerhet och granskning instrumentpanel – nya upplevelsen
Våra kunder är bäst att använda Azure Security Center, som ger samma Säkerhetsöversikt över flera prenumerationer och arbetsytor som är associerade med dem, tillsammans med en mer omfattande funktionsuppsättning.

De ursprungliga Log Analytics-frågor som används för att fylla i säkerheten och granska instrumentpanelen är tillgängliga i Security Center [GitHub-lagringsplatsen](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards).

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/)
- Läs mer om [Sentinel-Azure](https://docs.microsoft.com/azure/sentinel)

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
