---
title: Metodtips för säkerhet för dina Azure-resurser
titleSuffix: Azure security
description: Den här artikeln innehåller en uppsättning metodtips för att skydda data, program och andra resurser i Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 72d7a2dd112e5e7a5105ff977e3917ccdfd7b53e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77500296"
---
# <a name="azure-operational-security-best-practices"></a>Metodtips för Azure-driftsäkerhet
Den här artikeln innehåller en uppsättning metodtips för att skydda data, program och andra resurser i Azure.

De bästa metoderna baseras på konsensus och de fungerar med aktuella Azure-plattformsfunktioner och funktionsuppsättningar. Åsikter och teknik förändras med tiden och denna artikel uppdateras regelbundet för att återspegla dessa förändringar.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Definiera och distribuera starka operativa säkerhetsrutiner
Azure-driftsäkerhet refererar till tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda deras data, program och andra resurser i Azure. Azure-driftsäkerhet bygger på ett ramverk som innehåller den kunskap som erhållits genom funktioner som är unika för Microsoft, inklusive [SDL (Security Development Lifecycle),](https://www.microsoft.com/sdl) [Microsoft Security Response Center-programmet](https://www.microsoft.com/msrc?rtc=1) och djup medvetenhet om cybersäkerhetshotlandskapet.

## <a name="manage-and-monitor-user-passwords"></a>Hantera och övervaka användarlösenord
I följande tabell visas några metodtips som är relaterade till hantering av användarlösenord:

**Bästa praxis:** Se till att du har rätt nivå av lösenordsskydd i molnet.   
**Information**: Följ anvisningarna i [Microsoft Password Guidance](https://www.microsoft.com/research/publication/password-guidance/), som är begränsad till användare av Microsofts identitetsplattformar (Azure Active Directory, Active Directory och Microsoft-konto).

**Bästa praxis**: Övervaka misstänkta åtgärder som är relaterade till dina användarkonton.   
**Detalj:** Övervaka för [användare i riskzonen](/azure/active-directory/reports-monitoring/concept-user-at-risk) och [riskfyllda inloggningar](../../active-directory/reports-monitoring/concept-risk-events.md) med hjälp av Azure AD-säkerhetsrapporter.

**Bästa praxis:** Identifiera och åtgärda högrisklösenord automatiskt.   
**Detalj:** [Azure AD Identity Protection](/azure/active-directory/identity-protection/overview) är en funktion i Azure AD Premium P2-utgåvan som gör att du kan:

- Identifiera potentiella sårbarheter som påverkar organisationens identiteter
- Konfigurera automatiska svar till identifierade misstänkta åtgärder som är relaterade till organisationens identiteter
- Undersöka misstänkta incidenter och vidta lämpliga åtgärder för att lösa dem

## <a name="receive-incident-notifications-from-microsoft"></a>Ta emot incidentmeddelanden från Microsoft
Se till att säkerhetsoperationsteamet får Azure-incidentmeddelanden från Microsoft. Ett incidentmeddelande låter ditt säkerhetsteam veta att du har komprometterade Azure-resurser så att de snabbt kan svara på och åtgärda potentiella säkerhetsrisker.

I Azure-registreringsportalen kan du se till att administratörskontaktinformation innehåller information som meddelar säkerhetsåtgärder. Kontaktuppgifter är en e-postadress och telefonnummer.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Ordna Azure-prenumerationer i hanteringsgrupper
Om din organisation har många prenumerationer kanske du behöver ett sätt att effektivt hantera åtkomst, principer och efterlevnad för dessa prenumerationer. [Azure-hanteringsgrupper](/azure/governance/management-groups/create) ger en nivå av omfattning som är över prenumerationer. Du organiserar prenumerationer i behållare som kallas hanteringsgrupper och tillämpar dina styrningsvillkor på hanteringsgrupperna. Alla prenumerationer i en hanteringsgrupp ärver automatiskt de villkor som tillämpas för hanteringsgruppen.

Du kan skapa en flexibel struktur för hanteringsgrupper och prenumerationer i en katalog. Varje katalog får en enda hanteringsgrupp på den översta nivån som kallas rothanteringsgruppen. Rothanteringsgruppen är inbyggd i hierarkin så att alla hanteringsgrupper och prenumerationer är dess underordnade element. Rothanteringsgruppen gör att globala principer och RBAC-tilldelningar kan tillämpas på katalognivå.

Här är några metodtips för hur du använder hanteringsgrupper:

**Bästa praxis:** Se till att nya prenumerationer tillämpar styrningselement som principer och behörigheter när de läggs till.   
**Detalj**: Använd rothanteringsgruppen för att tilldela företagsomfattande säkerhetselement som gäller för alla Azure-resurser. Principer och behörigheter är exempel på element.

**Bästa praxis**: Justera de översta nivåerna i hanteringsgrupper med segmenteringsstrategi för att ge en punkt för kontroll och konsekvens i policyn inom varje segment.   
**Detalj**: Skapa en enda hanteringsgrupp för varje segment under rothanteringsgruppen. Skapa inga andra hanteringsgrupper under roten.

**Bästa praxis**: Begränsa hanteringsgruppens djup för att undvika förvirring som hämmar både drift och säkerhet.   
**Detalj:** Begränsa hierarkin till tre nivåer, inklusive roten.

**Bästa praxis**: Välj noggrant vilka objekt som ska tillämpas på hela företaget med rothanteringsgruppen.   
**Detalj:** Se till att rothanteringsgruppelement har ett tydligt behov av att tillämpas för varje resurs och att de har låg påverkan.

Bra kandidater inkluderar:

- Lagstadgade krav som har en tydlig inverkan på verksamheten (t.ex. begränsningar relaterade till datasuveränitet)
- Krav med nära-noll potentiell negativ påverkan på operationer, till exempel princip med revisionseffekt eller RBAC-behörighetstilldelningar som har granskats noggrant

**Bästa praxis**: Planera och testa alla företagsomfattande ändringar i rothanteringsgruppen innan du tillämpar dem (princip, RBAC-modell och så vidare).   
**Detalj:** Ändringar i rothanteringsgruppen kan påverka alla resurser på Azure. De är ett kraftfullt sätt att säkerställa konsekvens i hela företaget, men fel eller felaktig användning kan påverka produktionsåtgärder negativt. Testa alla ändringar i rothanteringsgruppen i ett testlabb eller produktionspilot.

## <a name="streamline-environment-creation-with-blueprints"></a>Effektivisera skapandet av miljön med ritningar
[Azure Blueprints-tjänsten](/azure/governance/blueprints/overview) gör det möjligt för molnarkitekter och centrala it-grupper att definiera en repeterbar uppsättning Azure-resurser som implementerar och följer en organisations standarder, mönster och krav. Azure Blueprints gör det möjligt för utvecklingsteam att snabbt bygga och stå upp nya miljöer med en uppsättning inbyggda komponenter och förtroendet för att de skapar dessa miljöer inom organisationens efterlevnad.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Övervaka lagringstjänster för oväntade ändringar i beteende
Diagnostisera och felsöka problem i ett distribuerat program som finns i en molnmiljö kan vara mer komplext än i traditionella miljöer. Program kan distribueras i en PaaS- eller IaaS-infrastruktur, lokalt, på en mobil enhet eller i någon kombination av dessa miljöer. Programmets nätverkstrafik kan passera offentliga och privata nätverk och ditt program kan använda flera lagringstekniker.

Du bör kontinuerligt övervaka de lagringstjänster som ditt program använder för oväntade ändringar i beteendet (t.ex. långsammare svarstider). Använd loggning för att samla in mer detaljerade data och för att analysera ett problem på djupet. Diagnostikinformationen som du får från både övervakning och loggning hjälper dig att fastställa orsaken till problemet som programmet påträffades. Sedan kan du felsöka problemet och bestämma lämpliga steg för att åtgärda det.

[Azure Storage Analytics](../../storage/common/storage-analytics.md) utför loggning och tillhandahåller måttdata för ett Azure-lagringskonto. Vi rekommenderar att du använder dessa data för att spåra begäranden, analysera användningstrender och diagnostisera problem med ditt lagringskonto.

## <a name="prevent-detect-and-respond-to-threats"></a>Förebygga, upptäcka och reagera på hot
[Azure Security Center](../../security-center/security-center-intro.md) hjälper dig att förebygga, identifiera och svara på hot genom att ge ökad insyn i (och kontrollera över) säkerheten för dina Azure-resurser. Den tillhandahåller integrerad säkerhetsövervakning och principhantering i dina Azure-prenumerationer, hjälper till att identifiera hot som annars kan gå obemärkt förbi och fungerar med olika säkerhetslösningar.

Den kostnadsfria nivån för Security Center erbjuder begränsad säkerhet för endast dina Azure-resurser. Standardnivån utökar dessa funktioner till lokala och andra moln. Security Center Standard hjälper dig att hitta och åtgärda säkerhetsproblem, tillämpa åtkomst- och programkontroller för att blockera skadlig aktivitet, identifiera hot med hjälp av analyser och intelligens och svara snabbt när du attackerar. Du kan prova Security Center Standard utan kostnad under de första 60 dagarna. Vi rekommenderar att du [uppgraderar din Azure-prenumeration till Security Center Standard](../../security-center/security-center-get-started.md).

Använd Security Center för att få en central vy över säkerhetstillståndet för alla dina Azure-resurser. Kontrollera snabbt att lämpliga säkerhetskontroller är på plats och konfigureras korrekt och snabbt identifiera alla resurser som behöver åtgärdas.

Security Center integrerar också med [Microsoft Defender Advanced Threat Protection (ATP),](../../security-center/security-center-wdatp.md)som tillhandahåller omfattande edr-funktioner (Endpoint Detection and Response). Med Microsoft Defender ATP-integrering kan du upptäcka avvikelser. Du kan också identifiera och svara på avancerade attacker på serverslutpunkter som övervakas av Security Center.

Nästan alla företagsorganisationer har ett SIEM-system (Security Information and Event Management) för att identifiera nya hot genom att konsolidera logginformation från olika signalinsamlingsenheter. Loggarna analyseras sedan av ett dataanalyssystem för att identifiera vad som är "intressant" från bullret som är oundvikligt i alla logginsamlings- och analyslösningar.

[Azure Sentinel](/azure/sentinel/overview) är en skalbar, molnbaserad, säkerhetsinformation och händelsehantering (SIEM) och drivna lösningar för automatisk respons (SOAR) för säkerhetsorkestrering .Azure Sentinel is a scalable, cloud-native, security information and event management (SIEM) and security orchestration automated response (SOAR) solution. Azure Sentinel tillhandahåller intelligent säkerhetsanalys och hotinformation via varningsidentifiering, hotsynlighet, proaktiv jakt och automatiserad hothantering.

Här är några metodtips för att förebygga, upptäcka och svara på hot:

**Bästa praxis:** Öka hastigheten och skalbarheten för din SIEM-lösning med hjälp av ett molnbaserat SIEM.   
**Detalj:** Undersök funktionerna i [Azure Sentinel](/azure/sentinel/overview) och jämför dem med funktionerna i det du använder lokalt. Överväg att använda Azure Sentinel om det uppfyller organisationens SIEM-krav.

**Bästa praxis**: Hitta de allvarligaste säkerhetsproblemen så att du kan prioritera undersökning.   
**Detalj:** Granska din [Azure-säkra poäng](../../security-center/security-center-secure-score.md) för att se rekommendationerna från Azure-principer och initiativ som är inbyggda i Azure Security Center. Dessa rekommendationer hjälper till att hantera de största riskerna som säkerhetsuppdateringar, slutpunktsskydd, kryptering, säkerhetskonfigurationer, saknade WAF, internetanslutna virtuella datorer och många fler.

Med den säkra poängen, som baseras på CIS-kontroller (Center for Internet Security), kan du jämföra organisationens Azure-säkerhet mot externa källor. Extern validering hjälper till att validera och berika teamets säkerhetsstrategi.

**Bästa praxis:** Övervaka säkerhetshållningen för datorer, nätverk, lagring och datatjänster och program för att upptäcka och prioritera potentiella säkerhetsproblem.  
**Detalj**: Följ [säkerhetsrekommendationerna](../../security-center/security-center-recommendations.md) i Security Center från och med de högst prioriterade objekten.

**Bästa praxis:** Integrera Säkerhetscentervarningar i din siem-lösning (Security Information and Event Management).   
**Detalj**: De flesta organisationer med en SIEM använder den som ett centralt clearinghouse för säkerhetsvarningar som kräver ett analytikersvar. Bearbetade händelser som produceras av Security Center publiceras i Azure Activity Log, en av loggarna som är tillgängliga via Azure Monitor. Azure Monitor erbjuder en konsoliderad pipeline för routning av någon av dina övervakningsdata till ett SIEM-verktyg. Instruktioner [finns i Integrera säkerhetslösningar i Security Center.](../../security-center/security-center-partner-integration.md#exporting-data-to-a-siem) Om du använder Azure Sentinel läser du [Anslut Azure Security Center](../../sentinel/connect-azure-security-center.md).

**Bästa praxis:** Integrera Azure-loggar med din SIEM.   
**Detalj:** Använd [Azure Monitor för att samla in och exportera data](/azure/azure-monitor/overview#integrate-and-export-data). Den här metoden är avgörande för att aktivera utredning av säkerhetsincidenter och lagring av onlineloggar är begränsad. Om du använder Azure Sentinel läser du [Ansluta datakällor](../../sentinel/connect-data-sources.md).

**Bästa praxis:** Snabba upp dina undersöknings- och jaktprocesser och minska falska positiva genom att integrera edr-funktioner (Endpoint Detection and Response) i din attackundersökning.   
**Detalj:** [Aktivera Microsoft Defender ATP-integrering](../../security-center/security-center-wdatp.md#enable-microsoft-defender-atp-integration) via säkerhetscentrets säkerhetsprincip. Överväg att använda Azure Sentinel för hotjakt och incidenthantering.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Övervaka scenariobaserad nätverksövervakning från på ett på sluten tid
Kunder skapar ett heltäckande nätverk i Azure genom att kombinera nätverksresurser som ett virtuellt nätverk, ExpressRoute, Application Gateway och belastningsutjämnare. Övervakning är tillgänglig på var och en av nätverksresurserna.

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) är en regional tjänst. Använd dess diagnostik- och visualiseringsverktyg för att övervaka och diagnostisera tillstånd på nätverksscenarionivå i, till och från Azure.

Följande är metodtips för nätverksövervakning och tillgängliga verktyg.

**Bästa praxis:** Automatisera fjärrnätverksövervakning med paketinsamling.  
**Detalj:** Övervaka och diagnostisera nätverksproblem utan att logga in på dina virtuella datorer med hjälp av Network Watcher. Utlösa [paketinsamling](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) genom att ställa in aviseringar och få tillgång till prestandainformation i realtid på paketnivå. När du ser ett problem kan du undersöka i detalj för bättre diagnoser.

**Bästa praxis:** Få insikt i nätverkstrafiken med hjälp av flödesloggar.  
**Detalj:** Skapa en djupare förståelse för nätverkstrafikmönster med hjälp av [flödesloggar för nätverkssäkerhetsgrupper](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). Information i flödesloggar hjälper dig att samla in data för efterlevnad, granskning och övervakning av nätverkssäkerhetsprofilen.

**Bästa praxis:** Diagnostisera problem med VPN-anslutning.  
**Detalj:** Använd Network Watcher för att [diagnostisera dina vanligaste VPN Gateway och anslutningsproblem](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Du kan inte bara identifiera problemet utan också använda detaljerade loggar för att undersöka ytterligare.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Säker distribution med hjälp av beprövade DevOps-verktyg
Använd följande bästa metoder för DevOps för att säkerställa att ditt företag och dina team är produktiva och effektiva.

**Bästa praxis**: Automatisera uppbyggnad och distribution av tjänster.  
**Detalj:** [Infrastruktur som kod](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) är en uppsättning tekniker och metoder som hjälper IT-proffs att ta bort bördan av den dagliga uppbyggnaden och hanteringen av modulär infrastruktur. Det gör det möjligt för IT-proffs att bygga och underhålla sin moderna servermiljö på ett sätt som är som hur programutvecklare bygger och underhåller programkod.

Du kan använda [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) för att etablera dina program med hjälp av en deklarativ mall. I samma mall kan du distribuera flera tjänster tillsammans med deras beroenden. Du använder samma mall för att upprepade gånger distribuera ditt program i varje steg i programmets livscykel.

**Bästa praxis:** Skapa och distribuera automatiskt till Azure-webbappar eller molntjänster.  
**Detalj:** Du kan konfigurera dina Azure DevOps-projekt för att [automatiskt skapa och distribuera](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) till Azure-webbappar eller molntjänster. Azure DevOps distribuerar automatiskt binärfilerna efter att ha gjort en version till Azure efter varje kod incheckning. Paketbyggprocessen motsvarar kommandot Paket i Visual Studio och publiceringsstegen motsvarar kommandot Publicera i Visual Studio.

**Bästa praxis**: Automatisera versionshantering.  
**Detalj:** [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) är en lösning för att automatisera distribution i flera steg och hantera utgivningsprocessen. Skapa hanterade pipelines för kontinuerlig distribution för att släppa snabbt, enkelt och ofta. Med Azure Pipelines kan du automatisera din utgivningsprocess och du kan ha fördefinierade godkännandearbetsflöden. Distribuera lokalt och till molnet, utöka och anpassa efter behov.

**Bästa praxis**: Kontrollera appens prestanda innan du startar den eller distribuera uppdateringar till produktionen.  
**Detalj**: Kör molnbaserade [belastningstester](/azure/devops/test/load-test/overview#alternatives) till:

- Hitta prestandaproblem i appen.
- Förbättra driftsättningskvaliteten.
- Se till att appen alltid är tillgänglig.
- Se till att appen kan hantera trafik för nästa lanserings- eller marknadsföringskampanj.

[Apache JMeter](https://jmeter.apache.org/) är ett gratis, populärt open source-verktyg med en stark gemenskap stöd.

**Bästa praxis**: Övervaka programmets prestanda.  
**Detalj:** [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) är en utökningsbar APM-tjänst (Application Performance Management) för webbutvecklare på flera plattformar. Använd Application Insights för att övervaka ditt live-webbprogram. Den identifierar automatiskt prestandaavvikelser. Den innehåller analysverktyg som hjälper dig att diagnostisera problem och förstå vad användarna faktiskt gör med din app. Den är avsedd för utvecklare och för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet.

## <a name="mitigate-and-protect-against-ddos"></a>Minska och skydda mot DDoS
DDoS (Distributed Denial of Service) är en typ av attack som försöker ta bort programresurser. Målet är att påverka programmets tillgänglighet och dess förmåga att hantera legitima förfrågningar. Dessa attacker blir mer sofistikerade och större i storlek och effekt. De kan riktas till alla slutpunkter som är offentligt nås via Internet.

Designa och bygga för DDoS återhämtning kräver planering och design för en mängd olika fellägen. Följande är metodtips för att skapa DDoS-tåliga tjänster på Azure.

**Bästa praxis:** Se till att säkerhet är en prioritet under hela livscykeln för ett program, från design och implementering till distribution och åtgärder. Program kan ha buggar som tillåter en relativt låg mängd begäranden att använda många resurser, vilket resulterar i ett avbrott i tjänsten.  
**Detalj:** För att skydda en tjänst som körs på Microsoft Azure bör du ha en god förståelse för din programarkitektur och fokusera på de [fem pelarna i programvarukvaliteten](https://docs.microsoft.com/azure/architecture/guide/pillars). Du bör känna till vanliga trafikvolymer, anslutningsmodellen mellan programmet och andra program och tjänstslutpunkterna som exponeras för det offentliga internet.

Att se till att ett program är tillräckligt flexibelt för att hantera en överbelastning som är inriktad på själva programmet är viktigast. Säkerhet och sekretess är inbyggda i Azure-plattformen, som börjar med [Livscykeln för säkerhetsutveckling (SDL).](https://www.microsoft.com/sdl) SDL åtgärdar säkerhet i varje utvecklingsfas och säkerställer att Azure uppdateras kontinuerligt för att göra det ännu säkrare.

**Bästa praxis:** Utforma dina program så att [de kan skalas horisontellt](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) för att möta efterfrågan på en förstärkt belastning, särskilt i händelse av en DDoS-attack. Om ditt program är beroende av en enda instans av en tjänst skapas en enskild felpunkt. Etablering av flera instanser gör ditt system mer flexibelt och skalbart.  
**Detalj:** För [Azure App Service](/azure/app-service/app-service-value-prop-what-is)väljer du en App [Service-plan](../../app-service/overview-hosting-plans.md) som erbjuder flera instanser.

Konfigurera var och en av dina roller för Azure Cloud Services så att de använder [flera instanser](../../cloud-services/cloud-services-choose-me.md).

För [Virtuella Azure-datorer](/azure/virtual-machines/windows/overview)kontrollerar du att din vm-arkitektur innehåller mer än en virtuell dator och att varje virtuell dator ingår i en [tillgänglighetsuppsättning](/azure/virtual-machines/virtual-machines-windows-manage-availability). Vi rekommenderar att du använder skaluppsättningar för virtuella datorer för automatisk skalning.

**Bästa praxis:** Skiktning säkerhetsförsvar i ett program minskar risken för en lyckad attack. Implementera säker design för dina program med hjälp av de inbyggda funktionerna i Azure-plattformen.  
**Detalj**: Risken för angrepp ökar med storleken (ytan) av ansökan. Du kan minska ytan genom att använda vitlistning för att stänga av det exponerade IP-adressutrymmet och lyssningsportarna som inte behövs på belastningsutjämnare ([Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) och [Azure Application Gateway](/azure/application-gateway/application-gateway-create-probe-portal)).

[Nätverkssäkerhetsgrupper](../../virtual-network/security-overview.md) är ett annat sätt att minska angreppsytan. Du kan använda [tjänsttaggar](../../virtual-network/security-overview.md#service-tags) och [programsäkerhetsgrupper](../../virtual-network/security-overview.md#application-security-groups) för att minimera komplexiteten för att skapa säkerhetsregler och konfigurera nätverkssäkerhet, som en naturlig förlängning av ett programs struktur.

Du bör distribuera Azure-tjänster i ett [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md) när det är möjligt. Med den här metoden kan tjänstresurser kommunicera via privata IP-adresser. Azure-tjänsttrafik från ett virtuellt nätverk använder offentliga IP-adresser som käll-IP-adresser som standard.

Om du använder [tjänstslutpunkter](../../virtual-network/virtual-network-service-endpoints-overview.md) växlar tjänsttrafik för att använda privata virtuella nätverksadresser som käll-IP-adresser när de använder Azure-tjänsten från ett virtuellt nätverk.

Vi ser ofta kunders lokala resurser attackeras tillsammans med sina resurser i Azure. Om du ansluter en lokal miljö till Azure minimerar du exponeringen av lokala resurser till det offentliga internet.

Azure har två [DDoS-tjänsterbjudanden](../../virtual-network/ddos-protection-overview.md) som ger skydd mot nätverksattacker:

- Grundläggande skydd är integrerat i Azure som standard utan extra kostnad. Omfattningen och kapaciteten för det globalt distribuerade Azure-nätverket ger skydd mot vanliga nätverkslagerattacker genom trafikövervakning alltid och begränsning i realtid. Basic kräver ingen användarkonfiguration eller programändringar och hjälper till att skydda alla Azure-tjänster, inklusive PaaS-tjänster som Azure DNS.
- Standardskydd ger avancerade DDoS-begränsningsfunktioner mot nätverksattacker. Den är automatiskt inställd för att skydda dina specifika Azure-resurser. Skydd är enkelt att aktivera när virtuella nätverk skapas. Det kan också göras när du har skapat och kräver inga program- eller resursändringar.

## <a name="enable-azure-policy"></a>Aktivera Azure-princip
[Azure Policy](/azure/governance/policy/overview) är en tjänst i Azure som du använder för att skapa, tilldela och hantera principer. Dessa principer tillämpar regler och effekter över dina resurser, så att dessa resurser förblir kompatibla med företagets standarder och servicenivåavtal. Azure Policy uppfyller detta behov genom att utvärdera dina resurser för icke-kompatibilitet med tilldelade principer.

Aktivera Azure-princip för att övervaka och framtvinga organisationens skrivna princip. Detta säkerställer efterlevnad av ditt företag eller lagstadgade säkerhetskrav genom att centralt hantera säkerhetsprinciper över dina hybridmolnarbetsbelastningar. Lär dig hur du [skapar och hanterar principer för att genomdriva efterlevnad](../../governance/policy/tutorials/create-and-manage.md). Se [Definitionsstrukturen](../../governance/policy/concepts/definition-structure.md) för Azure Princip för en översikt över elementen i en princip.

Här är några metodtips för säkerhet att följa när du har antagit Azure Policy:

**Bästa praxis**: Principen stöder flera typer av effekter. Du kan läsa om dem i [Definitionsstrukturen](../../governance/policy/concepts/definition-structure.md#policy-rule)för Azure Princip . Affärsverksamheten kan påverkas negativt av **neka effekten** och **den åtgärdande** effekten, så börja med **revisionseffekten** för att begränsa risken för negativ påverkan från principen.   
**Detalj**: [Starta principdistributioner i granskningsläge](../../governance/policy/concepts/definition-structure.md#policy-rule) och sedan senare framsteg för att **neka** eller **åtgärda**. Testa och granska resultatet av granskningseffekten innan du går för att **neka** eller **åtgärda**.

Mer information finns i [Skapa och hantera principer för att tillämpa efterlevnad](../../governance/policy/tutorials/create-and-manage.md).

**Bästa praxis**: Identifiera de roller som ansvarar för övervakningen av policyöverträdelser och se till att rätt saneringsåtgärder vidtas snabbt.   
**Detalj:** Har den tilldelade rollen övervaka efterlevnad via [Azure-portalen](../../governance/policy/how-to/get-compliance-data.md#portal) eller via [kommandoraden](../../governance/policy/how-to/get-compliance-data.md#command-line).

**Bästa praxis**: Azure Policy är en teknisk representation av en organisations skrivna principer. Mappa alla Azure-principer till organisationsprinciper för att minska förvirring och öka konsekvensen.   
**Detalj**: Dokumentmappning i organisationens dokumentation eller i Azure-principen själv genom att lägga till en referens till organisationsprincipen i [Azure-principbeskrivningen](../../governance/policy/concepts/definition-structure.md#display-name-and-description) eller Azure-principinitiativbeskrivningen. [initiative](../../governance/policy/concepts/definition-structure.md#initiatives)

## <a name="monitor-azure-ad-risk-reports"></a>Övervaka Azure AD-riskrapporter
De allra flesta säkerhetsöverträdelser äger rum när angripare får tillgång till en miljö genom att stjäla en användares identitet. Att upptäcka komprometterade identiteter är ingen lätt uppgift. Azure AD använder adaptiva maskininlärningsalgoritmer och heuristik för att identifiera misstänkta åtgärder som är relaterade till dina användarkonton. Varje identifierad misstänkt åtgärd lagras i en post som kallas [en riskidentifiering](../../active-directory/reports-monitoring/concept-risk-events.md). Riskidentifieringar registreras i Azure AD-säkerhetsrapporter. Mer information finns i [säkerhetsrapporten för användare med risk](../../active-directory/reports-monitoring/concept-user-at-risk.md) och [säkerhetsrapporten för riskfyllda inloggningar](../../active-directory/reports-monitoring/concept-risky-sign-ins.md).

## <a name="next-steps"></a>Nästa steg
Se metodtips och mönster för [Azure-säkerhet](best-practices-and-patterns.md) för fler metodtips för säkerhet som ska användas när du utformar, distribuerar och hanterar dina molnlösningar med hjälp av Azure.

Följande resurser är tillgängliga för att ge mer allmän information om Azure-säkerhet och relaterade Microsoft-tjänster:
* [Blogg för Azure Security Team](https://blogs.msdn.microsoft.com/azuresecurity/) – för aktuell information om det senaste inom Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – där Microsofts säkerhetsproblem, inklusive problem med Azure, kan rapporteras eller via e-post tillsecure@microsoft.com
