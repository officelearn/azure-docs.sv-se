---
title: "Azure Operational säkerhetsmetoder | Microsoft Docs"
description: "Den här artikeln innehåller en uppsättning av bästa praxis för Azure operativ säkerhet."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: ed3c4bf2dcdda3e04a18682da568ae73f41bb37d
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="azure-operational-security-best-practices"></a>Azure Operational säkerhetsmetoder
Azure operativ säkerhet refererar till tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda sina data, program och andra resurser i Microsoft Azure. Azure operativ säkerhet bygger på ett ramverk som innehåller kunskap via olika funktioner som är unika för Microsoft, inklusive den Microsoft Security Development Lifecycle (SDL), Microsoft Security Response Center-program och djup medvetenhet om hotbild cybersecurity.

I den här artikeln tar vi upp en samling Azure-databas säkerhetsmetoder. Följande rekommendationer härleds från våra upplevelse med säkerhet på Azure-databas och erfarenheter från kunder som dig själv.

För varje bästa praxis förklarar vi:
-   Vad som är bästa praxis
-   Varför du vill aktivera den bästa praxis
-   Vad kan vara resultatet om du inte aktivera bästa praxis
- Hur du kan lära dig att aktivera bästa praxis

Den här artikeln Azure Operational säkerhetsmetoder baseras på en konsensus åsikt och Azure plattformsfunktioner och funktioner, som de finns på den tid som den här artikeln skrevs. Åsikter och tekniker ändras med tiden och den här artikeln kommer att uppdateras regelbundet så att dessa ändringar.

Azure Operational Metodtips om säkerhet i den här artikeln omfattar:

-   Övervaka, hantera och skydda moln-infrastruktur
-   Hantera identitet och implementera enkel inloggning (SSO)
-   Spåra begäranden, analysera användningstrender och diagnostisera problem
-   Övervaka tjänster med en centraliserad övervakningslösning
-   Förebygga, upptäcka och åtgärda hot
-   Slutpunkt till slutpunkt scenariobaserade nätverksövervakning
-   Säker distribution med beprövade DevOps-verktyg

## <a name="monitor-manage-and-protect-cloud-infrastructure"></a>Övervaka, hantera och skydda moln-infrastruktur
IT-avdelningen ansvarar för att hantera datacenter infrastruktur, program och data, inklusive stabilitet och säkerhet för dessa system. Få säkerhetsinsikter över öka komplexa IT-miljöer ofta kräver dock organisationer att cobble ihop data från flera system för säkerhet och hantering.

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) är Microsofts molnbaserade IT lösning som hjälper dig att hantera och skydda dina lokala och molnet infrastruktur.

[OMS säkerhet och granska lösningen](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) möjliggör för IT att övervaka aktivt alla resurser som hjälper dig att minimera effekten av säkerhetsincidenter. OMS säkerhet och granska har säkerhetsdomäner som kan användas för att övervaka resurser.

