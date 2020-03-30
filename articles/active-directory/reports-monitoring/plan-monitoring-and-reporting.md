---
title: Planera rapporter & övervakningsdistribution - Azure AD
description: Beskriver hur du planerar och utför implmentation av rapportering och övervakning.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad84b8910e8d4f8af9845c33c22d128e317dedc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232109"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Planera en Azure Active Directory-rapportering och övervakningsdistribution

Rapporterings- och övervakningslösningen för Azure Active Directory (Azure AD) beror på dina juridiska, säkerhets- och driftskrav och din befintliga miljö och processer. I den här artikeln visas de olika designalternativen och du får rätt distributionsstrategi.

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Fördelar med Azure AD-rapportering och övervakning

Azure AD-rapportering ger en omfattande vy och loggar över Azure AD-aktivitet i din miljö, inklusive inloggningshändelser, granskningshändelser och ändringar i katalogen.

Med dessa data kan du:

* avgöra hur dina appar och tjänster används.

* upptäcka potentiella risker som påverkar hälsan i din miljö.

* felsöka problem som hindrar användarna från att få jobbet gjort.

* få insikter genom att se granskningshändelser för ändringar i din Azure AD-katalog.

> [!IMPORTANT]
> Azure AD-övervakning gör att du kan dirigera dina loggar som genereras av Azure AD-rapportering till olika målsystem. Du kan antingen behålla dem för långvarig användning eller integrera dem med SIEM-verktyg (säkerhetsinformation och händelsehantering) för att få insikter om din miljö.

Med Azure AD-övervakning kan du dirigera loggar till:

* ett Azure-lagringskonto för arkivering.
* Azure Monitor-loggar, tidigare känd som Azure Log Analytics-arbetsyta, där du kan analysera data, skapa instrumentpaneler och avisera om specifika händelser.
* en Azure-händelsehubb där du kan integrera med dina befintliga SIEM-verktyg som Splunk, Sumologic eller QRadar.

