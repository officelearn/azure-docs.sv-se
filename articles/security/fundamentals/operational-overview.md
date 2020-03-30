---
title: Översikt över driftsäkerhet i Azure| Microsoft-dokument
description: Den här artikeln innehåller en översikt över Azures driftsäkerhet.
services: security
documentationcenter: na
author: unifycloud
manager: rkarlin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: tomsh
ms.openlocfilehash: bcaf45af4c02dad22e2cc611fa7ea5a32ad3a853
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443871"
---
# <a name="azure-operational-security-overview"></a>Översikt över Azure-driftsäkerhet

[Azure-driftsäkerhet](/azure/security/fundamentals/operational-security) refererar till tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda deras data, program och andra resurser i Microsoft Azure. Det är ett ramverk som innehåller den kunskap som erhållits genom en mängd olika funktioner som är unika för Microsoft. Dessa funktioner omfattar Microsoft Security Development Lifecycle (SDL), Microsoft Security Response Center-programmet och djup medvetenhet om cybersäkerhetshotlandskapet.

## <a name="azure-management-services"></a>Azure-hanteringstjänster

Ett IT-driftteam ansvarar för att hantera datacenterinfrastruktur, program och data, inklusive stabiliteten och säkerheten för dessa system. Men för att få säkerhetsinsikter i ökande komplexa IT-miljöer krävs det ofta att organisationer snickrar ihop data från flera säkerhets- och hanteringssystem.

[Microsoft Azure Monitor-loggar](/azure/operations-management-suite/operations-management-suite-overview) är en molnbaserad IT-hanteringslösning som hjälper dig att hantera och skydda din lokala infrastruktur och molninfrastruktur. Dess kärnfunktioner tillhandahålls av följande tjänster som körs i Azure. Azure innehåller flera tjänster som hjälper dig att hantera och skydda din lokala infrastruktur och molninfrastruktur. Varje tjänst tillhandahåller en specifik hanteringsfunktion. Du kan kombinera tjänster för att uppnå olika hanteringsscenarier. 

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](/azure/azure-monitor/overview) samlar in data från hanterade källor i centrala datalager. Dessa data kan innehålla händelser, prestandadata eller anpassade data som tillhandahålls via API:et. När data har samlats in är den tillgänglig för aviseringar, analyser och export.

Du kan konsolidera data från en mängd olika källor och kombinera data från dina Azure-tjänster med din befintliga lokala miljö. Azure Monitor loggar också tydligt separerar insamlingen av data från de åtgärder som vidtagits på dessa data, så att alla åtgärder är tillgängliga för alla typer av data.

### <a name="automation"></a>Automation

[Azure Automation](/azure/automation/automation-intro) är ett sätt för dig att automatisera manuella, långvariga, felbenägna och ofta upprepade uppgifter som ofta utförs i en moln- och företagsmiljö. Det sparar tid och ökar tillförlitligheten i administrativa uppgifter. Det schemalägger även dessa uppgifter som ska utföras automatiskt med jämna mellanrum. Du kan automatisera processer genom att använda runbooks eller automatisera konfigurationshantering med hjälp av Önskad tillståndskonfiguration.

### <a name="backup"></a>Säkerhetskopiering

[Azure Backup](/azure/backup/backup-introduction-to-azure-backup) är den Azure-baserade tjänst som du kan använda för att säkerhetskopiera (eller skydda) och återställa dina data i Microsoft Cloud. Azure Backup ersätter din befintliga lokala eller off-site backup-lösning med en molnbaserad lösning som är tillförlitlig, säker och kostnadseffektiv.

Azure Backup erbjuder komponenter som du hämtar och distribuerar på rätt dator eller server eller i molnet. Komponenten eller agenten som du distribuerar beror på vad du vill skydda. Alla Azure Backup-komponenter (oavsett om du skyddar data lokalt eller i molnet) kan användas för att säkerhetskopiera data till ett Azure Recovery Services-valv i Azure.

