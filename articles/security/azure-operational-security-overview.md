---
title: Översikt över Azure driftsäkerhet | Microsoft Docs
description: Den här artikeln innehåller en översikt över Azure driftsäkerhet.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: tomsh
ms.openlocfilehash: da58737314012255f26d344f279d855244f0c1ba
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56116481"
---
# <a name="azure-operational-security-overview"></a>Översikt över Azure driftsäkerhet

[Azure driftsäkerhet](https://docs.microsoft.com/azure/security/azure-operational-security) refererar till de tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda sina data, program och andra resurser i Microsoft Azure. Det är ett ramverk som innehåller den kunskap som en mängd funktioner som är unika för Microsoft. Funktionerna omfattar Microsoft Security Development Lifecycle (SDL), Microsoft Security Response Center-program och djup medvetenhet om hotlandskapet.

## <a name="azure-management-services"></a>Azure-hanteringstjänster

En IT-driftsteamet ansvarar för att hantera infrastruktur med datacenter, program och data, inklusive stabilitet och säkerheten för dessa system. Informationshämtning säkerhet över ökar komplexa IT-miljöer ofta kräver dock organisationer att cobble ihop data från flera system för säkerhet och hantering.

[Microsoft Log Analytics](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) är en molnbaserad IT-hanteringslösning som hjälper dig att hantera och skydda dina lokala och molnbaserade infrastruktur. Dess huvudfunktioner tillhandahåller följande tjänster som körs i Azure. Azure innehåller flera tjänster som hjälper dig att hantera och skydda dina lokala och molnbaserade infrastruktur. Varje tjänst tillhandahåller en specifik hanteringsfunktion. Du kan kombinera tjänster för olika hanteringsscenarier. 

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) samlar in data från hanterade källor till centrala datalager. Dessa data kan omfatta händelser, prestandadata eller anpassade data via API: et. När data har samlats in, är den tillgänglig för avisering, analys och export. 

Du kan konsolidera data från olika källor och kombinera data från dina Azure-tjänster med din befintliga lokala miljö. Log Analytics skiljer också en tydlig insamling av data från de åtgärder som vidtagits på dessa data så att alla åtgärder är tillgängliga för alla typer av data.

### <a name="automation"></a>Automation

[Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) gör det möjligt för dig att automatisera manuella, tidskrävande, felbenägna och regelbundet återkommande uppgifter som är vanliga i en moln- och enterprise-miljö. Det sparar tid och ökar tillförlitligheten för administrativa uppgifter. Det schemalägger även dessa uppgifter utförs automatiskt med jämna mellanrum. Du kan automatisera processer med hjälp av runbooks eller automatisera konfigurationshantering med Desired State Configuration.

### <a name="backup"></a>Backup

[Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) är den Azure-baserad tjänst som du kan använda för att säkerhetskopiera (eller skydda) och återställa data i Microsoft Cloud. Azure Backup ersätter din befintliga lokala eller säkerhetskopieringslösning med en molnbaserad lösning som är tillförlitlig, säker och konkurrenskraftig. 

Azure Backup erbjuder komponenter som du hämtar och distribuerar på den aktuella datorn eller servern eller i molnet. Komponenten eller agenten som du distribuerar beror på vad du vill skydda. Alla Azure Backup-komponenter kan (oavsett om du skyddar data lokalt eller i molnet) användas för att säkerhetskopiera data till ett Azure Recovery Services-valv i Azure. 

Mer information finns i den [Azure Backup-komponenter tabell](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup#which-azure-backup-components-should-i-use).

### <a name="site-recovery"></a>Site Recovery

[Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) ger kontinuitet för företag genom att samordna replikeringen av lokala virtuella och fysiska datorer till Azure eller till en sekundär plats. Om din primära plats är tillgänglig, växlar du över till den sekundära platsen så att användarna kan fortsätta att arbeta. Du växlar tillbaka när systemen fungerar korrekt. Använd Azure Security Center för att utföra mer intelligenta och effektiva hotidentifiering.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-enable-sso-scenario) är en omfattande identitets-tjänst som:

-   Gör det möjligt för identitets- och åtkomsthantering (IAM) som en tjänst i molnet.
-   Ger central hantering, enkel inloggning (SSO) och rapportering.
-   Stöder integrerad åtkomsthantering för [tusentals program](https://azure.microsoft.com/marketplace/active-directory/) på Azure Marketplace, inklusive Salesforce, Google Apps, Box och Concur.

Azure AD innehåller också en komplett uppsättning [identitetshanteringsfunktioner](https://docs.microsoft.com/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports), inklusive följande:

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)
- [Egen lösenordshantering](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Grupphantering](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password)
- [Hantering av Privilegierade](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)
- [Rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Övervakning av programanvändning](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)
- [Omfattande granskning](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
- [Säkerhetsövervakning och avisering](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts)

Med Azure Active Directory, alla program som du publicerar för dina partner och kunder (företag eller konsumenter) får samma identitet och åtkomst till funktioner för hantering. På så sätt kan du avsevärt minska dina driftskostnader.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) hjälper dig att förhindra, upptäcka och svara på hot med ökad insyn i (och kontroll över) säkerheten för dina Azure-resurser. Det ger integrerad säkerhet övervaka och hantera principer för dina prenumerationer. Det hjälper dig att identifiera hot som kan annars oupptäckta och de fungerar med ett vittomfattande ekosystem med säkerhetslösningar.

[Skydda data i virtuella datorer (VM)](https://docs.microsoft.com/azure/security-center/security-center-linux-virtual-machine) i Azure genom att ge bättre inblick i den virtuella datorns säkerhetsinställningar och övervakning för hot. Security Center kan övervaka dina virtuella datorer för:

- Säkerhetsinställningar för operativsystemet med de rekommenderade konfigurationsreglerna.
- Systemsäkerhet och viktiga uppdateringar som saknas.
- Rekommendationer om endpoint protection.
- Verifiering av diskkryptering.
- Nätverksbaserade attacker.

Security Center använder [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal). RBAC ger [inbyggda roller](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) som kan tilldelas till användare, grupper och tjänster i Azure.

Security Center utvärderar konfigurationen av dina resurser för att identifiera säkerhetsproblem och säkerhetsproblem. I Security Center kan se du information relaterad till en resurs endast när du har tilldelat rollen ägare, deltagare eller läsare för prenumeration eller resursgrupp som en resurs hör till.

>[!Note]
>Mer information om roller och tillåtna åtgärder i Security Center finns i [behörigheter i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-permissions).

Security Center använder Microsoft Monitoring Agent. Det här är samma agent som använder tjänsten Log Analytics. Data som samlas in från agenten lagras i en befintlig logganalys [arbetsytan](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) som är associerade med din Azure-prenumeration eller en ny arbetsyta med hänsyn till geoplats för den virtuella datorn.

## <a name="azure-monitor"></a>Azure Monitor

Prestandaproblem i din molnapp kan påverka din verksamhet. Med flera sammankopplade komponenter och ofta versioner kan försämringar inträffa när som helst. Och om du utvecklar en app kan användarna identifiera vanligtvis problem som du hittade i testet. Du bör veta om de här problemen omedelbart och du bör ha verktyg för att diagnostisera och lösa problem.

[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) är grundläggande verktyg för att övervaka tjänster som körs på Azure. Det ger dig infrastrukturnivå om dataflödet för en tjänst och den omgivande miljön. Om du hanterar dina appar på Azure och bestämma om du vill skala upp eller ned resurser, är Azure Monitor ställe att börja.

Du kan också använda övervakningsdata för att få djupa insikter om ditt program. Denna kunskap kan hjälpa dig att förbättra programmets prestanda och underhåll eller automatisera åtgärder som annars skulle kräva manuella åtgärder. 

Azure Monitor innehåller följande komponenter.

### <a name="azure-activity-log"></a>Azure-aktivitetsloggen

Den [Azure-aktivitetsloggen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger insikt i de åtgärder som utförts på resurser i din prenumeration. Den tidigare kallades ”granskningsloggen” eller ”arbetsloggen” eftersom den rapporterar kontrollplanet händelser för dina prenumerationer.

### <a name="azure-diagnostic-logs"></a>Azure diagnostikloggar

[Azure diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) genereras av en resurs och tillhandahåller omfattande, frekventa data om användningen av den här resursen. Innehållet i de här loggarna varierar efter resurstyp.

Windows-händelsesystemloggar är en kategori för diagnostikloggar för virtuella datorer. BLOB, tabell och kö-loggar finns kategorier av diagnostikloggar för storage-konton.

Diagnostikloggar skiljer sig från den [aktivitetsloggen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Aktivitetsloggen ger insikt i de åtgärder som utförts på resurser i din prenumeration. Diagnostikloggar ger information om åtgärder som din resurs har vidtagit själva.

### <a name="metrics"></a>Mått

Azure Monitor innehåller telemetri som ger dig insyn i prestanda och hälsa för dina arbetsbelastningar på Azure. Viktigaste Azure telemetridata är den [mått](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) (kallas även prestandaräknare) som genereras av de flesta Azure-resurser. Azure Monitor innehåller flera olika sätt att konfigurera och använda de här måtten för övervakning och felsökning.

### <a name="azure-diagnostics"></a>Azure Diagnostics

Azure-diagnostik aktiverar insamlingen av diagnostikdata på ett distribuerat program. Du kan använda diagnostiktillägget från olika källor. För närvarande är [Azure molntjänstroller](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [Azure-datorer](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) som kör Microsoft Windows och [Azure Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics).

## <a name="azure-network-watcher"></a>Azure Network Watcher

Kunder skapar en slutpunkt till slutpunkt-nätverk i Azure genom att samordna och skapa enskilda nätverksresurser, till exempel virtuella nätverk, Azure ExpressRoute, Azure Application Gateway och belastningsutjämnare. Övervakning är tillgängliga på var och en av nätverksresurserna.

Slutpunkt till slutpunkt-nätverk kan ha komplexa konfigurationer och interaktioner mellan resurser. Resultatet är komplicerade scenarier som behöver scenariobaserade övervakning via [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).

Network Watcher förenklar övervakning och diagnos av din Azure-nätverk. Du kan använda verktyg för diagnostik- och visualiseringsverktygen i Network Watcher till:

- Ta remote-infångade på virtuella Azure-datorer.
- Få insikter i din nätverkstrafik med flödesloggar.
- Diagnostisera Azure VPN Gateway och anslutningar.

Network Watcher har för närvarande följande funktioner:

- [Topologi](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): Ger en överblick över de olika anslutningarna och associationer mellan nätverksresurser i en resursgrupp.
- [Variabla infångade paket](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): Samlar in paketdata till och från en virtuell dator. Avancerade alternativ för filtrering och finjusterade kontroller, till exempel möjligheten att ange tid- och storleksbegränsningar, ger flexibilitet. Paketdata kan lagras i en blob store eller på den lokala disken i CAP-format.
- [Kontrollera IP-flöde](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): Kontrollerar om ett paket tillåts eller nekas baserat på 5-tuppel paket parametrar för flödesinformation (mål-IP, käll-IP, målport, källport och protokoll). Om en säkerhetsgrupp nekar paketet, returneras regel och grupp som nekade paketet.
- [Nästa hopp](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): Anger nästa hopp för paket som vidarebefordras i Azure nätverksinfrastruktur, så du kan diagnostisera eventuella felkonfigurerad användardefinierade vägar.
- [Säkerhetsgruppvy](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): Hämtar de effektiva och tillämpade säkerhetsregler som tillämpas på en virtuell dator.
- [NSG-flödesloggar för nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview): Kan du samla in loggar som rör trafik som tillåts eller nekas av säkerhetsregler i gruppen. Flödet har definierats av information om 5-tuppel: käll-IP, mål-IP, källport, målport och protokoll.
- [Virtuell nätverksgateway och anslutning felsökning](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): Ger möjlighet att felsöka virtuella nätverksgatewayer och anslutningar.
- [Nätverks-prenumerationsbegränsningar](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Kan du visa nätverksresursanvändning mot gränser.
- [Diagnostikloggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Tillhandahåller ett fönster för att aktivera eller inaktivera diagnostikloggar för nätverksresurser i en resursgrupp.

Mer information finns i [konfigurera Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="cloud-service-provider-access-transparency"></a>Cloud Service Provider åtkomst transparens

[Customer Lockbox för Microsoft Azure](https://azure.microsoft.com/blog/approve-audit-support-access-requests-to-vms-using-customer-lockbox-for-azure/) är en tjänst som är integrerad i Azure portal som ger dig explicit kontroll i sällsynta fall när en Microsoft Support-tekniker kan behöva åtkomst till dina data att lösa ett problem. Det finns mycket få instanser, till exempel ett felsökning fjärråtkomst problem där en Microsoft Support-tekniker kräver förhöjd behörighet att lösa problemet. I sådana fall kan använda Microsoft-tekniker just-in-time-åtkomst-tjänst som tillhandahåller begränsade, tidsbegränsade auktorisering med åtkomst begränsas till tjänsten.  
När Microsoft har alltid hämtas kundernas medgivande för åtkomst, ger Customer Lockbox nu dig möjlighet att granska och godkänna eller neka sådana begäranden från Azure Portal. Microsofts supporttekniker kommer inte beviljas åtkomst tills du har godkänt förfrågan.

## <a name="standardized-and-compliant-deployments"></a>Standardiserad och godkända distributioner

[Azure skisser](../governance/blueprints/overview.md) aktivera molnarkitekter och central information technology grupper att definiera en upprepningsbara uppsättning Azure-resurser som implementerar och följa en organisations standarder, mönster och krav.  
Detta gör det möjligt för DevOps-team att snabbt bygga och skapa nya miljöer och lita på att de skapar dem med infrastruktur som underhåller organisatorisk efterlevnad. Skisser tillhandahåller en deklarativ metod för att dirigera distributionen av olika resursmallar och andra artefakter som: 

- Rolltilldelningar
- Principtilldelningar
- Azure Resource Manager-mallar
- Resursgrupper

## <a name="devops"></a>DevOps

Innan du [Developer-åtgärder (DevOps)](https://www.visualstudio.com/learn/what-is-devops/) programutveckling, team har ansvarar för samla in företagets krav på ett visst program och skriva kod. Ett separat team QA testat sedan programmet i en isolerad utvecklingsmiljö. Om kraven var uppfyllda, publiceras koden att distribuera QA-teamet. Distributionsgrupperna har ytterligare fragmenterad i grupper som nätverk och databas. Varje gång som ett program ”uppstod under väggen” till en oberoende-teamet, läggs den flaskhalsar.

DevOps gör det möjligt att leverera lösningar för säkrare, högre kvalitet snabbare och lägre priser. Kunderna förväntar sig en dynamisk och tillförlitlig upplevelse när programvara och tjänster. Team måste snabbt iterera programuppdateringar och mäta effekten av uppdateringarna. De måste svara snabbt med den nya utveckling iterationer att åtgärda problem med eller mer värde.  

Molnplattformar, till exempel Microsoft Azure har bort traditionella flaskhalsar och hjälpte commoditize infrastruktur. Programvara prestandarik i alla företag som nyckelfaktorn och faktor för affärsresultat. Ingen organisation, utvecklare eller IT-arbetare kan eller bör undvika att flytta DevOps.

Mogen DevOps läkare anta flera av följande metoder. Dessa metoder [omfattar personer](https://www.visualstudio.com/learn/what-is-devops-culture/) till formuläret strategier baserat på verksamhets scenarier. Verktyg kan hjälpa dig att automatisera olika metoder.

- [Flexibel planering och projektledning](https://www.visualstudio.com/learn/what-is-agile/) tekniker som används för att planera och isolera arbete i sprintar, hantera team kapacitet och kan dina team snabbt anpassa sig till förändrade verksamhetsbehov.
- [Versionskontroll, vanligtvis med Git](https://www.visualstudio.com/learn/what-is-git/), gör det möjligt finnas var som helst i världen kan dela källa och integrera med programvara utvecklingsverktyg för att automatisera versionspipelinen.
- [Kontinuerlig integrering](https://www.visualstudio.com/learn/what-is-continuous-integration/) Driver pågående sammanslagning och testning av kod, vilket leder till att söka efter fel för tidigt.  Andra fördelar mindre tid som gått på bekämpa merge problem och snabb feedback för utvecklingsteam.
- [Kontinuerlig leverans](https://www.visualstudio.com/learn/what-is-continuous-delivery/) av programvarulösningar till produktion och testning miljöer hjälper att organisationer snabbt åtgärda buggar och svara på ständigt föränderliga affärsbehov.
- [Övervaka](https://www.visualstudio.com/learn/what-is-monitoring/) av program som körs – inklusive produktion hjälper miljöer för programmets hälsotillstånd, samt KUNDANVÄNDNING--organisationer utgör en hypotes och snabbt Validera eller disprove strategier.  Omfattande data inhämtas och lagras i olika format för loggning.
- [Infrastruktur som kod (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) är ett tips som gör det möjligt för automatisering och verifiering av skapande och teardown av nätverk och virtuella datorer för att hjälpa med att leverera säker och stabil program som är värd för plattformar.
- [Mikrotjänster](https://www.visualstudio.com/learn/what-are-microservices/) arkitektur som används för att isolera användningsområden i små återanvändbara tjänster.  Den här arkitekturen möjliggör skalbarhet och effektivitet.

## <a name="next-steps"></a>Nästa steg

Läs om lösningen för säkerhet och granskning i följande artiklar:

- [Säkerhet och efterlevnad](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)
