---
title: Översikt över Azure operativ säkerhet | Microsoft Docs
description: Den här artikeln innehåller en översikt över Azure operativ säkerhet.
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
ms.openlocfilehash: c0413678aad16105f732ef23fb60c61fddcdad45
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="azure-operational-security-overview"></a>Översikt över Azure operativ säkerhet
[Azure operativ säkerhet](https://docs.microsoft.com/azure/security/azure-operational-security) refererar till tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda sina data, program och andra resurser i Microsoft Azure. Det är ett ramverk som innehåller kunskap via en mängd funktioner som är unika för Microsoft. Dessa funktioner är Microsoft Security Development Lifecycle (SDL), Microsoft Security Response Center program och djup medvetenhet om hotbild cybersecurity.

## <a name="operations-management-suite"></a>Operations Management Suite
IT-teamet operations ansvarar för att hantera datacenter infrastruktur, program och data, inklusive stabilitet och säkerhet för dessa system. Få säkerhetsinsikter över öka komplexa IT-miljöer ofta kräver dock organisationer att cobble ihop data från flera system för säkerhet och hantering.

[Microsoft Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) är en molnbaserad IT lösning som hjälper dig att hantera och skydda dina lokala och molnet infrastruktur. Den grundläggande funktionen kommer från följande tjänster som körs i Azure. Varje tjänst innehåller en funktion för specifika hanteringsservern. Du kan kombinera tjänster för att uppnå olika hanteringsscenarier. 

### <a name="log-analytics"></a>Log Analytics
[Azure logganalys](http://azure.microsoft.com/documentation/services/log-analytics) tillhandahåller övervakningstjänster för Operations Management Suite genom att samla in data från hanterade resurser i en central databas. Dessa data kan omfatta händelser, prestandadata eller anpassade data som tillhandahålls via API: et. När data samlas in, är den tillgänglig för aviseringar, analys och export. 

Kan du konsolidera data från olika källor och kombinera data från Azure-tjänster med din befintliga lokala miljö. Logganalys skiljer också tydligt insamling av data från de åtgärder som vidtagits på dessa data så att alla åtgärder är tillgängliga för alla typer av data.

### <a name="automation"></a>Automation
[Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) är ett sätt för att automatisera de manuella, långvariga, felbenägna och ofta återkommande uppgifter som utförs ofta i en miljö med molnet och företagets. Det sparar tid och ökar tillförlitligheten administrativa uppgifter. Dessa aktiviteter utförs automatiskt med jämna mellanrum schemaläggs även. Du kan automatisera processer med hjälp av runbooks eller automatisera konfigurationshantering med hjälp av Desired State Configuration.

### <a name="backup"></a>Backup
[Azure-säkerhetskopiering](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) är den Azure-tjänst som du kan använda för att säkerhetskopiera (eller skydda) och återställa data i Microsoft Cloud. Azure-säkerhetskopiering ersätter din befintliga lokala eller en lösning för extern säkerhetskopiering med en molnbaserad lösning som är tillförlitlig, säker och konkurrenskraftiga kostnaden. 

Azure Backup erbjuder komponenter som du kan hämta och distribuera på datorn eller servern eller i molnet. Komponenten eller agenten som du distribuerar beror på vad du vill skydda. Alla komponenter i Azure Backup kan (om du skyddar data lokalt eller i molnet) användas för att säkerhetskopiera data till ett Azure Recovery Services-valv i Azure. 

Mer information finns i [Azure Backup komponenter tabellen](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup#which-azure-backup-components-should-i-use).

### <a name="site-recovery"></a>Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) ger affärskontinuitet genom att samordna replikeringen av lokala virtuella och fysiska datorer till Azure eller till en sekundär plats. Om den primära platsen är tillgänglig, växlar du över till den sekundära platsen så att användarna kan fortsätta att arbeta. Du växlar tillbaka när system återgå till fungerande skick. Använd Azure Security Center för att utföra intelligent och effektiv hotidentifiering.

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory (AD Azure)](https://docs.microsoft.com/azure/active-directory/active-directory-enable-sso-scenario) är en omfattande identitet tjänsten som:

-   Aktiverar identitets- och åtkomsthantering (IAM) som en tjänst i molnet.
-   Tillhandahåller central hantering, enkel inloggning (SSO) och rapportering.
-   Stöder integrerad åtkomsthantering för [tusentals program](https://azure.microsoft.com/marketplace/active-directory/) i Azure Marketplace, inklusive Salesforce, Google Apps, rutan och Concur.

Azure AD innehåller också en fullständig uppsättning [identitetshanteringsfunktionerna](https://docs.microsoft.com/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports), inklusive följande:

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)
- [Registrering av enheten]( https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-overview)
- [Självbetjäning lösenordshantering](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Grupphantering via självbetjäning](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password)
- [Hantering av privilegierat konto](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)
- [Rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Övervakning av programanvändning](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)
- [Omfattande granskning](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
- [Säkerhetsövervakning och avisering](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts)

Med Azure Active Directory, alla program som du publicerar för partner och kunder (business eller konsumenten) har samma identitet och åtkomst hanteringsfunktioner. Detta gör att du kan avsevärt minska din driftskostnader.

## <a name="azure-security-center"></a>Azure Security Center
[Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) hjälper dig att förebygga, upptäcka och åtgärda hot med bättre överblick (och kontroll över) säkerheten för dina Azure-resurser. Det ger integrerad säkerhet övervaka och hantera principer för alla prenumerationer. Det hjälper dig att identifiera hot som annars kanske skulle förbli oupptäckta och den fungerar med ett vittomfattande ekosystem med säkerhetslösningar.

[Skydda data för virtuell dator (VM)](https://docs.microsoft.com/azure/security-center/security-center-linux-virtual-machine) i Azure genom att ge insyn i den virtuella datorns säkerhetsinställningar och övervakning för hot. Security Center kan övervaka dina virtuella datorer för:

-   Operativsystemet säkerhetsinställningar med de rekommenderade konfigurationen reglerna.
-   Systemets säkerhet och viktiga uppdateringar som saknas.
-   Rekommendationer för endpoint protection.
-   Disk encryption validering.
-   Nätverksbaserade attacker.

Security Center använder [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal). RBAC ger [inbyggda roller](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) som kan tilldelas användare, grupper och tjänster i Azure.

Security Center utvärderar konfigurationen av dina resurser för att identifiera säkerhetsproblem och säkerhetsproblem. I Security Center finns information som rör en resurs bara när tilldelas du rollen som ägare, deltagare eller läsare för prenumeration eller resursgrupp som en resurs tillhör.

>[!Note]
>Mer information om roller och tillåtna åtgärder i Security Center finns [behörigheter i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-permissions).

Security Center använder Microsoft Monitoring Agent. Det här är samma agent med Operations Management Suite och Log Analytics-tjänsten. Data som samlas in från den här agenten lagras i en befintlig logganalys [arbetsytan](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) som är associerade med din Azure-prenumeration eller en ny arbetsyta med hänsyn till geolokalisering för den virtuella datorn.

## <a name="azure-monitor"></a>Azure Monitor
Prestandaproblem i din molnapp kan påverka din verksamhet. Med flera sammankopplade komponenter och ofta versioner kan degradations inträffa när som helst. Och om du utvecklar en app användarna identifiera vanligtvis problem som du inte kan hitta vid testning. Du bör känna till om de här problemen omedelbart och du bör ha verktyg för att diagnostisera och lösa problem.

[Azure-Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) är grundläggande verktyg för att övervaka tjänster som körs på Azure. Den ger dig infrastrukturnivå data om genomflödet av en tjänst och omgivningen. Om du hanterar dina appar i Azure och bestämmer dig för att skala upp eller ned resurser, är Azure-Monitor börjar.

Du kan också använda övervakningsdata för att få djupa insikter om ditt program. Denna kunskap kan hjälpa dig att förbättra programmets prestanda eller underhålla eller automatisera åtgärder som annars skulle kräva manuella åtgärder. 

Azure-Monitor innehåller följande komponenter.

### <a name="azure-activity-log"></a>Azure-aktivitetsloggen
Den [Azure-aktivitetsloggen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger inblick i åtgärder som utfördes på resurser i din prenumeration. Den tidigare kallades ”granskningsloggen” eller ”arbetsloggen” eftersom den rapporterar kontroll-plan händelser för dina prenumerationer.

### <a name="azure-diagnostic-logs"></a>Azure diagnostikloggar
[Azure diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) orsakat av en resurs och tillhandahålla omfattande, ofta information om handhavandet av den här resursen. Innehållet i de här loggarna varierar beroende på resurstypen.

Windows-händelsesystemloggar är en kategori av diagnostiska loggar för virtuella datorer. BLOB-, tabell- och kön loggar finns kategorier av diagnostiska loggar för lagringskonton.

Diagnostikloggar skiljer sig från den [aktivitetsloggen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Aktivitetsloggen ger inblick i åtgärder som utfördes på resurser i din prenumeration. Diagnostikloggar ger kunskaper om åtgärder som din resurs utförde sig själv.

### <a name="metrics"></a>Mått
Övervakare för Azure tillhandahåller telemetri som ger inblick i prestanda och hälsotillståndet för dina arbetsbelastningar i Azure. Viktigaste Azure telemetridata är den [mått](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) (även kallade prestandaräknare) sänds av mest Azure-resurser. Azure-Monitor finns flera sätt att konfigurera och använda de här måtten för övervakning och felsökning.

### <a name="azure-diagnostics"></a>Azure Diagnostics
Azure Diagnostics aktiverar insamlingen av diagnostiska data på ett distribuerat program. Du kan använda tillägget diagnostik från olika källor. Stöds för närvarande är [Azure molntjänstroller](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [virtuella Azure-datorer](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) med Microsoft Windows och [Azure Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics).


## <a name="azure-network-watcher"></a>Azure Nätverksbevakaren
Kunder skapa ett nätverk i Azure genom att samordna och skapa enskilda nätverksresurser, till exempel virtuella nätverk, Azure ExpressRoute, Azure Application Gateway och belastningsutjämnare. Övervakning är tillgänglig på alla nätverksresurser.

Slutpunkt till slutpunkt-nätverket kan ha komplexa konfigurationer och samverkan mellan resurser. Resultatet är komplicerade scenarier som behöver scenariobaserade övervakning via [Azure Nätverksbevakaren](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).

Nätverksbevakaren förenklar övervakning och diagnos av Azure-nätverk. Du kan använda verktygen diagnostik och visualisering i Nätverksbevakaren till:
- Ta insamlingar remote paket på en virtuell Azure-dator.
- Få insikter om trafik på nätverket genom att använda flödet loggar.
- Diagnostisera Azure VPN-Gateway och anslutningar.

Nätverksbevakaren har för närvarande följande funktioner:

- [Topologi](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): en vy över de olika anslutningarna och associationer mellan nätverksresurser i en resursgrupp.
-   [Variabeln paketinsamling](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): samlar in paketdata till och från en virtuell dator. Avancerade alternativ för filtrering och finjustera kontroller, till exempel möjligheten att ange tid och ändra storlek på begränsningar ger flexibilitet. Paketdata kan lagras i en blobstore eller på den lokala disken i CAP-format.
-   [Kontrollera IP-flöde](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): kontrollerar om ett paket tillåts eller nekas baserat på 5-tuppel paket parametrarna flödet information (mål-IP, käll-IP, målport, källport och protocol). Om en säkerhetsgrupp nekar paketet, returneras regeln och grupp som nekas paketet.
-   [Nästa hopp](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): Anger nästa hopp för paket som vidarebefordras i Azure nätverksinfrastruktur, så du kan diagnostisera eventuella felkonfigurerat användardefinierade vägar.
-   [Säkerhet gruppvyn](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): hämtar effektiva och tillämpade säkerhetsregler som tillämpas på en virtuell dator.
-   [NSG-flöde loggar för nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview): gör det möjligt att samla in loggar som rör trafik som tillåts eller nekas av säkerhetsregler i gruppen. Flödet definieras av 5-tuppel-information: käll-IP, mål-IP, källport, målport och protokoll.
-   [Virtuella nätverksgatewayen och anslutningen felsökning](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): ger möjlighet att felsöka gateways för virtuella datornätverk och anslutningar.
-   [Nätverk prenumerationsbegränsningar](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): gör att du kan visa nätverksresursanvändning mot gränser.
-   [Diagnostikloggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): innehåller en om du vill aktivera eller inaktivera diagnostikloggar för nätverksresurser i en resursgrupp.

Mer information finns i [konfigurera Nätverksbevakaren](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="devops"></a>DevOps
Innan du [Developer Operations (DevOps)](https://www.visualstudio.com/learn/what-is-devops/) programutveckling, team har ansvarig för insamling av affärskraven för ett program och skriva kod. Ett separat team QA testat sedan programmet i en isolerad utvecklingsmiljö. Om kraven var uppfyllda släppte QA-teamet koden att distribuera. Distributionsgrupperna har ytterligare fragmenterad i grupper som nätverk och databasen. Varje gång ett program ”utlöstes över vägguttaget” till ett oberoende team läggs den flaskhalsar.

DevOps aktiverar team kan ge säkrare, högre kvalitet lösningar snabbare och mer låg kostnad. Kunder förväntar sig en dynamisk och tillförlitlig upplevelse när förbrukar programvaror och tjänster. Grupper måste snabbt iterera programuppdateringar och mäta effekten av uppdateringarna. De måste svara snabbt med nya development iterationer för att åtgärda problem eller ger mer värde.  

Molnplattformar, till exempel Microsoft Azure har bort traditionella flaskhalsar och hjälpt commoditize infrastruktur. Programvara prestandarik i alla företag som nyckelfaktorn och faktor i affärsresultatet. Ingen organisation, utvecklare och IT-arbetare kan eller Undvik DevOps-transport.

Mogen jobbar med DevOps anta flera av följande metoder. Dessa metoder [involvera personer](https://www.visualstudio.com/learn/what-is-devops-culture/) till formuläret strategier baserat på verksamhets scenarier. Verktygsuppsättning kan hjälpa dig att automatisera olika metoder.

-   [Flexibel planering och projekthantering](https://www.visualstudio.com/learn/what-is-agile/) tekniker som används för att planera och isolera arbete i sprints, hantera team kapacitet och hjälpa grupper snabbt anpassa sig till förändrade verksamhetsbehov.
-   [Versionskontroll, vanligtvis med Git](https://www.visualstudio.com/learn/what-is-git/), gör att grupper som helst i världen kan dela datakällan och interagera med utvecklarverktyg för programvara för att automatisera versionen pipeline.
-   [Kontinuerlig integration](https://www.visualstudio.com/learn/what-is-continuous-integration/) enheter pågående sammanslagning och testning av kod, vilket leder till att söka efter fel för tidigt.  Andra fördelar mindre tid som gått förlorat på bekämpa merge problem och snabb feedback för utvecklingsgrupper.
-   [Kontinuerlig leverans](https://www.visualstudio.com/learn/what-is-continuous-delivery/) lösningar software till produktion och tester miljöer hjälper organisationer snabbt åtgärda fel och svara på föränderliga affärsbehov.
-   [Övervaka](https://www.visualstudio.com/learn/what-is-monitoring/) för program som körs – inklusive produktion hjälper miljöer för programmets hälsotillstånd, samt kundens användning--organisationer utgör en hypotes och snabbt Validera eller disprove strategier.  Omfattande data fångas in och lagras i olika format för loggning.
-   [Infrastruktur som kod (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) är en metod som gör det möjligt för automatisering och validering skapas och teardown av nätverk och virtuella datorer för att leverera säker och stabil program som är värd för plattformar.
-   [Mikrotjänster](https://www.visualstudio.com/learn/what-are-microservices/) arkitektur som används för att isolera användningsområden i små återanvändbara tjänster.  Den här arkitekturen möjliggör skalbarhet och effektivitet.

## <a name="next-steps"></a>Nästa steg
Mer information om Operations Management Suite säkerhet och granska lösningen, finns i följande artiklar:

- [Säkerhet och efterlevnad](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Övervaka och åtgärda säkerhetsaviseringar i lösningen Operations Management Suite säkerhets- och granskning](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts)
- [Övervaka resurser i lösningen Operations Management Suite säkerhets- och granskning](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources)
