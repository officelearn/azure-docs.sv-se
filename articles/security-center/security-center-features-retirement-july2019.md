---
title: Tillbakadragning av Security Center-funktioner (juli 2019) | Microsoft Docs
description: Den här artikeln beskriver funktionerna i Security Center som kommer att dras tillbaka den 31 juli 2019.
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 1d364da9506124a35c724209c68ff72db4243e80
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341561"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Tillbakadragning av Security Center-funktioner (juli 2019)

Vi har gjort flera [förbättringar](https://azure.microsoft.com/updates/?product=security-center) till Azure Security Center under de senaste sex månaderna.
Med de förbättra funktionerna, vi ska ta bort vissa redundant funktioner och relaterade API: er från Security Center 31 juli 2019.  

De flesta av funktionerna retiring kan ersättas med nya funktioner i Azure Security Center eller Azure Log Analytics. Andra funktioner kan implementeras med hjälp av [Azure Sentinel (förhandsversion)](https://azure.microsoft.com/services/azure-sentinel/).

Security Center-funktioner kommer att dras in:

- [Instrumentpanelen för händelser](#menu_events)
- [Sök menypost](#menu_search)
- [Visa klassisk identitet och åtkomst länk på identitet och åtkomst (förhandsversion)](#menu_classicidentity)
- [Säkerhetshändelser mappa knappen säkerhetskarta aviseringar (förhandsversion)](#menu_securityeventsmap)
- [Anpassade aviseringsregler (förhandsversion)](#menu_customalerts)
- [Undersöka knappen i threat protection säkerhetsaviseringar](#menu_investigate)
- [En delmängd av säkerhetslösningar](#menu_solutions)
- [Redigera säkerhetskonfigurationerna för säkerhetsprinciper](#menu_securityconfigurations)
- [Säkerhet och granskning instrumentpanelen (ursprungligen används i OMS-portalen) för Log Analytics-arbetsytor](#menu_securityomsdashboard)

Den här artikeln innehåller detaljerad information om varje tillbakadragna funktion och steg du kan vidta för att implementera ersättning funktioner.

## Instrumentpanelen för händelser<a name="menu_events"></a>

Security Center använder Microsoft Monitoring Agent för att samla in olika säkerhetsrelaterade konfigurationer och händelser från dina virtuella datorer. Dessa händelser lagras i dina arbetsytor. Den [instrumentpanelen för händelser](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) kan du visa dessa data och ger en startpunkt till Log Analytics.

Vi ska pensionera instrumentpanelen för händelser som visas när du väljer en arbetsyta:

![Instrumentpanelen för händelser][2]

### <a name="events-dashboard---the-new-experience"></a>Instrumentpanelen för händelser – den nya upplevelsen

Vi gärna att du kan använda de inbyggda funktionerna i Azure Log Analytics för att visa viktiga händelser på dina arbetsytor.

Om du har skapat anpassade viktiga händelser i Security Center kan vara de tillgänglig. I Log Analytics, går du till **Välj arbetsyta** > **sparade sökningar**. Dina data inte tappas bort eller ändras. Interna viktiga händelser är också tillgängliga från samma skärm i Log Analytics.

![Arbetsytan sparade sökningar][3]

## Sök menypost<a name="menu_search"></a>

Azure Security Center använder för närvarande Azure Monitor-loggar search för att hämta och analysera dina säkerhetsdata. Den här skärmen fungerar som ett fönster för att Log Analytics-söksidan och gör att användarna kan köra sökfrågor på den valda arbetsytan. Mer information finns i [Azure Security Center search](https://docs.microsoft.com/azure/security-center/security-center-search). Vi ska pensionera den här sökfönstret:

![Sidan objektsökning][4]

### <a name="search-menu-entry---the-new-experience"></a>Sök menypost – den nya upplevelsen

Vi rekommenderar att du kan använda de inbyggda funktionerna i Azure Log Analytics för att utföra sökningar på dina arbetsytor. Gå till Azure Log Analytics och välj **loggar**.

![Sidan för log Analytics-loggar][5]

## Klassisk identitet och åtkomst (förhandsversion)<a name="menu_classicidentity"></a>

Den klassiska identitet och åtkomst-upplevelsen i Security Center visar för närvarande en instrumentpanel för identitets- och information i Log Analytics. Visa den här instrumentpanelen:

1. Välj **Visa klassisk identitet och åtkomst**.

   ![Sidan][6]

1. Visa den **identitets- och åtkomstinstrumentpanel**.

    ![Sidan - val av arbetsyta][7]

1. Välj en arbetsyta för att öppna den **identitet och åtkomst** instrumentpanelen i Log Analytics för att visa identitet och åtkomst till information för din arbetsyta.

   ![Sidan - instrumentpanel][8]

Vi ska pensionera alla tre skärmar som visas i föregående steg. Dina data förblir tillgängliga i Log Analytics-lösningen för säkerhet och inte ändras eller tas bort.

### <a name="classic-identity--access-preview---the-new-experience"></a>Klassisk identitet och åtkomst (förhandsversion) – den nya upplevelsen

Log Analytics-instrumentpanelen har visat insikter på en enda arbetsyta. Dock ger inbyggda funktionerna i Security Center insyn i alla prenumerationer och alla arbetsytor som är kopplade. Du kan komma åt ett enkelt sätt – att använda vy där du kan fokusera på vad som är viktigt med rekommendationer som rangordnas utifrån deras säker poäng.

Alla funktioner i den **identitet och åtkomst** instrumentpanelen i Log Analytics kan nås genom att välja **identitet och åtkomst (förhandsversion)** i Security Center.

![Identitet sidan – klassiska uppleva pensionering][9]

## Mappa säkerhetshändelser<a name="menu_securityeventsmap"></a>

Security Center kan du med en [aviseringar säkerhetskarta](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) för att identifiera säkerhetshot. Den **går du till händelser säkerhetskarta** som mappar knappen öppnas en instrumentpanel där du kan visa rå säkerhetshändelser på den valda arbetsytan.

Vi ska ta bort den **går du till händelser säkerhetskarta** knappen och instrumentpanelen per arbetsyta.

![Security aviseringskarta - knappen][10]

När du väljer den **går du till händelser säkerhetskarta** knappen du öppnar instrumentpanelen för hotinformation. Vi ska pensionera instrumentpanelen för hotinformation.  

![Instrumentpanelen Hotinformation][11]

När du väljer en arbetsyta för att visa dess instrumentpanel för hotinformation kan öppna du skärmen security aviseringar karta (förhandsgranskning) i Log Analytics. Vi ska pensionera den här skärmen.

![Säkerhetskarta för aviseringar i Log Analytics][12]

Dina befintliga data förblir tillgängliga i Log Analytics-lösningen för säkerhet och inte ändras eller tas bort.

### <a name="security-events-map---the-new-experience"></a>Händelser säkerhetskarta – den nya upplevelsen

Vi rekommenderar att du kan använda kartan funktionerna aviseringar i Security Center: **Säkerhetskarta aviseringar (förhandsversion)** . Den här funktionen ger en optimerad upplevelse och fungerar på alla prenumerationer och associerade arbetsytor. Det ger dig en översikt över din miljö och är inte fokuserar på en enda arbetsyta.

## Anpassade aviseringsregler (förhandsversion)<a name="menu_customalerts"></a>

Vi [tas ur bruk anpassat aviseringar upplevelse](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) den 30 juni 2019 eftersom tas ur bruk den underliggande infrastrukturen. Du kan redigera befintliga anpassade aviseringsregler fram till dess, men du inte kan komma att lägga till nya. Efter datumet för tillbakadragandet kommer alla anpassade aviseringar som definierats börjar inte gälla och säkerhetsaviseringar baserat på de här reglerna kommer att genereras.
Vi rekommenderar att du aktiverar [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) och återskapa dina anpassade aviseringar. Alternativt kan du skapa dina aviseringar med Azure Monitor log-aviseringar.

Att hålla dina befintliga aviseringar och skapa dem med Azure Sentinel:

1. [Öppna Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview) och välj arbetsytan där din anpassade aviseringar lagras
1. Välj **Analytics** från menyn
1. Följ instruktionerna i följande [självstudien](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) om hur du skapar anpassade aviseringar i Azure Sentinel

Om du inte vill använda Azure Sentinel kan du skapa dina aviseringar med loggaviseringar i Azure Monitor. Anvisningar finns i [skapa, visa och hantera aviseringar med hjälp av Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) och [Loggaviseringar i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

![Anpassade aviseringar][13]

Mer information om anpassade aviseringar tillbakadragande finns [anpassade Aviseringsregler i Azure Security Center (förhandsversion)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Säkerhetsundersökning för aviseringar<a name="menu_investigate"></a>

[Undersökningsfunktionen](https://docs.microsoft.com/azure/security-center/security-center-investigation) i Security Center kan du sortera en potentiell säkerhetsincident. Funktionen kan du förstår omfattningen av en incident och spåra grundorsaken. Vi ska ta bort den här funktionen från Security Center eftersom den har ersatts med en bättre upplevelse i [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/).

![Säkerhetsincident][14]

När du väljer den **Undersök** knappen från en **säkerhetsincident** kan du öppna instrumentpanelen undersökning (förhandsversion) i Log Analytics. Vi ska pensionera Undersökningsinstrumentpanelen.  

Dina befintliga data förblir tillgängliga i Log Analytics-lösningen för säkerhet och inte ändras eller tas bort.

![Instrumentpanel för undersökning i Log Analytics][15]

### <a name="investigation---the-new-experience"></a>Undersökning – den nya upplevelsen

Vi rekommenderar att du skulle övergå till [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) för en omfattande felsökningsfunktionen. Azure Sentinel innehåller kraftfulla Sök och fråga i Verktyg för jakt efter säkerhetshot via din organisations datakällor.  

## Delmängd av säkerhetslösningar<a name="menu_solutions"></a>

Security Center kan aktivera [integrerade säkerhetslösningar i Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). Vi ska pensionera följande partnerlösningar från Security Center. De här lösningarna är aktiverade i [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) tillsammans med ett antal ytterligare datakällor.

- [Nästa generations brandvägg och web brandväggen programlösningar](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Integrering av säkerhetslösningar som stöder Common Event Format (CEF)](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Du kommer inte kunna lägga till eller ändra någon av Lösningstyper som nämns i listan ovan, antingen från Användargränssnittet eller API: et efter dras tillbaka.

Om du har befintliga anslutna lösningar, rekommenderar vi att du kan flytta till Azure Sentinel.

![Security centers lösningar][16]

## Redigera säkerhetskonfigurationerna för säkerhetsprinciper<a name="menu_securityconfigurations"></a>

Azure Security Center övervakar säkerhetskonfigurationer genom att använda en uppsättning [över 150 rekommenderas regler](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). för att härda Operativsystemet. Dessa regler gäller brandväggar, granskning, lösenordsprinciper med mera. Om en sårbar konfiguration hittas på en dator, genererar Security Center en säkerhetsrekommendation. Den [redigeringsskärmen security configuration](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) gör att kunder kan anpassa den OS standardkonfigurationen för säkerhet i Security Center.

Vi ska pensionera den här funktionen för förhandsgranskning. Om du vill återställa din säkerhetskonfigurationer till sina standardvärden för efter datumet för tillbakadragandet kan du göra det via API eller Powershell med hjälp av den [instruktioner](https://aka.ms/ascresetsecurityconfigurations)

![Redigera säkerhetskonfigurationerna][17]

### <a name="edit-security-configurations---the-new-experience"></a>Redigera säkerhetskonfigurationerna – den nya upplevelsen

Vi planerar att aktivera Security Center att stödja den [configuration gästagenten](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration). Sådan uppdatering kan en mycket mer omfattande funktionsuppsättning, inklusive stöd för flera operativsystem och integration av Azure i gäst-principer för gästen konfigurationer. När ändringarna är aktiverade, har du också möjlighet att styra konfigurationer i stor skala och tillämpa dem automatiskt till nya resurser.

## Instrumentpanelen för säkerhet och granskning för Log Analytics-arbetsytor<a name="menu_securityomsdashboard"></a>

Instrumentpanelen för säkerhet och granskning användes i OMS-portalen. I Log Analytics ger instrumentpanelen en per arbetsyta-översikt över viktiga säkerhetshändelser och hot, en karta för hotinformation och en identitet och åtkomst-bedömning av säkerhetshändelser som sparats i arbetsytan. Vi ska ta bort instrumentpanelen. Som vi redan rekommenderas i instrumentpanelen Användargränssnittet, rekommenderar vi som du skulle övergå till Azure Security Center.

![Log Analytics security-instrumentpanelen][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Säkerhet och granskning instrumentpanel – den nya upplevelsen

Vi rekommenderar att du kan växla till Azure Security Center. Det ger samma Säkerhetsöversikt över flera prenumerationer och arbetsytor som är associerade med dem, samt en mer omfattande funktionsuppsättning.

Du kan hämta Log Analytics-frågor som fyller på instrumentpanelen för säkerhet och granskning i den [GitHub-lagringsplatsen](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) för Security Center.

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