> [!NOTE]
Vi har nyligen börjat använda termen Azure Monitor-loggar i stället för Logganalys. Loggdata lagras fortfarande i en Log Analytics-arbetsyta och samlas fortfarande in och analyseras av samma Log Analytics-tjänst. Vi uppdaterar terminologin för att bättre återspegla rollen för [loggar i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection). Mer information finns i [Azure Monitor-terminologiändringar.](https://docs.microsoft.com/azure/azure-monitor/azure-monitor-rebrand)

[Läs mer om principer för rapportlagring](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention).

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Licensiering och förutsättningar för Azure AD-rapportering och övervakning

Du behöver en Azure AD-premiumlicens för att komma åt Azure AD-inloggningsloggarna.

Detaljerad information om funktioner och licensiering i [Azure Active Directory-prisguiden](https://azure.microsoft.com/pricing/details/active-directory/).

För att distribuera Azure AD-övervakning och rapportering behöver du en användare som är global administratör eller säkerhetsadministratör för Azure AD-klienten.

Beroende på loggdatas slutdestination behöver du något av följande:

* Ett Azure-lagringskonto som du har ListKeys-behörigheter för. Vi rekommenderar att du använder ett allmänt lagringskonto och inte ett blob-lagringskonto. Prisinformation för lagring finns i [priskalkylatorn för Azure Storage](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Ett Azure Event Hubs-namnområde som ska integreras med SIEM-lösningar från tredje part.

* En Azure Log Analytics-arbetsyta för att skicka loggar till Azure Monitor-loggar.

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Planera ett Azure-rapporterings- och övervakningsdistributionsprojekt

I det här projektet definierar du de målgrupper som ska använda och övervakar rapporter och definierar din Azure AD-övervakningsarkitektur.

### <a name="engage-the-right-stakeholders"></a>Engagera rätt intressenter

När teknikprojekt misslyckas gör de det vanligtvis på grund av felaktiga förväntningar på påverkan, resultat och ansvar. För att undvika dessa fallgropar, [se till att du engagerar rätt intressenter.](https://aka.ms/deploymentplans) Se också till att intressentrollerna i projektet förstås väl genom att dokumentera intressenterna och deras projektinmatning och kontobesiktning.

### <a name="plan-communications"></a>Planera kommunikation

Kommunikation är avgörande för att en ny tjänst ska lyckas. Kommunicera proaktivt med användarna hur deras upplevelse kommer att förändras, när den ändras och hur de får support om de får problem.

### <a name="document-your-current-infrastructure-and-policies"></a>Dokumentera din nuvarande infrastruktur och dina aktuella principer

Din nuvarande infrastruktur och dina nuvarande principer kommer att driva din rapporterings- och övervakningsdesign. Se till att du vet

* Vad, om någon, SIEM verktyg du använder.

* Din Azure-infrastruktur, inklusive befintliga lagringskonton och övervakning som används.

* Dina organisationslagringsprinciper för loggar, inklusive tillämpliga efterlevnadsramverk som krävs. 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Planera en Azure AD-rapportering och övervakningsdistribution

Rapportering och övervakning används för att uppfylla dina affärskrav, få insikter om användningsmönster och öka organisationens säkerhetsposition.

### <a name="business-use-cases"></a>Ärenden om användning i företag

* Krävs för lösning för att möta affärsbehov
* Skönt att behöva möta affärsbehov
* Inte tillämpligt

|Område |Beskrivning |
|-|-|
|Kvarhållning| **Logga kvarhållning på mer än 30 dagar**. På grund av juridiska krav eller affärskrav krävs för att lagra granskningsloggar och logga in loggar för Azure AD längre än 30 dagar. |
|Analys| **Loggarna måste vara sökbara**. De lagrade loggarna måste vara sökbara med analytiska verktyg. |
| Operational Insights| **Insikter för olika team**. Behovet av att ge tillgång för olika användare att få operativa insikter såsom programanvändning, inloggningsfel, självbetjäningsanvändning, trender, etc. |
| Säkerhetsinsikter| **Insikter för olika team**. Behovet av att ge tillgång för olika användare att få operativa insikter såsom programanvändning, inloggningsfel, självbetjäningsanvändning, trender, etc. |
| Integration i SIEM-system      | **SIEM integration**. Behovet av att integrera och strömma Azure AD-inloggningsloggar och granskningsloggar till befintliga SIEM-system. |

### <a name="choose-a-monitoring-solution-architecture"></a>Välj en övervakningslösningsarkitektur

Med Azure AD-övervakning kan du dirigera dina Azure AD-aktivitetsloggar till ett system som bäst uppfyller dina affärsbehov. Du kan sedan behålla dem för långsiktig rapportering och analys för att få insikter i din miljö och integrera den med SIEM-verktyg.

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>Beslutsflödesdiagram![En bild som visar vad som beskrivs i efterföljande avsnitt](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>Arkivera loggar in i ett lagringskonto

Genom att dirigera loggar till ett Azure-lagringskonto kan du behålla dem längre än den standardlagringsperiod som beskrivs i våra [bevarandeprinciper](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention). Använd den här metoden om du behöver arkivera dina loggar, men inte behöver integrera dem med ett SIEM-system och inte behöver pågående frågor och analyser. Du kan fortfarande göra sökningar på begäran.

Lär dig hur du [dirigerar data till ditt lagringskonto](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account).

#### <a name="send-logs-to-azure-monitor-logs"></a>Skicka loggar till Azure Monitor-loggar

[Azure Monitor-loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) konsoliderar övervakningsdata från olika källor. Det ger också en frågespråk och analysmotor som ger dig insikter om hur dina program fungerar och använder resurser. Genom att skicka Azure AD-aktivitetsloggar till Azure Monitor-loggar kan du snabbt hämta, övervaka och avisera dig om insamlade data. Använd den här metoden när du inte har en befintlig SIEM-lösning som du vill skicka dina data till direkt men vill ha frågor och analyser. När dina data finns i Azure Monitor-loggar kan du sedan skicka dem till händelsehubben och därifrån till ett SIEM om du vill.

Lär dig att [skicka data till Azure Monitor-loggar](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Du kan också installera de färdiga vyerna för Azure AD-aktivitetsloggar för att övervaka vanliga scenarier som involverar inloggnings- och granskningshändelser.

Lär dig att [installera och använda logganalysvyer för Azure AD-aktivitetsloggar](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views).

#### <a name="stream-logs-to-your-azure-event-hub"></a>Strömma loggar till din Azure-händelsehubb

Routningsloggar till en Azure-händelsehubb möjliggör integrering med SIEM-verktyg från tredje part. Den här integrationen gör att du kan kombinera Azure AD-aktivitetsloggdata med andra data som hanteras av ditt SIEM, för att ge bättre inblick i din miljö. 

Lär dig hur du [skickar händelser till en händelsehubb](https://docs.microsoft.com//azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub).

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Planera åtgärder och säkerhet för Azure AD-rapportering och övervakning

Intressenter måste komma åt Azure AD-loggar för att få operativa insikter. Troliga användare inkluderar medlemmar i säkerhetsgruppen, interna eller externa revisorer och identitets- och åtkomsthanteringsgruppen.

Med Azure AD-roller kan du delegera möjligheten att konfigurera och visa Azure AD-rapporter baserat på din roll. Identifiera vem i organisationen som behöver behörighet att läsa Azure AD-rapporter och vilken roll som skulle vara lämplig för dem. 

Följande roller kan läsa Azure AD-rapporter:

* Global administratör

* Säkerhetsadministratör

* Säkerhetsläsare

* Rapportläsare

Läs mer om [administrativa Azure AD-roller](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

*Tillämpa alltid begreppet lägsta behörighet för att minska risken för en kontokompromiss*. Överväg att implementera [privilegierad identitetshantering](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) för att ytterligare skydda din organisation.

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Distribuera Azure AD-rapportering och övervakning

Beroende på vilka beslut du har fattat tidigare med hjälp av designvägledningen ovan kommer det här avsnittet att guida dig till dokumentationen om de olika distributionsalternativen.

### <a name="consume-and-archive-azure-ad-logs"></a>Använda och arkivera Azure AD-loggar

[Hitta aktivitetsrapporter på Azure-portalen](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-find-activity-reports)

[Arkivera Azure AD-loggar till ett Azure Storage-konto](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account)

### <a name="implement-monitoring-and-analytics"></a>Implementera övervakning och analys

[Skicka loggar till Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

[Installera och använda logganalysvyerna för Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)

[Analysera Azure AD-aktivitetsloggar med Azure Monitor-loggar](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

* [Tolka granskningsloggschema i Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

* [Tolka inloggningsloggschema i Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema)

 * [Strömma Azure AD-loggar till en Azure-händelsehubb](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)

* [Integrera Azure AD-loggar med Splunk med hjälp av Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-integrate-activity-logs-with-splunk)

* [Integrera Azure AD-loggar med SumoLogic med hjälp av Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic)

 

 

## <a name="next-steps"></a>Nästa steg

Överväg att implementera [privilegierad identitetshantering](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) 

Överväg att implementera [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview)

 