Mer information finns i [tabellen Azure Backup components](/azure/backup/backup-overview#what-can-i-back-up).

### <a name="site-recovery"></a>Site Recovery

[Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) ger affärskontinuitet genom att dirigera replikering av lokala virtuella och fysiska datorer till Azure eller till en sekundär plats. Om din primära webbplats inte är tillgänglig växlar du över till den sekundära platsen så att användarna kan fortsätta arbeta. Du misslyckas tillbaka när systemen återgår till funktionsdugligt skick. Använd Azure Security Center för att utföra mer intelligent och effektiv hotidentifiering.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD)](/azure/active-directory/active-directory-enable-sso-scenario) är en omfattande identitetstjänst som:

-   Aktiverar identitets- och åtkomsthantering (IAM) som en molntjänst.
-   Tillhandahåller central åtkomsthantering, enkel inloggning (SSO) och rapportering.
-   Stöder integrerad åtkomsthantering för [tusentals program](https://azure.microsoft.com/marketplace/active-directory/) på Azure Marketplace, inklusive Salesforce, Google Apps, Box och Concur.

Azure AD innehåller också en komplett uppsättning [funktioner för identitetshantering,](/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports)inklusive följande:

- [Multifaktorautentisering](/azure/multi-factor-authentication/multi-factor-authentication)
- [Lösenordshantering via självbetjäning](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Grupphantering med självbetjäning](/azure/active-directory/active-directory-passwords-update-your-own-password)
- [Hantering av privilegierade konton](/azure/active-directory/active-directory-privileged-identity-management-configure)
- [Rollbaserad åtkomstkontroll](/azure/role-based-access-control/overview)
- [Övervakning av programanvändning](/azure/active-directory/connect-health/active-directory-aadconnect-health)
- [Omfattande granskning](/azure/active-directory/active-directory-reporting-activity-audit-logs)
- [Övervakning och avisering av säkerhet](/azure/operations-management-suite/oms-security-responding-alerts)

Med Azure Active Directory har alla program som du publicerar för dina partner och kunder (företag eller konsumenter) samma funktioner för identitets- och åtkomsthantering. På så sätt kan du avsevärt minska dina driftskostnader.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](/azure/security-center/security-center-intro) hjälper dig att förebygga, identifiera och svara på hot med ökad insyn i (och kontroll över) säkerheten för dina Azure-resurser. Det ger integrerad säkerhetsövervakning och principhantering över dina prenumerationer. Det hjälper till att upptäcka hot som annars skulle gå obemärkt förbi, och det fungerar med ett brett ekosystem av säkerhetslösningar.

[Skydda virtuella datordata (VM)](/azure/security-center/security-center-linux-virtual-machine) i Azure genom att ge insyn i den virtuella datorns säkerhetsinställningar och övervakning för hot. Security Center kan övervaka dina virtuella datorer för:

- Säkerhetsinställningar för operativsystemet med de rekommenderade konfigurationsreglerna.
- Systemsäkerhet och viktiga uppdateringar som saknas.
- Skyddsrekommendationer för slutpunktsskyddet.
- Validering av diskkryptering.
- Nätverksbaserade attacker.

Security Center använder [rollbaserad åtkomstkontroll (RBAC)](/azure/role-based-access-control/role-assignments-portal). RBAC tillhandahåller [inbyggda roller](../../role-based-access-control/built-in-roles.md) som kan tilldelas användare, grupper och tjänster i Azure.

Security Center utvärderar konfigurationen av dina resurser för att identifiera säkerhetsproblem och säkerhetsproblem. I Security Center visas endast information som är relaterad till en resurs när du har tilldelat rollen som ägare, deltagare eller läsare för den prenumeration eller resursgrupp som en resurs tillhör.

>[!Note]
>Mer information om roller och tillåtna åtgärder i Security Center finns [i Behörigheter i Azure Security Center](/azure/security-center/security-center-permissions).

Security Center använder Microsoft Monitoring Agent. Det här är samma agent som Azure Monitor-tjänsten använder. Data som samlas in från den här agenten lagras i antingen en befintlig Log [Analytics-arbetsyta](/azure/log-analytics/log-analytics-manage-access) som är associerad med din Azure-prenumeration eller en ny arbetsyta, med hänsyn till den virtuella datorns geolokalisering.

