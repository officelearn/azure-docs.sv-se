---
title: Översikt över Azure operativ säkerhet | Microsoft Docs
description: Den här artikeln innehåller en översikt över Azure operativ säkerhet.
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: ca42e0cc76bc385f04cb2e010ff91dbb27bd627a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="azure-operational-security-overview"></a>Översikt över Azure operativ säkerhet
Azure operativ säkerhet refererar till tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda sina data, program och andra resurser i Microsoft Azure. [Azure operativ säkerhet](https://docs.microsoft.com/azure/security/azure-operational-security) är ett ramverk som innehåller informationen som gjorts via en mängd funktioner som är unika för Microsoft, inklusive den Microsoft Security Development Lifecycle (SDL), Microsoft Security Response Center programmet och djup medvetenhet om cyber security hotbilden i grunden.

Den här Azure Operational översikt över säkerheten i artikeln fokuserar på följande områden:

- Azure Operations Management Suite
-   Azure Security Center
-   Azure Monitor
-   Azure nätverksbevakaren
-   Azure Storage analytics
-   Azure Active directory

## <a name="azure-operations-management-suite"></a>Azure Operations Management Suite
IT-avdelningen ansvarar för att hantera datacenter infrastruktur, program och data, inklusive stabilitet och säkerhet för dessa system. Få säkerhetsinsikter över öka komplexa IT-miljöer ofta kräver dock organisationer att cobble ihop data från flera system för säkerhet och hantering.

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) är Microsofts molnbaserade IT lösning som hjälper dig att hantera och skydda dina lokala och molnet infrastruktur.

OMS är en molnbaserad IT lösning med många erbjudanden, till exempel IT Automation, säkerhet och efterlevnad, Log Analytics och säkerhetskopiering och återställning. Därför är det ett perfekt stöd för att hantera och skydda din IT-infrastruktur, lokalt och i molnet.

Huvudfunktionerna i OMS tillhandahålls genom en uppsättning tjänster som körs i Azure. Varje tjänst tillhandahåller en specifik hanteringsfunktion, och du kan kombinera tjänster för olika hanteringsscenarier. Som innehåller:

-   Log Analytics
-   Automation
-   Backup
-   Webbplatsåterställning

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) tillhandahåller övervakning för OMS genom att samla in data från hanterade resurser i en central databas. Dessa data kan omfatta händelser, prestandadata eller anpassade data via API:t. När data har samlats in är de tillgängliga för avisering, analys och export. Med den här metoden kan du konsolidera data från olika källor och kombinera data från dina Azure-tjänster med din befintliga lokala miljö. Metoden innebär också en tydlig separation mellan insamling av data från de åtgärder som vidtas för dessa data, vilket gör att alla åtgärder är tillgängliga för alla typer av data.

### <a name="automation"></a>Automation
Microsoft [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) gör det möjligt för användare att automatisera de manuella, långvariga, felbenägna och ofta återkommande uppgifter som utförs ofta i en miljö med molnet och företagets. Det sparar tid och ökar tillförlitligheten i vanliga administrativa uppgifter och schemalägger dem även så att de kan utföras automatiskt med jämna mellanrum. Du kan automatisera processer med hjälp av runbooks eller automatisera konfigurationshantering med Desired State Configuration.

### <a name="backup"></a>Backup
[Azure-säkerhetskopiering](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) är den Azure-baserad tjänst som du kan använda för att säkerhetskopiera (eller skydda) och återställa data i Microsoft-molnet. Azure Backup ersätter din befintliga lokala eller externa säkerhetskopieringslösning med en tillförlitlig och säker molnbaserad lösning med ett konkurrenskraftigt pris. Azure Backup erbjuder flera komponenter som du kan ladda ned och distribuera på den aktuella datorn, servern eller i molnet. Komponenten eller agenten som du distribuerar beror på vad du vill skydda. Alla Azure Backup-komponenter (oavsett om du skyddar data lokalt eller i molnet) kan användas för att säkerhetskopiera data till ett Recovery Services-valv i Azure. Finns det [Azure Backup komponenter tabellen](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup#which-azure-backup-components-should-i-use).

### <a name="site-recovery"></a>Webbplatsåterställning
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) ger verksamhetskontinuitet genom att dirigera replikering av lokala virtuella och fysiska datorer till Azure, eller till en sekundär plats. Om din primära plats inte är tillgänglig görs en växling till den sekundära platsen så att användarna kan fortsätta att arbeta och sedan görs en återställning när systemen fungerar som de ska igen. Använd Azure Security Center för att utföra intelligent och effektiv hotidentifiering.

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enable-sso-scenario) är Microsofts omfattande identitet som en tjänst (IDaaS) som:

