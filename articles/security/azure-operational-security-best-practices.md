---
title: Metodtips för Azure driftsäkerhet | Microsoft Docs
description: Den här artikeln innehåller en uppsättning Metodtips för driftsäkerhet för Azure.
services: security
documentationcenter: na
author: unifycloud
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: d8f6ad48c62ff2021c91e593cee44dd6f5551247
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297917"
---
# <a name="azure-operational-security-best-practices"></a>Metodtips för Azure driftsäkerhet
Azure driftsäkerhet refererar till de tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda sina data, program och andra resurser i Microsoft Azure. Azure driftsäkerhet bygger på ett ramverk som innehåller den kunskap som olika funktioner som är unika för Microsoft, inklusive på Microsoft Security Development Lifecycle (SDL), Microsoft Security Response Center-program och djup medvetenhet om hotlandskapet.

I den här artikeln diskuterar vi en samling med Azure database säkerhetsmetoder. Dessa metodtips härleds från vår erfarenhet med Azure database-säkerhet och erfarenheter från kunder som dig själv.

För varje rekommenderar förklarar vi:
-   Vad den bästa metoden är
-   Varför du vill aktivera den bästa praxis
-   Vad kan vara resultatet om du inte aktivera den bästa metoden
- Hur du kan lära dig att aktivera ett metodtips

Den här artikeln för Azure Operational säkerhetsmetoder baseras på en konsensus-åsikter och funktioner för Azure-plattformen och funktioner, eftersom de finns när den här artikeln skrevs. Andras åsikter och tekniker som ändras med tiden och den här artikeln kommer att uppdateras regelbundet att återspegla dessa ändringar.

Driftsäkerhet Metodtips för Azure beskrivs i den här artikeln är:

-   Övervaka, hantera och skydda molninfrastruktur
-   Hantera identitet och implementera enkel inloggning (SSO)
-   Spåra förfrågningar, analysera användningstrender och diagnostisera problem
-   Övervaka tjänster med en centraliserad övervakningslösning
-   Förhindra, upptäcka och svara på hot
-   Slutpunkt till slutpunkt scenariobaserade nätverksövervakning
-   Säker distribution med beprövade DevOps-verktyg

## <a name="monitor-manage-and-protect-cloud-infrastructure"></a>Övervaka, hantera och skydda molninfrastruktur
IT-avdelningen ansvarar för att hantera infrastruktur med datacenter, program och data, inklusive stabilitet och säkerheten för dessa system. Informationshämtning säkerhet över ökar komplexa IT-miljöer ofta kräver dock organisationer att cobble ihop data från flera system för säkerhet och hantering.

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) är Microsofts molnbaserade IT-hanteringslösning som hjälper dig att hantera och skydda dina lokala och molnbaserade infrastruktur.

[OMS säkerhet och Granskningslösning](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) möjliggör för IT att aktivt övervakar alla resurser som kan bidra till att minimera effekten av incidenter. Säkerhets- och Granskningslösningen har säkerhetsdomäner som kan användas för att övervaka resurser.