## <a name="azure-monitor"></a>Azure Monitor

Prestandaproblem i molnappen kan påverka ditt företag. Med flera sammankopplade komponenter och frekventa versioner kan nedbrytningar inträffa när som helst. Och om du utvecklar en app upptäcker användarna vanligtvis problem som du inte hittade i testningen. Du bör veta om dessa frågor omedelbart, och du bör ha verktyg för att diagnostisera och åtgärda problemen.

[Azure Monitor](/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) är ett grundläggande verktyg för att övervaka tjänster som körs på Azure. Det ger dig data på infrastrukturnivå om dataflödet för en tjänst och den omgivande miljön. Om du hanterar dina appar i Azure och bestämmer om du ska skala upp eller ned resurser är Azure Monitor platsen att börja på.

Du kan också använda övervakningsdata för att få djupa insikter om ditt program. Den kunskapen kan hjälpa dig att förbättra programmets prestanda eller underhåll, eller automatisera åtgärder som annars skulle kräva manuella åtgärder.

Azure Monitor innehåller följande komponenter.

### <a name="azure-activity-log"></a>Azure-aktivitetslogg

[Azure-aktivitetsloggen](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger insikt i de åtgärder som utfördes på resurser i din prenumeration. Det var tidigare känt som "Granskningslogg" eller "Operativ logg", eftersom den rapporterar kontrollplanshändelser för dina prenumerationer.

### <a name="azure-diagnostic-logs"></a>Azure diagnostikloggar

[Azure-diagnostikloggar](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) genereras av en resurs och tillhandahåller omfattande, frekventa data om driften av den resursen. Innehållet i dessa loggar varierar beroende på resurstyp.

Windows händelsesystemloggar är en kategori av diagnostikloggar för virtuella datorer. Blob- och tabellloggar är kategorier av diagnostikloggar för lagringskonton.

Diagnostikloggar skiljer sig från [aktivitetsloggen](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Aktivitetsloggen ger insikt i de åtgärder som utfördes på resurser i din prenumeration. Diagnostikloggar ger insikt i åtgärder som din resurs utförde själv.

### <a name="metrics"></a>Mått

Azure Monitor tillhandahåller telemetri som ger dig insyn i prestanda och hälsa för dina arbetsbelastningar på Azure. Den viktigaste typen av Azure telemetridata är [måtten](/azure/monitoring-and-diagnostics/monitoring-overview-metrics) (även kallade prestandaräknare) som avges av de flesta Azure-resurser. Azure Monitor innehåller flera sätt att konfigurera och använda dessa mått för övervakning och felsökning.

### <a name="azure-diagnostics"></a>Microsoft Azure Diagnostics

Azure Diagnostics möjliggör insamling av diagnostikdata för ett distribuerat program. Du kan använda tillägget Diagnostik från olika källor. För närvarande stöds [Azure-molntjänstroller,](/azure/vs-azure-tools-configure-roles-for-cloud-service) [virtuella Azure-datorer](/azure/vs-azure-tools-configure-roles-for-cloud-service) som kör Microsoft Windows och [Azure Service Fabric](/azure/monitoring-and-diagnostics/azure-diagnostics).

## <a name="azure-network-watcher"></a>Azure Network Watcher

Kunder skapar ett heltäckande nätverk i Azure genom att orkestrera och komponera enskilda nätverksresurser som virtuella nätverk, Azure ExpressRoute, Azure Application Gateway och belastningsutjämnare. Övervakning är tillgänglig på var och en av nätverksresurserna.

Det end-to-end-nätverket kan ha komplexa konfigurationer och interaktioner mellan resurser. Resultatet är komplexa scenarier som behöver scenariobaserad övervakning via [Azure Network Watcher](/azure/network-watcher/network-watcher-monitoring-overview).

Network Watcher förenklar övervakning och diagnos av ditt Azure-nätverk. Du kan använda diagnostik- och visualiseringsverktygen i Network Watcher för att:

- Ta fjärrpaketinsamlingar på en virtuell Azure-dator.
- Få insikter om nätverkstrafiken med hjälp av flödesloggar.
- Diagnostisera Azure VPN Gateway och anslutningar.

Network Watcher har för närvarande följande funktioner:

- [Topologi](/azure/network-watcher/network-watcher-topology-overview): Ger en vy över de olika sammanlänkningarna och associationerna mellan nätverksresurser i en resursgrupp.
- [Variabel paketfångst:](/azure/network-watcher/network-watcher-packet-capture-overview)Samlar in paketdata in och ut från en virtuell dator. Avancerade alternativ för filtrering och finjusterade kontroller, till exempel möjligheten att ange tid- och storleksbegränsningar, ger flexibilitet. Paketdata kan lagras i ett blob-arkivet eller på den lokala disken i .cap-format.
- [IP-flöde verifiera:](/azure/network-watcher/network-watcher-ip-flow-verify-overview)Kontrollerar om ett paket tillåts eller nekas baserat på 5-tuppel paketparametrar för flödesinformation (mål-IP, käll-IP, målport, källport och protokoll). Om en säkerhetsgrupp nekar paketet returneras regeln och gruppen som nekade paketet.
- [Nästa hopp:](/azure/network-watcher/network-watcher-next-hop-overview)Bestämmer nästa hopp för paket som dirigeras i Azure-nätverksinfrastrukturen, så att du kan diagnostisera eventuella felkonfigurerade användardefinierade vägar.
- [Säkerhetsgruppsvyn](/azure/network-watcher/network-watcher-security-group-view-overview): Hämtar effektiva och tillämpade säkerhetsregler som tillämpas på en virtuell dator.
- [NSG-flödesloggar för nätverkssäkerhetsgrupper](/azure/network-watcher/network-watcher-nsg-flow-logging-overview): Gör att du kan samla in loggar relaterade till trafik som tillåts eller nekas av säkerhetsreglerna i gruppen. Flödet definieras av 5-tuppelinformation: käll-IP, mål-IP, källport, målport och protokoll.
- [Felsökning av virtuell nätverksgateway och anslutning](/azure/network-watcher/network-watcher-troubleshoot-manage-rest): Ger möjlighet att felsöka virtuella nätverksgateways och anslutningar.
- [Begränsningar för nätverksprenumeration:](/azure/network-watcher/network-watcher-monitoring-overview)Gör att du kan visa användning av nätverksresurser mot gränser.
- [Diagnostikloggar](/azure/network-watcher/network-watcher-monitoring-overview): Tillhandahåller en enda ruta för att aktivera eller inaktivera diagnostikloggar för nätverksresurser i en resursgrupp.

Mer information finns i [Konfigurera network watcher](/azure/network-watcher/network-watcher-create).

## <a name="cloud-service-provider-access-transparency"></a>Åtkomst till molntjänstleverantör

[Customer Lockbox för Microsoft Azure](customer-lockbox-overview.md) är en tjänst som är integrerad i Azure-portalen och som ger dig explicit kontroll i den sällsynta instansen när en Microsoft Support Engineer kan behöva åtkomst till dina data för att lösa ett problem.
Det finns mycket få instanser, till exempel ett felsökningsfjärråtkomstproblem, där en Microsoft Support Engineer kräver förhöjda behörigheter för att lösa problemet. I sådana fall använder Microsoft-tekniker just-in-time-åtkomsttjänsten som ger begränsad, tidsbunden auktorisering med åtkomst begränsad till tjänsten.  
Microsoft har alltid fått kundens medgivande för åtkomst, men Customer Lockbox ger dig nu möjlighet att granska och godkänna eller neka sådana begäranden från Azure Portal. Microsofts supporttekniker beviljas inte åtkomst förrän du godkänner begäran.

## <a name="standardized-and-compliant-deployments"></a>Standardiserade och kompatibla distributioner

[Azure Blueprints](/azure/governance/blueprints/overview) gör det möjligt för molnarkitekter och centrala it-grupper att definiera en repeterbar uppsättning Azure-resurser som implementerar och följer en organisations standarder, mönster och krav.  
Detta gör det möjligt för DevOps-team att snabbt bygga och stå upp nya miljöer och lita på att de bygger dem med infrastruktur som upprätthåller organisationens efterlevnad.
Skisser är ett deklarativt sätt att dirigera distributionen av olika resursmallar och andra artefakter, till exempel:

- Rolltilldelningar
- Principtilldelningar
- Azure Resource Manager-mallar
- Resursgrupper

## <a name="devops"></a>DevOps

Innan [Developer Operations (DevOps) applikationsutveckling,](https://www.visualstudio.com/learn/what-is-devops/) var team som ansvarar för att samla in affärskrav för ett program och skriva kod. Sedan testade ett separat QA-team programmet i en isolerad utvecklingsmiljö. Om kraven var uppfyllda släppte QA-teamet koden för åtgärder som ska distribueras. Distributionsteamen var ytterligare fragmenterade i grupper som nätverk och databas. Varje gång ett program "kastades över väggen" till ett oberoende team, la det flaskhalsar.

DevOps gör det möjligt för team att leverera säkrare lösningar av högre kvalitet snabbare och billigare. Kunderna förväntar sig en dynamisk och pålitlig upplevelse när de konsumerar programvara och tjänster. Team måste snabbt iterera på programuppdateringar och mäta effekterna av uppdateringarna. De måste svara snabbt med nya utvecklingsiterationer för att lösa problem eller ge mer värde.  

Molnplattformar som Microsoft Azure har tagit bort traditionella flaskhalsar och hjälpt till att commoditize infrastruktur. Programvara regerar i varje företag som den viktigaste differentiator och faktor i affärsresultat. Ingen organisation, utvecklare eller IT-arbetare kan eller bör undvika DevOps-rörelsen.

Mogna DevOps utövare anta flera av följande metoder. Dessa metoder [involverar människor](https://www.visualstudio.com/learn/what-is-devops-culture/) att bilda strategier baserat på affärsscenarier. Verktyg kan hjälpa till att automatisera de olika metoderna.

- [Agila planerings- och projektledningstekniker](https://www.visualstudio.com/learn/what-is-agile/) används för att planera och isolera arbetet i sprintar, hantera teamkapacitet och hjälpa team att snabbt anpassa sig till förändrade affärsbehov.
- [Versionskontroll, vanligtvis med Git](https://www.visualstudio.com/learn/what-is-git/), gör det möjligt för team som finns var som helst i världen att dela källa och integrera med mjukvaruutvecklingsverktyg för att automatisera versionspipelinen.
- [Kontinuerlig integrering](https://www.visualstudio.com/learn/what-is-continuous-integration/) driver pågående sammanslagning och testning av kod, vilket leder till att hitta defekter tidigt.  Andra fördelar är mindre tid slösas bort på att bekämpa sammanfogning frågor och snabb feedback för utvecklingsteam.
- [Kontinuerlig leverans](https://www.visualstudio.com/learn/what-is-continuous-delivery/) av programvarulösningar till produktions- och testmiljöer hjälper organisationer att snabbt åtgärda fel och svara på ständigt föränderliga affärskrav.
- [Övervakning](https://www.visualstudio.com/learn/what-is-monitoring/) av program som körs – inklusive produktionsmiljöer för programhälsa, samt kundanvändning – hjälper organisationer att bilda en hypotes och snabbt validera eller motbevisa strategier.  Omfattande data samlas in och lagras i olika loggningsformat.
- [Infrastruktur som kod (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) är en praxis som gör det möjligt att automatisera och validera skapande och rivning av nätverk och virtuella datorer för att hjälpa till med att leverera säkra, stabila programvärdplattformar.
- [Arkitekturen Mikrotjänster](https://www.visualstudio.com/learn/what-are-microservices/) används för att isolera affärsanvändningsfall till små återanvändningsbara tjänster.  Den här arkitekturen möjliggör skalbarhet och effektivitet.

## <a name="next-steps"></a>Nästa steg

Mer information om lösningen Säkerhet och granskning finns i följande artiklar:

- [Säkerhet och efterlevnad](https://azure.microsoft.com/overview/trusted-cloud/)
- [Azure Security Center](/azure/security-center/security-center-intro)
- [Azure Monitor](/azure/azure-monitor/overview)