-   Aktiverar IAM som en tjänst i molnet
-   Tillhandahåller centrala hantering, enkel inloggning (SSO) och rapportering
-   Stöder integrerad åtkomsthantering för [tusentals program](https://azure.microsoft.com/marketplace/active-directory/) i programgalleriet inklusive Salesforce, Google Apps, rutan, Concur och mycket mer.

Azure AD innehåller också en fullständig uppsättning [identitetshanteringsfunktionerna](https://docs.microsoft.com/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports) inklusive [multifaktorautentisering](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication), [enhetsregistrering]( https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-overview), [självbetjäning lösenordshantering](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/), [grupphantering via självbetjäning](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password), [privilegierad kontohantering](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure), [rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/overview), [övervakning av programanvändning](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health), [omfattande granskning](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs), och [säkerhet övervakning och avisering](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts).

Med Azure Active Directory, alla program som du publicerar för partner och kunder (business eller konsumenten) har samma identitet och åtkomst hanteringsfunktioner. Detta gör att du kan avsevärt minska din driftskostnader.

## <a name="azure-security-center"></a>Azure Security Center
Med hjälp av [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-get-started) kan du förebygga, upptäcka och åtgärda hot med bättre överblick och kontroll över säkerheten för dina resurser i Azure. Härifrån kan du övervaka och hantera principer för alla prenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

[Security Center](https://docs.microsoft.com/azure/security-center/security-center-linux-virtual-machine) kan du skydda data för virtuell dator i Azure genom att tillhandahålla insyn i den virtuella datorns säkerhetsinställningar och övervakning för hot. Security Center kan övervaka dina virtuella datorer för:

-   Säkerhetsinställningar för operativsystem (OS) med de rekommenderade konfigurationsreglerna
-   Systemsäkerhet och viktiga uppdateringar som saknas
-   Rekommendationer för slutpunktsskydd
-   Verifiering av diskkryptering
-   Nätverksbaserade attacker

Azure Security Center använder [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), vilket ger [inbyggda roller](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) som kan tilldelas användare, grupper och tjänster i Azure.

Security Center utvärderar konfigurationen av dina resurser för att identifiera säkerhetsproblem och säkerhetsproblem. I Security Center kan se du bara information relaterad till en resurs när du har tilldelats rollen ägare, deltagare eller läsare för prenumeration eller resursgrupp som en resurs tillhör.

>[!Note]
>Se [behörigheter i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-permissions) för mer information om roller och tillåtna åtgärder i Security Center.

Security Center använder Microsoft Monitoring Agent – det här är samma agent används av Operations Management Suite och Log Analytics-tjänsten. Data som samlas in från den här agenten lagras i en befintlig logganalys [arbetsytan](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) som är associerade med din Azure-prenumeration eller en ny arbetsytor med hänsyn till geolokalisering för den virtuella datorn.

## <a name="azure-monitor"></a>Azure Monitor
Prestandaproblem i din molnapp kan påverka din verksamhet. Med flera sammankopplade komponenter och ofta versioner kan degradations inträffa när som helst. Och om du utvecklar en app användarna identifiera vanligtvis problem som du inte kan hitta vid testning. Du bör känna till de här problemen omedelbart och har verktyg för att diagnostisera och lösa problem.

[Azure-Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) är grundläggande verktyg för att övervaka tjänster som körs på Azure. Den ger dig infrastrukturnivå data om genomflödet av en tjänst och omgivningen. Om du hanterar dina appar i Azure, ger bestämmer dig för att skala upp eller ned resurser, sedan Azure-Monitor dig vad du använder för att starta.

Du kan dessutom använda övervakningsdata och få djupa insikter om ditt program. Denna kunskap kan hjälpa dig att förbättra programmets prestanda eller underhålla eller automatisera åtgärder som annars skulle kräva manuella åtgärder. Det innehåller:

-   Azure-aktivitetsloggen
-   Azure diagnostikloggar
-   Mått
-   Azure Diagnostics

### <a name="azure-activity-log"></a>Azure-aktivitetsloggen
Det är en logg som ger inblick i åtgärder som utfördes på resurser i din prenumeration. Den [aktivitetsloggen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) kallades tidigare ”granskningsloggar” eller ”operativa loggar” eftersom den rapporterar kontroll-plan händelser för dina prenumerationer.

### <a name="azure-diagnostic-logs"></a>Azure diagnostikloggar
[Azure diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) orsakat av en resurs och tillhandahålla omfattande, ofta information om handhavandet av den här resursen. Innehållet i de här loggarna varierar beroende på resurstypen.

Till exempel Windows-händelsesystemloggar är en kategori av diagnostiska loggar för virtuella datorer och blob, tabell och kön loggar är kategorier av diagnostiska loggar för lagringskonton.

Diagnostik loggar skiljer sig från den [aktivitetsloggen (kallades granskningsloggen eller arbetsloggen)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Aktivitetsloggen ger inblick i åtgärder som utfördes på resurser i din prenumeration. Diagnostikloggar ger information om åtgärder som din resurs har vidtagit på egen hand.

### <a name="metrics"></a>Mått
Azure-Monitor kan du använda telemetri för att få insyn i prestanda och hälsotillståndet för dina arbetsbelastningar i Azure. Viktigaste är Azure telemetridata mätvärdena (kallas även prestandaräknare) sänds av mest Azure-resurser. Azure-Monitor finns flera sätt att konfigurera och använda dessa [mått](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) för övervakning och felsökning.

### <a name="azure-diagnostics"></a>Azure Diagnostics
Det är en funktion i Azure som möjliggör insamling av diagnostikdata på ett distribuerat program. Du kan använda tillägget diagnostik från olika olika källor. Stöds för närvarande är [Azure Cloud Service webb- och arbetsroller](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [Azure Virtual Machines](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) med Microsoft Windows och [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics).


## <a name="network-watcher"></a>Network Watcher
Kunder kan du skapa ett nätverk i Azure genom att samordna och skapa olika enskilda nätverksresurser, till exempel virtuella nätverk, ExpressRoute, Programgateway, belastningsutjämnare och flera. Övervakning är tillgänglig på alla nätverksresurser.

Slutpunkt till slutpunkt-nätverket kan ha komplexa konfigurationer och samverkan mellan resurser, skapa komplicerade scenarier som behöver scenariobaserade övervakning via Nätverksbevakaren.

[Nätverk Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) kommer förenklar övervakning och diagnos av Azure-nätverk. Diagnostik- och visualisering verktyg som är tillgängliga med Nätverksbevakaren aktivera fångar du vidtar remote paket på en virtuell dator i Azure, få insikter om nätverket trafik med hjälp av flödet loggar och diagnostisera VPN-Gateway och anslutningar.

Nätverksbevakaren har för närvarande följande funktioner:

- [Topologi](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) -nätverket nivån visa visar de olika anslutningarna och associationer mellan nätverksresurser i en resursgrupp.
-   [Variabeln paketinsamling](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) -samlar in paketdata till och från en virtuell dator. Avancerade alternativ för filtrering och finjustera kontroller, till exempel att kunna ange tid och storlek begränsningar ger flexibilitet. Paketdata kan lagras i en blobstore eller på den lokala disken i CAP-format.
-   [Kontrollera IP-flöden](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) -kontrollerar om ett paket tillåts eller nekas baserat på flödet information 5-tuppel paket parametrar (mål-IP, käll-IP, målport, källport och Protocol). Om paketet nekas av en säkerhetsgrupp, returneras regeln och grupp som nekas paketet.
-   [Nästa hopp](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) -avgör nästa hopp för paket som vidarebefordras i Azure-nätverksinfrastruktur, vilket gör det lättare att diagnostisera eventuella felkonfigurerat användardefinierade vägar.
-   [Säkerhet gruppvyn](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) -hämtar effektiva och tillämpade säkerhetsregler som tillämpas på en virtuell dator.
-   [NSG flöda loggning](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) -flöde Nätverkssäkerhetsgrupper loggarna gör det möjligt att samla in loggar som rör trafik som tillåts eller nekas av säkerhetsregler i gruppen. Flödet definieras av en 5-tuppel information – käll-IP, mål-IP, källport, målport och protokoll.
-   [Virtuella Nätverksgatewayen och anslutningen felsökning](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) – ger dig möjlighet att felsöka virtuella Nätverksgatewayer och anslutningar.
-   [Nätverk prenumerationsbegränsningar](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) -kan du visa nätverksresursanvändning mot gränser.
-   [Konfigurera diagnostik loggen](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) – innehåller en om du vill aktivera eller inaktivera diagnostik loggar för nätverksresurser i en resursgrupp.

Mer information hur du konfigurerar nätverket watcher ser [konfigurera nätverksbevakaren](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="developer-operations-devops"></a>Utvecklare Operations (DevOps)
Innan DevOps programutveckling var team ansvarar för samla in affärskraven för ett program och skriva kod. Ett separat team QA testar sedan programmet i en isolerad utvecklingsmiljö om krav har uppfyllts, och frigör koden att distribuera. Distributionsgrupperna är ytterligare fragmenterad i ”magasin” grupper som nätverk och databasen. Varje gång ett program är ”uppstod över vägguttaget” till ett oberoende team den lägger till flaskhalsar.

[DevOps](https://www.visualstudio.com/learn/what-is-devops/) aktiverar team kan ge säkrare högre kvalitet lösningar snabbare och billigare. Kunder förväntar sig en dynamisk och tillförlitlig upplevelse när förbrukar programvaror och tjänster.  Team måste snabbt iterera programuppdateringar, mäter effekten av uppdateringar och snabbt svara med nya development iterationer för att åtgärda problem eller ger mer värde.  Molnplattformar, till exempel Microsoft Azure har bort traditionella flaskhalsar och hjälpt commoditize infrastruktur. Programvara prestandarik i alla företag som nyckelfaktorn och faktor i affärsresultatet. Ingen organisation, utvecklare och IT-arbetare kan eller Undvik DevOps-transport.

Mogen jobbar med DevOps anta flera av följande metoder. Dessa metoder [involvera personer](https://www.visualstudio.com/learn/what-is-devops-culture/) till formuläret strategier baserat på verksamhets scenarier.  Verktygsuppsättning kan hjälpa dig att automatisera olika metoder:

-   [Flexibel planering och projekthantering](https://www.visualstudio.com/learn/what-is-agile/) tekniker som används för att planera och isolera arbete i sprints, hantera team kapacitet och hjälpa grupper snabbt anpassa sig till förändrade verksamhetsbehov.
-   [Versionskontroll, vanligtvis med Git](https://www.visualstudio.com/learn/what-is-git/), gör att grupper som helst i världen kan dela datakällan och interagera med utvecklarverktyg för programvara för att automatisera versionen pipeline.
-   [Kontinuerlig Integration](https://www.visualstudio.com/learn/what-is-continuous-integration/) enheter pågående sammanslagning och testning av kod, vilket leder till att söka efter fel för tidigt.  Andra fördelar mindre tid som gått förlorat på bekämpa merge problem och snabb feedback för utvecklingsgrupper.
-   [Kontinuerlig leverans](https://www.visualstudio.com/learn/what-is-continuous-delivery/) lösningar software till produktion och tester miljöer hjälp organisationer snabbt åtgärda fel och svara på föränderliga affärsbehov.
-   [Övervaka](https://www.visualstudio.com/learn/what-is-monitoring/) inklusive produktionsmiljöer för programmets hälsotillstånd som program som körs som kundens användning hjälp organisationer formulär plus en hypotes snabbt validera och disprove strategier.  Omfattande data fångas in och lagras i olika format för loggning.
-   [Infrastruktur som kod (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) är en metod, vilket gör att automation och verifiering av skapande och teardown av nätverk och virtuella datorer för att leverera säker och stabil program som är värd för plattformar.
-   [Mikrotjänster](https://www.visualstudio.com/learn/what-are-microservices/) arkitektur utnyttjas för att isolera användningsområden i små återanvändbara tjänster.  Den här arkitekturen möjliggör skalbarhet och effektivitet.

## <a name="next-steps"></a>Nästa steg
Om du vill lära dig mer om OMS-säkerhet och granska lösningen finns i följande artiklar:

- [Operations Management Suite | Säkerhet och efterlevnad](https://www.microsoft.com/cloud-platform/security-and-compliance).
- [Övervaka och åtgärda säkerhetsaviseringar i Operations Management Suite säkerhet och granska lösningen](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts).
- [Övervakning av resurser i Operations Management Suite säkerhet och granska lösningen](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources).