Mer information om OMS finns i artikeln [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

För att förhindra, upptäcka och svara på hot genom [Operations Management Suite (OMS) säkerhets- och Granskningslösningen](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) samlar in och bearbeta data om dina resurser, vilket innefattar:

-   Säkerhetshändelselogg
-   ETW-händelser (Event Tracing for Windows)
-   AppLocker-granskningshändelser
-   Windows-brandväggslogg
-   Advanced Threat Analytics-händelser
-   Resultat från utvärdering av säkerhetsbaslinje
-   Resultat från utvärdering av program mot skadlig kod
-   Resultat från utvärdering av uppdateringar/korrigeringar
-   Syslog-dataströmmar som uttryckligen har aktiverats på agenten


## <a name="manage-identity-and-implement-single-sign-on"></a>Hantera identitet och implementera enkel inloggning
[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) är Microsofts flera innehavare molnbaserad katalog- och identity management-tjänsten.

[Azure AD](https://azure.microsoft.com/services/active-directory/) innehåller också en komplett uppsättning [Identitetshantering](https://docs.microsoft.com/azure/security/security-identity-management-overview) funktioner, inklusive [multifaktorautentisering](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication), enhetsregistrering, självbetjäning lösenordshantering grupphantering via självbetjäning, hantering av Privilegierade, rollbaserad åtkomstkontroll, övervakning, omfattande granskning och säkerhetsövervakning och avisering för programanvändning.

Följande funktioner kan hjälpa att säkra molnbaserade program, effektivisera IT-processer, minska kostnaderna och bidrar till att säkerställa att företagets efterlevnadsbehov uppfylls:

-   Identitets- och åtkomsthantering för molnet
-   Förenkla användaråtkomsten till alla molnappar
-   Skydda känsliga data och program
-   Ge personalen möjlighet att arbeta självständigt
-   Integrera med Azure Active Directory

### <a name="identity-and-access-management-for-the-cloud"></a>Identitets- och åtkomsthantering för molnet
Azure Active Directory (Azure AD) är en omfattande [molnlösning för identitets- och](https://www.microsoft.com/cloud-platform/identity-management), som ger dig en robust uppsättning funktioner för att hantera användare och grupper. Det hjälper att säkra åtkomst till lokala och molnbaserade program, inklusive Microsoft webbtjänster som Office 365 och mycket icke-Microsoft-programvara som en tjänst (SaaS)-program.
Läs mer hur du aktiverar du identity protection i Azure AD i [aktiverar Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable).

### <a name="simplify-user-access-to-any-cloud-app"></a>Förenkla användaråtkomsten till alla molnappar
[Aktivera enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) att förenkla användarnas åtkomst till tusentals molnprogram från Windows, Mac, Android och iOS-enheter. Användarna kan starta program från en anpassad webbaserad åtkomstpanelen eller mobilapp med sin företagsinloggning. Använda Azure AD Application Proxy-modulen för att gå bortom SaaS-program och publicera lokala webbprogram för att tillhandahålla mycket säker fjärråtkomst och enkel inloggning.

### <a name="protect-sensitive-data-and-applications"></a>Skydda känsliga data och program
Aktivera [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) att förebygga obehörig åtkomst till lokala och molnprogram genom att tillhandahålla en ytterligare autentiseringsnivå. Skydda ditt företag och identifiera potentiella hot med säkerhetsövervakning, varningar och maskininlärningsbaserade rapporter som visar avvikande åtkomstmönster.

### <a name="enable-self-service-for-your-employees"></a>Ge personalen möjlighet att arbeta självständigt
Delegera viktiga uppgifter till dina anställda, t.ex. återställa lösenord eller skapa och hantera grupper. [Aktivera självbetjäning lösenordsändring](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password), Återställ och självbetjäning grupphantering med Azure AD.

### <a name="integrate-with-azure-active-directory"></a>Integrera med Azure Active Directory
Utöka [Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate) och andra lokala kataloger till Azure AD för att aktivera enkel inloggning för alla molnbaserade program. Användarattributen kan automatiskt synkroniseras med din molnkatalog från alla typer av lokala kataloger.

Mer information om integrering av Azure Active Directory och hur du aktiverar det finns i artikel [integrerar dina lokala kataloger med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="trace-requests-analyze-usage-trends-and-diagnose-issues"></a>Spåra förfrågningar, analysera användningstrender och diagnostisera problem
[Azure Storage Analytics](https://docs.microsoft.com/azure/storage/storage-analytics) utför loggning och tillhandahåller mätvärden för ett lagringskonto. Du kan använda dessa data för att spåra förfrågningar, analysera användningstrender och diagnostisera problem med lagringskontot.

Mätvärden i Storage Analytics är aktiverade som standard för nya storage-konton. Du kan aktivera loggning och konfigurera mått och loggar in på Azure-portalen; Mer information finns i [övervaka ett lagringskonto i Azure-portalen](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Du kan också aktivera Storage Analytics programmässigt via REST API eller klientbiblioteket. Du kan använda åtgärden Ange egenskaper för filtjänsten för att aktivera Storage Analytics individuellt för varje tjänst.

Utförliga instruktioner om hur du använder Storage Analytics och andra verktyg för att identifiera, diagnostisera och felsöka Azure Storage-relaterade problem, se [övervaka, diagnostisera och Felsök Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Läs artikeln om du vill veta mer om integrering av Azure Active Directory och hur du aktiverar det [aktivera och konfigurera Lagringsanalys](https://docs.microsoft.com/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics?redirectedfrom=MSDN).

## <a name="monitoring-services"></a>Övervakningstjänster
Molnprogram är komplexa med alla rörliga delar. Övervakning ger data så att ditt program fungerar och körs i ett felfritt tillstånd. Det hjälper dig också att stave potentiella problem eller felsöka tidigare sådana. Du kan dessutom använda övervakningsdata för att få djupa insikter om ditt program. Denna kunskap kan hjälpa dig att förbättra programmets prestanda och underhåll eller automatisera åtgärder som annars skulle kräva manuella åtgärder.

### <a name="monitor-azure-resources"></a>Övervaka Azure-resurser
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) är plattformstjänst som tillhandahåller en enda källa för övervakning av Azure-resurser. Med Azure Monitor kan du visualisera, fråga, vidarebefordra, aktivera och vidta åtgärder för mått och loggar från resurser i Azure.  Du kan arbeta med dessa data med Monitor-portalbladet, [Monitor PowerShell Cmdlets](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples), [plattformsoberoende CLI](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples) eller [REST API:er för Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx).

### <a name="enable-autoscale-with-azure-monitor"></a>Aktivera automatisk skalning med Azure monitor
Aktivera [automatisk skalning i Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started) gäller enbart för VM-skalningsuppsättningar (VMSS), molntjänster, app service-planer och app service-miljöer.

### <a name="manage-roles-permissions-and-security"></a>Hantera roller behörigheter och säkerhet
Många team som behöver strikt [reglera åtkomst till övervakning](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security) data och inställningar. Till exempel om du har som fungerar endast om hur du övervakar (support-tekniker, devops-tekniker) eller om du använder en leverantör av hanterade tjänster kan du behöva ge dem åtkomst till endast övervakningsdata samtidigt begränsa deras möjlighet att skapa, ändra, eller ta bort resurser.

Detta visar hur du snabbt gäller en inbyggd övervakning RBAC-roll för en användare i Azure eller skapa en egen anpassad roll för en användare behöver begränsade behörigheter för övervakning. Det diskuterar sedan säkerhetsaspekter för dina Azure Monitor-relaterade resurser och hur du kan begränsa åtkomsten till den data de innehåller.

## <a name="prevent-detect-and-respond-to-threats"></a>Förhindra, upptäcka och svara på hot
Hotidentifieringen i Security Center sker genom automatisk insamling av säkerhetsinformation från Azure-resurser, nätverket och anslutna partnerlösningar. Det analyserar den här informationen kan korrelerar ofta information från flera källor för att identifiera hot. Säkerhetsaviseringar prioriteras i Security Center tillsammans med rekommendationer om hur hotet kan åtgärdas.

-   [Konfigurera en säkerhetsprincip](https://docs.microsoft.com/azure/security-center/security-center-policies) för din Azure-prenumeration.
-   Använd den [rekommendationer i Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations) att hjälpa dig att skydda dina Azure-resurser.
-   Granska och hantera din aktuella [säkerhetsaviseringar](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts).

Med hjälp av [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) kan du förebygga, upptäcka och åtgärda hot med bättre överblick och kontroll över säkerheten för dina resurser i Azure. Härifrån kan du övervaka och hantera principer för alla Azure-prenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

Security Center innehåller lättanvända och effektiva funktioner som finns inbyggda i Azure och som kan användas för att förebygga, upptäcka och åtgärda hot. Här är de viktigaste funktionerna:

-   Förstå molnsäkerhetsläge
-   Ta kontroll över molnsäkerhet
-   Distribuera enkelt integrerade molnsäkerhetslösningar
-   Upptäck hot och svara snabbt

### <a name="understand-cloud-security-state"></a>Förstå molnsäkerhetsläge
Använd Azure Security Center för att få en central vy över säkerhetsläget hos alla dina Azure-resurser. Kontrollera att rätt säkerhetskontroller är på plats och korrekt konfigurerad på ett ögonblick, och identifiera snabbt eventuella resurser som kräver uppmärksamhet.

### <a name="take-control-of-cloud-security"></a>Ta kontroll över molnsäkerhet
Definiera [säkerhetsprinciper](https://docs.microsoft.com/azure/security-center/security-center-policies) för din Azure-prenumerationer utifrån företagets molnsäkerhetsbehov, anpassade till typen av program eller efter Känslighetsnivån på datauppgifterna i respektive prenumeration. Använd principdrivna rekommendationer för att leda resursägare genom processen att implementera nödvändiga kontroller – ta bort gissningarna kring molnsäkerhet.

### <a name="easily-deploy-integrated-cloud-security-solutions"></a>Distribuera enkelt integrerade molnsäkerhetslösningar
[Aktivera säkerhetslösningar](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) från Microsoft och dess partner, inklusive branschledande brandväggar och program mot skadlig kod. Använd strömlinjeformad etablering för att distribuera säkerhetslösningar – även nätverksändringar konfigureras åt dig. Dina säkerhetshändelser från partnerlösningar samlas automatiskt in för analys och varning.

### <a name="detect-threats-and-respond-fast"></a>Upptäck hot och svara snabbt
Ligg före aktuella och kommande molnhot med en integrerad, analysdriven metod. Genom att kombinera Microsoft global [hotinformation](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities) och expertis med insikter i säkerhetsrelaterade händelser i molnet i din Azure-installationer kan Security Center hjälper dig att upptäcka verkliga hot tidigt och minska falska positiva identifieringar. Molnsäkerhetsvarningar ger dig insikt i attackkampanjen, inklusive relaterade händelser och vilka resurser som påverkas, samt föreslår sätt att åtgärda problem och hur du snabbt Återhämtar.

## <a name="end-to-end-scenario-based-network-monitoring"></a>Slutpunkt till slutpunkt scenariobaserade nätverksövervakning
Kunder kan du skapa en slutpunkt till slutpunkt-nätverk i Azure genom att samordna och skapa olika enskilda nätverksresurser, till exempel virtuella nätverk, ExpressRoute, Application Gateway, belastningsutjämnare och mer. Övervakning är tillgängliga på var och en av nätverksresurserna.

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) är en regional tjänst som hjälper dig att övervaka och diagnostisera villkor på scenariot för en nivå i, till och från Azure. Nätverksdiagnostik- och visualiseringsverktyg för Network Watcher hjälper dig att förstå, diagnostisera och få information om ditt nätverk i Azure.

### <a name="automate-remote-network-monitoring-with-packet-capture"></a>Automatisera övervakning av fjärrnätverk med infångade paket
Övervaka och diagnostisera nätverksproblem utan att logga in på dina virtuella datorer (VM) med Network Watcher. Utlösaren [paketfångsten](https://docs.microsoft.com/azure/network-watcher/network-watcher-alert-triggered-packet-capture) genom att konfigurera aviseringar och få åtkomst till prestandainformation i realtid på paketnivå. När du ser ett problem kan du undersöka i detalj för att få bättre diagnoser.

### <a name="gain-insight-into-your-network-traffic-using-flow-logs"></a>Få insikt i din nätverkstrafik med flödesloggar
Få en djupare förståelse av din trafik mönster med [flödesloggar för Nätverkssäkerhetsgruppen](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). Information från flödesloggar kan du samla in data för efterlevnad, granskning och övervakning av din nätverkssäkerhetsprofil.

### <a name="diagnose-vpn-connectivity-issues"></a>Diagnostisera VPN-anslutningsproblem
Network Watcher får du möjlighet att [diagnostisera dina vanligaste problem för VPN Gateway och anslutningar](https://docs.microsoft.com/azure/network-watcher/network-watcher-diagnose-on-premises-connectivity). Så att du inte bara för att identifiera problemet, utan också för att använda de detaljerade loggar som skapats för att gräva.

Mer information om hur du konfigurerar Network watcher och hur du aktiverar det finns i artikel [konfigurera Network watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="secure-deployment-using-proven-devops-tools"></a>Säker distribution med beprövade DevOps-verktyg
Det här är några av de listan för Azure DevOps-metoder i det här utrymmet för Microsoft Cloud, som gör företag och team produktiva och effektivt.

-   **Infrastruktur som kod (IaC):** infrastruktur som kod är en uppsättning tekniker och metoder som hjälper IT-proffs att ta bort belastningen som är associerade med den dagliga bygg- och hantering av modulära infrastrukturen. Det gör att IT-proffs att skapa och underhålla sina moderna server-miljö på ett sätt som liknar hur programvaruutvecklare att bygga och underhålla programkod. För Azure, har vi [Azure Resource Manager]( https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) kan du etablera dina program med hjälp av en deklarativ mall. I samma mall kan du distribuera flera tjänster tillsammans med deras beroenden. Du använder samma mall till att upprepade gånger distribuera ditt program i varje fas av programmets livscykel.
-   **Kontinuerlig integrering och distribution:** kan du konfigurera din Azure DevOps-projekt till [automatiskt skapa och distribuera](https://www.visualstudio.com/docs/build/overview) till Azure-webbappar eller molntjänster. Azure DevOps distribuerar automatiskt binärfilerna när du har gjort en version till Azure efter varje checkar in ny kod. Paketet genereringsprocessen som beskrivs här motsvarar att kommandot paketet i Visual Studio och publicera stegen är likvärdiga med kommandot Publicera i Visual Studio.
-   **Versionshantering:** Visual Studio [Release Management](https://msdn.microsoft.com/library/vs/alm/release/overview) är en bra lösning för att automatisera distribution av flera steg och hantera lanseringsprocessen. Skapa hanterade kontinuerlig distribution pipelines Frigör snabbt, enkelt och ofta. Vi kan mycket automatisera vår lanseringsprocessen med Release Management, och vi kan fördefinierade godkännandearbetsflöden. Distribuera lokalt till molnet, utöka och anpassa efter behov.
-   **Prestandaövervakning för appen:** identifiera problem, lösa problem och kontinuerligt förbättra dina program. Diagnostisera snabbt problem i ditt liveprogram. Förstå vad användarna gör med det. Konfigurationen är enkelt bara att lägga till JS-kod och en webconfig-post och du ser resultaten inom några minuter i portalen med all information. [Appinsikter](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/) hjälper företag att för snabbare identifiering av problem och åtgärder.
-   **Läsa in testning och automatisk skalning:** vi hittar problem med prestanda i vår app till att förbättra kvaliteten för distribution och appen alltid är igång eller vara tillgängliga för att serva verksamheten behöver. Kontrollera att din app kan hantera trafik för nästa start eller marknadsföring kampanjen. Börja köra molnbaserade [belastningstester](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) i nästan ingen tid med Azure DevOps.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure driftsäkerhet](https://docs.microsoft.com/azure/security/azure-operational-security).
- Mer [Operations Management Suite | Säkerhet och efterlevnad](https://www.microsoft.com/cloud-platform/security-and-compliance).
- [Komma igång med Operations Management Suite säkerhet och Granskningslösningen](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started).