Mer information om OMS finns i artikeln [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

För att förebygga, upptäcka och åtgärda hot, [Operations Management Suite (OMS) säkerhets- och granska lösningen](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) samlar in och bearbetar data om dina resurser, som omfattar:

-   Säkerhetshändelselogg
-   ETW-händelser (Event Tracing for Windows)
-   AppLocker-granskningshändelser
-   Windows-brandväggslogg
-   Advanced Threat Analytics-händelser
-   Resultat från utvärdering av säkerhetsbaslinje
-   Resultat från utvärdering av program mot skadlig kod
-   Resultat från utvärdering av uppdateringar/korrigeringar
-   Syslog-dataströmmar som explicit har aktiverats på agenten


## <a name="manage-identity-and-implement-single-sign-on"></a>Hantera identitet och implementera enkel inloggning
[Azure Active Directory (AD Azure)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) är Microsofts flera innehavare molnbaserad katalog och identity management-tjänsten.

[Azure AD](https://azure.microsoft.com/services/active-directory/) innehåller också en fullständig uppsättning [Identitetshantering](https://docs.microsoft.com/azure/security/security-identity-management-overview) funktioner inklusive [multifaktorautentisering](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication), enhetsregistrering, självbetjäning lösenordshantering grupphantering via självbetjäning, privilegierat kontohantering, rollbaserad åtkomstkontroll, övervakning, omfattande granskning och säkerhet övervakning och avisering för programanvändning.

Följande funktioner kan hjälpa säkra molnbaserade program, effektivisera IT-processer, minska kostnaderna och se till att företagsefterlevnad mål är uppfyllda:

-   Identitets- och åtkomsthantering för molnet
-   Förenkla användaråtkomsten till alla molnappar
-   Skydda känsliga data och program
-   Ge personalen möjlighet att arbeta självständigt
-   Integrera med Azure Active Directory

### <a name="identity-and-access-management-for-the-cloud"></a>Identitets- och åtkomsthantering för molnet
Azure Active Directory (AD Azure) är en omfattande [identitets- och molnet hanteringslösning](https://www.microsoft.com/cloud-platform/identity-management), som ger dig en kraftfull uppsättning funktioner för att hantera användare och grupper. Det hjälper säker åtkomst till lokala program och molnprogram, inklusive Microsoft web-tjänster som Office 365 och mycket icke-Microsoft-programvara som en tjänst (SaaS)-program.
Mer information hur du aktiverar identitetsskydd i Azure AD finns [aktiverar Azure Active Directory identitetsskydd](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable).

### <a name="simplify-user-access-to-any-cloud-app"></a>Förenkla användaråtkomsten till alla molnappar
[Aktivera enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) att förenkla användaråtkomst till tusentals molnprogram från Windows, Mac, Android och iOS-enheter. Användare kan starta program från en anpassad webbaserade åtkomstpanelen eller mobila appar med företagets autentiseringsuppgifter. Använda Azure AD Application Proxy-modulen utöver SaaS-program och publicera lokala webbprogram för att tillhandahålla hög säker fjärråtkomst och enkel inloggning.

### <a name="protect-sensitive-data-and-applications"></a>Skydda känsliga data och program
Aktivera [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) att förhindra obehörig åtkomst till lokala program och molnprogram genom att tillhandahålla en extra nivå av autentisering. Skydda ditt företag och identifiera potentiella hot med säkerhetsövervakning, varningar och maskininlärningsbaserade rapporter som visar avvikande åtkomstmönster.

### <a name="enable-self-service-for-your-employees"></a>Ge personalen möjlighet att arbeta självständigt
Delegera viktiga uppgifter till dina anställda, t.ex. återställa lösenord eller skapa och hantera grupper. [Aktivera självbetjäning lösenordsändring](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password), Återställ och självbetjäning hantering med Azure AD.

### <a name="integrate-with-azure-active-directory"></a>Integrera med Azure Active Directory
Utöka [Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate) och andra lokala kataloger till Azure AD för att aktivera enkel inloggning för alla molnbaserade program. Användarattributen kan automatiskt synkroniseras med din molnkatalog från alla typer av lokala kataloger.

Mer information om integrering av Azure Active Directory och hur du aktiverar det finns i artikel [integrera dina lokala kataloger med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="trace-requests-analyze-usage-trends-and-diagnose-issues"></a>Spåra begäranden, analysera användningstrender och diagnostisera problem
[Azure Storage Analytics](https://docs.microsoft.com/azure/storage/storage-analytics) utför loggning och tillhandahåller data för mått för ett lagringskonto. Du kan använda dessa data för att spåra förfrågningar, analysera användningstrender och diagnostisera problem med lagringskontot.

Storage Analytics mätvärden är aktiverade som standard för nya storage-konton. Du kan aktivera loggning och konfigurera mått och loggning i Azure-portalen; Mer information finns i [övervaka ett lagringskonto i Azure portal](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Du kan också aktivera Storage Analytics programmässigt via REST API eller klientbiblioteket. Du kan använda åtgärden Ange egenskaper för tjänsten för att aktivera Storage Analytics individuellt för varje tjänst.

En detaljerad vägledning om använder Storage Analytics och andra verktyg för att identifiera, diagnostisera och felsöka problem med Azure Storage finns [övervaka, diagnostisera och felsöka Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Mer information om integrering av Azure Active Directory och hur du aktiverar det artikeln [aktivera och konfigurera Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics?redirectedfrom=MSDN).

## <a name="monitoring-services"></a>Övervaka tjänster
Molnprogram är komplicerade med många rörliga delar. Övervakning tillhandahåller data för att säkerställa att programmet in och körs i ett felfritt tillstånd. Det hjälper dig också att stave ut potentiella problem eller felsöka tidigare viktiga. Du kan dessutom använda övervakningsdata och få djupa insikter om ditt program. Denna kunskap kan hjälpa dig att förbättra programmets prestanda eller underhålla eller automatisera åtgärder som annars skulle kräva manuella åtgärder.

### <a name="monitor-azure-resources"></a>Övervaka Azure-resurser
[Azure-Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) är platform-tjänst som tillhandahåller en enda källa för övervakning av Azure-resurser. Med Azure Monitor kan du visualisera, fråga, vidarebefordra, aktivera och vidta åtgärder för mått och loggar från resurser i Azure.  Du kan arbeta med dessa data med Monitor-portalbladet, [Monitor PowerShell Cmdlets](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples), [plattformsoberoende CLI](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples) eller [REST API:er för Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx).

### <a name="enable-autoscale-with-azure-monitor"></a>Aktivera Autoskala med Azure Övervakare
Aktivera [Azure övervakaren Autoskala](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started) gäller enbart för skalningsuppsättningar i virtuella datorer (VMSS), cloud services, apptjänstplaner och apptjänstmiljöer.

### <a name="manage-roles-permissions-and-security"></a>Hantera roller behörigheter och säkerhet
Många grupper som behöver strikt [reglera åtkomst till övervakning](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security) data och inställningar. Till exempel om du har gruppmedlemmar som arbetar enbart om hur du övervakar (supporttekniker, devops engineers) eller om du använder en leverantör av hanterade tjänster, kanske du vill bevilja dem åtkomst till endast övervakningsdata samtidigt begränsa deras möjlighet att skapa, ändra, eller ta bort resurser.

Här visas hur du snabbt vill använda en inbyggd övervakning RBAC roll till en användare i Azure eller skapa egna anpassade roll för en användare behöver begränsade behörigheter för övervakning. Sedan diskuterar det säkerhetsaspekter för dina Azure-Monitor-relaterade resurser och hur du kan begränsa åtkomst till de data som de innehåller.

## <a name="prevent-detect-and-respond-to-threats"></a>Förebygga, upptäcka och åtgärda hot
Hotidentifiering Security Center fungerar genom att automatiskt samla in säkerhetsinformation från Azure-resurser, nätverk och anslutna partnerlösningar. Det analyserar informationen ofta korrelerar information från flera källor, identifiera hot. Säkerhetsaviseringar prioriteras i Security Center tillsammans med rekommendationer om hur hotet kan åtgärdas.

-   [Konfigurera en säkerhetsprincip för](https://docs.microsoft.com/azure/security-center/security-center-policies) för din Azure-prenumeration.
-   Använd den [rekommendationer i Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations) som hjälper dig att skydda dina Azure-resurser.
-   Granska och hantera din aktuella [säkerhetsaviseringar](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts).

Med hjälp av [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) kan du förebygga, upptäcka och åtgärda hot med bättre överblick och kontroll över säkerheten för dina resurser i Azure. Härifrån kan du övervaka och hantera principer för alla Azureprenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

Security Center innehåller lättanvända och effektiva funktioner som finns inbyggda i Azure och som kan användas för att förebygga, upptäcka och åtgärda hot. Här är de viktigaste funktionerna:

-   Förstå hälsotillstånd för moln-säkerhet
-   Ta kontroll över molnsäkerhet
-   Distribuera enkelt integrerade molnsäkerhetslösningar
-   Upptäck hot och svara snabbt

### <a name="understand-cloud-security-state"></a>Förstå hälsotillstånd för moln-säkerhet
Använd Azure Security Center för att få en central vy över säkerhetsläget hos alla dina Azure-resurser. Kontrollera att lämpliga säkerhetsåtgärder är på plats och korrekt konfigurerad på ett ögonblick och snabbt identifiera alla resurser som kräver uppmärksamhet.

### <a name="take-control-of-cloud-security"></a>Ta kontroll över molnsäkerhet
Definiera [säkerhetsprinciper](https://docs.microsoft.com/azure/security-center/security-center-policies) för Azure-prenumerationer enligt ditt företags säkerhetskrav för moln, är skräddarsydda för typ av program eller datakänslighetsnivå i varje prenumeration. Använd principdrivna rekommendationer för att leda resursägare genom processen att implementera nödvändiga kontroller – ta bort gissningarna kring molnsäkerhet.

### <a name="easily-deploy-integrated-cloud-security-solutions"></a>Distribuera enkelt integrerade molnsäkerhetslösningar
[Aktivera säkerhetslösningar](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) från Microsoft och dess partners, inklusive branschledande brandväggar och program mot skadlig kod. Använd strömlinjeformad etablering för att distribuera säkerhetslösningar – även nätverksändringar konfigureras åt dig. Dina säkerhetshändelser från partnerlösningar samlas automatiskt in för analys och varning.

### <a name="detect-threats-and-respond-fast"></a>Upptäck hot och svara snabbt
Ligg före aktuella och kommande molnhot med en integrerad, analysdriven metod. Genom att kombinera Microsoft global [hot intelligence](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities) och kunskaper med insikter om molnet säkerhetshändelser över Azure-distributioner, Security Center hjälper dig att identifiera tidigt verkliga hot och falsklarm. Molnet säkerhetsaviseringar ger dig insikter om kampanjen attack, inklusive relaterade händelser och vilka resurser och föreslår sätt att åtgärda problem och återställa snabbt.

## <a name="end-to-end-scenario-based-network-monitoring"></a>Slutpunkt till slutpunkt scenariobaserade nätverksövervakning
Kunder kan du skapa ett nätverk i Azure genom att samordna och skapa olika enskilda nätverksresurser, till exempel virtuella nätverk, ExpressRoute, Programgateway, belastningsutjämnare och flera. Övervakning är tillgänglig på alla nätverksresurser.

[Nätverk Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) är en regionala tjänst som gör att du kan övervaka och diagnostisera villkor på scenariot för en nivå, till och från Azure. Diagnostiska nätverks- och visualiseringsverktyg som finns tillgängliga med Nätverksbevakaren hjälpa dig att förstå, diagnostisera och få insyn i nätverket i Azure.

### <a name="automate-remote-network-monitoring-with-packet-capture"></a>Automatisera övervakning av fjärrnätverk med infångade paket
Övervaka och diagnostisera nätverksproblem utan att logga in på dina virtuella datorer (VM) med Network Watcher. Utlösaren [paketinsamling](https://docs.microsoft.com/azure/network-watcher/network-watcher-alert-triggered-packet-capture) genom att konfigurera aviseringar och få tillgång till prestandainformation i realtid på paketnivå. När du ser ett problem kan du undersöka i detalj för att få bättre diagnoser.

### <a name="gain-insight-into-your-network-traffic-using-flow-logs"></a>Få insikt i din nätverkstrafik med flödesloggar
Skapa en bättre förståelse av din trafik mönster med [Nätverkssäkerhetsgruppen flöde loggar](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-nsg-flow-logging-overview). Information som tillhandahålls av flödet loggar hjälper dig att samla in data för godkännande, granskning och övervakning av din säkerhet nätverksprofil.

### <a name="diagnose-vpn-connectivity-issues"></a>Diagnostisera VPN-anslutningsproblem
Nätverksbevakaren ger dig möjlighet att [diagnostisera dina mest vanliga problem med VPN-Gateway och anslutningar](https://docs.microsoft.com/azure/network-watcher/network-watcher-diagnose-on-premises-connectivity). Så att du inte bara för att identifiera problemet, men också för att använda de detaljerade loggar som skapats för att undersöka.

Mer information om hur du konfigurerar nätverksbevakaren och hur du aktiverar det finns i artikel [konfigurera nätverksbevakaren](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="secure-deployment-using-proven-devops-tools"></a>Säker distribution med beprövade DevOps-verktyg
Detta är listan över Azure DevOps praxis i det här Microsoft Cloud-utrymme, vilket gör att företag och team produktiv och effektiv.

-   **Infrastruktur som kod (IaC):** infrastruktur som koden är en uppsättning tekniker och metoder som hjälper IT-proffs ta bort belastningen som är associerade med dagliga bygg- och hantering av modulära infrastruktur. Det gör att IT-proffs att bygga och underhålla sina moderna servermiljö på ett sätt som liknar hur för utvecklare att bygga och underhålla programkod. För Azure, har vi [Azure Resource Manager]( https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) kan du etablera dina program med en deklarativ mall. I samma mall kan du distribuera flera tjänster tillsammans med deras beroenden. Du använder samma mall till att upprepade gånger distribuera ditt program i varje fas av programmets livscykel.
-   **Kontinuerlig integrering och distribution:** kan du konfigurera din Visual Studio Online grupprojekt att [automatiskt skapa och distribuera](https://www.visualstudio.com/docs/build/overview) till Azure-webbappar eller molntjänster. VSO distribuerar binärfilerna automatiskt när du har gjort en build till Azure efter varje kod incheckning. Paketet build-processen som beskrivs här motsvarar kommandot paketet i Visual Studio och publishing stegen är likvärdiga med kommandot Publicera i Visual Studio.
-   **Versionshantering:** Visual Studio [släpper Management](https://msdn.microsoft.com/library/vs/alm/release/overview) är en bra lösning för att automatisera distributionen av flera steg och hantera release-processen. Skapa hanterade kontinuerlig distribution pipelines att släppa snabbt, enkelt och ofta. Vi kan mycket automatisera våra versionen med släpper Management och vi kan fördefinierade godkännandearbetsflöden. Distribuera lokalt till molnet, utöka och anpassa efter behov.
-   **Appen prestanda övervakning:** identifiera problem, lösa problem och kontinuerligt förbättra dina program. Diagnostisera snabbt problem i ditt liveprogram. Förstå vad användarna gör med det. Konfigurationen är enkelt frågan för att lägga till JS kod och en webconfig-post och du endast ser resultat inom minuter i portalen med informationen. [App insights](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/) hjälper företag för snabbare identifiering av problem & reparation.
-   **Läsa in testar & Autoskala:** vi hittar prestandaproblem i vår app för att förbättra kvaliteten för distribution och se till vår app alltid är igång eller tillgängliga för att tillgodose verksamheten behöver. Kontrollera att din app kan hantera trafik för nästa start eller marknadsföring kampanjen. Börja köra molnbaserade [tester för att läsa in](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) nästan ingen tid med Visual Studio Online.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [Azure operativ säkerhet](https://docs.microsoft.com/azure/security/azure-operational-security).
- Mer information [Operations Management Suite | Säkerhet och efterlevnad](https://www.microsoft.com/cloud-platform/security-and-compliance).
- [Komma igång med Operations Management Suite säkerhet och granska lösningen](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started).
