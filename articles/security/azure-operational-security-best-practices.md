---
title: Rekommenderade säkerhetsmetoder för att skydda dina tillgångar – Microsoft Azure
description: Den här artikeln innehåller en uppsättning Metodtips för fortlöpande för att skydda dina data, program och andra resurser i Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 4a4677b5db730001df75d201d8e6d3149cb928e6
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409852"
---
# <a name="azure-operational-security-best-practices"></a>Metodtips för Azure driftsäkerhet
Den här artikeln innehåller en uppsättning Metodtips för fortlöpande för att skydda dina data, program och andra resurser i Azure.

De bästa metoderna är baserade på en enhälligt av åsikter och de fungerar med den aktuella Azure-plattformsfunktioner och egenskapsuppsättningar. Andras åsikter och tekniker som ändras med tiden och den här artikeln uppdateras regelbundet att återspegla dessa ändringar.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Definiera och distribuera stark driftsäkerhet metoder
Azure driftsäkerhet refererar till de tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda sina data, program och andra resurser i Azure. Azure driftsäkerhet bygger på ett ramverk som innehåller den kunskap som funktioner som är unika för Microsoft, inklusive den [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl), [Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) programmet och djup medvetenhet om hotlandskapet.

## <a name="manage-and-monitor-user-passwords"></a>Hantera och övervaka användarlösenord
I följande tabell visas några metodtips som rör hantering av användarlösenord:

**Bästa praxis**: Kontrollera att du har rätt nivå av lösenord i molnet.   
**Information om**: Följ riktlinjerna i [Microsofts lösenord vägledning](https://www.microsoft.com/research/publication/password-guidance/), som är begränsad till användare av Microsoft identity-plattformar (Azure Active Directory, Active Directory och Microsoft-konto).

**Bästa praxis**: Övervaka misstänkta åtgärder relaterade till dina användarkonton.   
**Information om**: Övervaka [användare i farozonen](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk) och [riskfyllda inloggningar](../active-directory/reports-monitoring/concept-risk-events.md) med hjälp av Azure AD-säkerhetsgrupper rapporter.

**Bästa praxis**: Automatiskt identifiera och åtgärda med hög risk lösenord.   
**Information om**: [Azure AD Identity Protection](../active-directory/identity-protection/overview.md) är en funktion i Azure AD Premium P2-versionen som hjälper dig att:

- Identifiera potentiella sårbarheter som påverkar organisationens identiteter
- Konfigurera automatiska svar till identifierade misstänkta åtgärder som är relaterade till din organisations identiteter
- Undersöka misstänkta incidenter och vidta lämpliga åtgärder för att lösa dem.

## <a name="receive-incident-notifications-from-microsoft"></a>Ta emot incident meddelanden från Microsoft
Var noga med ditt säkerhetsteam för åtgärder tar emot Azure incident meddelanden från Microsoft. En incident meddelande kan ditt säkerhetsteam vet du har komprometterats Azure-resurser så att de snabbt kan svara på och åtgärda potentiella säkerhetsrisker.

I portalen Azure-registrering kan du se till administratören kontaktinformation innehåller information som meddelar säkerhetsåtgärder. Kontaktinformation är ett e-postadress och telefonnummer tal.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Organisera Azure-prenumerationer till hanteringsgrupper
Om din organisation har många prenumerationer, kanske du behöver ett sätt att effektivt hantera åtkomst, principer och efterlevnad för dessa prenumerationer. [Azure-hanteringsgrupper](../governance/management-groups/create.md) ger en omfattning som är över prenumerationer. Du organiserar prenumerationer i behållare som kallas hanteringsgrupper och tillämpa dina styrning villkor för hanteringsgrupperna. Alla prenumerationer i en hanteringsgrupp ärver automatiskt de villkor som tillämpas för hanteringsgruppen.

Du kan skapa en flexibel struktur för hanteringsgrupper och prenumerationer i en katalog. Varje katalog får en enskild översta hanteringsgrupp som kallas rot-hanteringsgruppen. Rothanteringsgruppen är inbyggd i hierarkin så att alla hanteringsgrupper och prenumerationer är dess underordnade element. Rot-hanteringsgruppen kan globala principer och RBAC-uppgifter som ska tillämpas på nivån för katalogen.

Här följer några rekommendationer för att använda hanteringsgrupper:

**Bästa praxis**: Se till att tillämpa nya prenumerationer styrning element som principer och behörigheter som de har lagts till.   
**Information om**: Använd rot-hanteringsgruppen för att tilldela företagsomfattande security-element som gäller för alla Azure-tillgångar. Principer och behörigheter är exempel på element.

**Bästa praxis**: Justera de högsta nivåerna i hanteringsgrupper segmentering strategi att tillhandahålla en tidpunkt för kontroll och principen konsekvens inom varje segment.   
**Information om**: Skapa en enskild hanteringsgrupp för varje segment under rot-hanteringsgruppen. Skapa inte några andra hanteringsgrupper under roten.

**Bästa praxis**: Begränsa management group djupet för att undvika förvirring som hindrar både åtgärder och säkerhet.   
**Information om**: Begränsa din hierarki till tre nivåer, inklusive rot.

**Bästa praxis**: Välj noggrant vilka objekt som gäller för hela företaget med hanteringsgruppen rot.   
**Information om**: Kontrollera att rotelement management grupp har en clear måste tillämpas på alla resurser och att de är låg inverkan.

Bra kandidater är:

- Regelkrav som har en tydlig inverkan på (till exempel begränsningar relaterade till datasuveränitet)
- Krav med nästan obefintlig potential negativt påverka på åtgärder, t.ex. principen med spårningsseffekt eller RBAC behörigheter som har granskats noggrant

**Bästa praxis**: Noggrant planera och testa alla företagsomfattande ändringar på rot-hanteringsgruppen innan du tillämpar dem (princip, RBAC-modellen och så vidare).   
**Information om**: Ändringar i hanteringsgruppen rot kan påverka alla resurser i Azure. De tillhandahåller ett kraftfullt sätt att garantera konsekvens i hela företaget, kan fel eller felaktig användning påverka produktion. Testa alla ändringar i hanteringsgruppen rot i ett testlabb eller produktion pilot.

## <a name="streamline-environment-creation-with-blueprints"></a>Effektivisera att skapa en miljö med skisser
[Azure-skisser](../governance/blueprints/overview.md) tjänsten kan molnarkitekter och central information technology grupper att definiera en upprepningsbara uppsättning Azure-resurser som implementerar och tillämpar en organisations standarder, mönster och krav. Azure skisser gör det möjligt för utvecklingsteam att snabbt bygga och skapa nya miljöer med en uppsättning inbyggda komponenter och var säker på att de skapar dessa miljöer inom organisatorisk efterlevnad.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Övervaka lagringstjänster för oväntade ändringar i beteende
Diagnostisera och felsöka problem i ett distribuerat program i en molnmiljö kan vara mer komplex än i traditionella miljöer. Program kan distribueras i en PaaS eller IaaS-infrastruktur lokalt, på en mobil enhet eller i en kombination av dessa miljöer. Ditt programs trafik kan bläddra i offentliga och privata nätverk och ditt program kan använda flera lagringstekniker.

Kontinuerligt bör du övervaka de storage-tjänster som programmet använder för eventuella oväntade ändringar i beteende (till exempel längre svarstider). Använd loggning för att samla in mer detaljerade data och analysera ett problem i detalj. Diagnostikinformation som hämtas från övervakning och loggning hjälper dig att fastställa orsaken till problemet som påträffats i ditt program. Sedan kan du felsöka problemet och fastställa lämpliga åtgärder kan åtgärdas.

[Azure Storage Analytics](../storage/storage-analytics.md) utför loggning och tillhandahåller mätvärden för ett Azure storage-konto. Vi rekommenderar att du använder dessa data för att spåra förfrågningar, analysera användningstrender och diagnostisera problem med ditt lagringskonto.

## <a name="prevent-detect-and-respond-to-threats"></a>Förhindra, upptäcka och svara på hot
[Azure Security Center](../security-center/security-center-intro.md) hjälper dig att förhindra, upptäcka och svara på hot med ökad insyn i (och kontroll över) säkerheten för dina Azure-resurser. Det ger integrerad säkerhet övervaka och hantera principer för alla Azure-prenumerationer, upptäcka hot som kan annars oupptäckta och fungerar med olika säkerhetslösningar.

Den kostnadsfria nivån av Security Center erbjuder begränsad säkerhet för endast dina Azure-resurser. Standard-nivån utökar funktionerna till lokala och andra moln. Security Center Standard kan du hitta och åtgärda säkerhetsproblem, tillämpa åtkomst- och kontroller för att blockera skadlig aktivitet, upptäcka hot med analys och intelligens och svara snabbt under attacker. Du kan prova Security Center Standard utan kostnad under de första 60 dagarna. Vi rekommenderar att du [uppgradera din Azure-prenumeration till Security Center Standard](../security-center/security-center-get-started.md).

Du kan använda Security Center för att få en central vy över säkerhetsläget hos alla dina Azure-resurser. Kontrollera att rätt säkerhetskontroller är på plats och korrekt konfigurerad och snabbt identifiera eventuella resurser som behöver åtgärdas på ett ögonblick.

Security Center är integrerat med [Windows Defender Advanced Threat Protection (ATP)](../security-center/security-center-wdatp.md), som tillhandahåller omfattande funktioner för slutpunktsidentifiering och svar (EDR). Med Windows Defender ATP-integrering kan du upptäcka avvikelser. Du kan också upptäcka och svara på avancerade attacker på serverslutpunkter som övervakas av Security Center.

Nästan alla företag har en säkerhet och händelsehantering (SIEM) hanteringssystem för att identifiera nya hot genom att konsolidera logginformation från olika signalen samlar in enheter. Loggarna analyseras sedan genom att ett system för analys av data för att identifiera vad är ”intressant” från bruset ofrånkomligt i alla log samla in och Analyslösningar.

[Azure Sentinel](../sentinel/overview.md) är en skalbar, molnbaserade, säkerhet och händelsehantering hantering (SIEM) och säkerhetslösning orchestration automatiska svar (SVÄVA). Azure Sentinel ger intelligent security analyser och hotinformation för via avisering identifiering, threat synlighet, proaktiv jakt och svar på automatiserad hot.

Här följer några rekommendationer för att förhindra, upptäcka och svara på hot:

**Bästa praxis**: Öka hastigheten och skalbarhet för din SIEM-lösning med hjälp av en molnbaserad SIEM.   
**Information om**: Undersök funktionerna och egenskaperna för [Azure Sentinel](../sentinel/overview.md) och jämför dem med funktionerna i vad du är för närvarande med hjälp av lokal. Överväg att använda Azure Sentinel om den uppfyller kraven för din organisation SIEM.

**Bästa praxis**: Hitta de mest allvarliga säkerhetsriskerna så att du kan prioritera undersökningen.   
**Information om**: Granska dina [Azure säker poäng](../security-center/security-center-secure-score.md) att se rekommendationerna från Azure-principer och initiativ som är inbyggda i Azure Security Center. Följande rekommendationer hjälper adress översta riskerna som säkerhetsuppdateringar, endpoint protection, kryptering, säkerhetskonfigurationer, saknas WAF, internet-anslutna virtuella datorer och många fler.

Säker poäng som baseras på Center för Internet Security (CIS) kontroller, kan du jämföra organisationens Azure-säkerhet mot externa källor. Externa verifiering kan du verifiera och berika din grupps säkerhetsstrategi.

**Bästa praxis**: Övervaka säkerhetstillståndet för datorer, nätverk, lagring och tjänster och program för att identifiera och prioritera potentiella säkerhetsproblem.  
**Information om**: Följ den [säkerhetsrekommendationer](../security-center/security-center-recommendations.md) i Security Center börjar med högsta prioritet-objekt.

**Bästa praxis**: Integrera Security Center-aviseringar i din säkerhet och händelsehantering (SIEM) lösning.   
**Information om**: De flesta organisationer med en SIEM använda den som en central clearinghouse för säkerhetsaviseringar som kräver ett svar för analytiker. Bearbetade händelser som genereras av Security Center publiceras till Azure-aktivitetsloggen, någon av loggarna som är tillgängliga i Azure Monitor. Azure Monitor erbjuder en konsoliderad pipeline för routning någon av dina övervakade data i ett SIEM-verktyg. Se [integrera säkerhetslösningar i Security Center](../security-center/security-center-partner-integration.md#exporting-data-to-a-siem) anvisningar. Om du använder Azure Sentinel, se [Anslut Azure Security Center](../sentinel/connect-azure-security-center.md).

**Bästa praxis**: Integrera Azure loggar med din SIEM.   
**Information om**: Använd [Azure Monitor för att samla in och exportera data](../azure-monitor/overview.md#integrate-and-export-data). Det här är viktiga för att aktivera incident säkerhetsundersökning och online loggbevarande är begränsat. Om du använder Azure Sentinel, se [ansluta datakällor](../sentinel/connect-data-sources.md).

**Bästa praxis**: Snabba upp din undersökning och jakt processer och minska falska positiva identifieringar genom att integrera slutpunktsidentifiering och svar (EDR) funktioner i din attack undersökning.   
**Information om**: [Aktivera Windows Defender ATP-integrering](../security-center/security-center-wdatp.md#enable-windows-defender-atp-integration) via din säkerhetsprincip i Security Center. Överväg att använda Azure Sentinel för threat jakt och incidenter.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Övervaka slutpunkt till slutpunkt scenariobaserade nätverksövervakning
Kunder skapar en slutpunkt till slutpunkt-nätverk i Azure genom att kombinera nätverksresurser som ett virtuellt nätverk, ExpressRoute, Application Gateway och belastningsutjämnare. Övervakning är tillgängliga på var och en av nätverksresurserna.

[Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) är en regional tjänst. Använd dess verktyg för diagnostisering och visualisering för att övervaka och diagnostisera villkor på nätverksscenarienivå i, till och från Azure.

Här följer bästa praxis för verktyg för övervakning och tillgänglig.

**Bästa praxis**: Automatisera övervakning av fjärrnätverk med infångade paket.  
**Information om**: Övervaka och diagnostisera nätverksproblem utan att logga in till dina virtuella datorer med hjälp av Network Watcher. Utlösaren [paketfångsten](../network-watcher/network-watcher-alert-triggered-packet-capture.md) genom att konfigurera aviseringar och få åtkomst till prestandainformation i realtid på paketnivå. När du ser ett problem kan du undersöka i detalj för att få bättre diagnoser.

**Bästa praxis**: Få insikt i din nätverkstrafik med flödesloggar.  
**Information om**: Skapa trafikmönster för en djupare förståelse för nätverket med hjälp av [network flödesloggar för nätverkssäkerhetsgruppen](../network-watcher/network-watcher-nsg-flow-logging-overview.md). Informationen i flödesloggar kan du samla in data för efterlevnad, granskning och övervakning av din nätverkssäkerhetsprofil.

**Bästa praxis**: Diagnostisera VPN-anslutningsproblem.  
**Information om**: Använda Network Watcher till [diagnostisera dina vanligaste problem för VPN-Gateway och anslutning](../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Du kan inte bara identifiera problemet, men också använda detaljerade loggar för att undersöka.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Skydda distributionen genom att använda beprövade DevOps-verktyg
Använd följande säkerhetsmetoder för DevOps för att säkerställa att ditt företag och team är produktiva och effektiv.

**Bästa praxis**: Automatisera skapande och distribution av tjänster.  
**Information om**: [Infrastruktur som kod](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) är en uppsättning tekniker och metoder som hjälper IT-proffs kan du ta bort arbetet med dagliga build och hantering av modulära infrastrukturen. Det gör att IT-proffs att skapa och underhålla sina moderna server-miljö på ett sätt som liknar hur programvaruutvecklare att bygga och underhålla programkod.

Du kan använda [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) etablera dina program med hjälp av en deklarativ mall. I samma mall kan du distribuera flera tjänster tillsammans med deras beroenden. Du kan använda samma mall för att upprepade gånger distribuera ditt program i varje fas av programmets livscykel.

**Bästa praxis**: Automatiskt skapa och distribuera till Azure-webbappar eller molntjänster.  
**Information om**: Du kan konfigurera Azure DevOps-projekt till [automatiskt skapa och distribuera](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) till Azure-webbappar eller molntjänster. Azure DevOps distribuerar automatiskt binärfilerna när du har gjort en version till Azure efter varje checkar in ny kod. Paketet skapandeprocessen motsvarar kommandot paketet i Visual Studio och publicera stegen är likvärdiga med kommandot Publicera i Visual Studio.

**Bästa praxis**: Automatisera versionshantering.  
**Information om**: [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) är en lösning för att automatisera distribution av flera steg och hantera lanseringsprocessen. Skapa hanterade kontinuerlig distribution pipelines Frigör snabbt, enkelt och ofta. Med Azure Pipelines, kan du automatisera din lanseringsprocessen och du kan ha fördefinierade godkännandearbetsflöden. Distribuera lokalt till molnet, utöka och anpassa efter behov.

**Bästa praxis**: Kontrollera din Apps prestanda innan du startar den eller distribuera uppdateringar till produktion.  
**Information om**: Kör molnbaserade [belastningstester](https://docs.microsoft.com/azure/devops/test/load-test/overview.md?view=azure-devops#alternatives) till:

- Hitta prestandaproblem i din app.
- Förbättra kvaliteten i distributionen.
- Se till att dina appar alltid är tillgängliga.
- Se till att din app kan hantera trafik för nästa start eller marknadsföring kampanjen.

[Apache JMeter](https://jmeter.apache.org/) är ett kostnadsfritt, populära öppen källkod-verktyg med en stark community säkerhetskopiering.

**Bästa praxis**: Övervaka programmets prestanda.  
**Information om**: [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) är en utökningsbar (APM) Hanteringstjänst för programprestanda för webbutvecklare på flera plattformar. Du kan använda Application Insights för att övervaka ditt webbprogram. Den identifierar automatiskt prestandaavvikelser. Den innehåller analysverktyg för att hjälpa dig att diagnostisera problem och förstå vad användare faktiskt gör med din app. Den är avsedd för utvecklare och för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet.

## <a name="mitigate-and-protect-against-ddos"></a>Minimera och skydda mot DDoS
Distribuerade överbelastningsattacker (DDoS) är en typ av angrepp som används för att få slut på resurser. Målet är att påverka programmets tillgänglighet och dess förmåga att hantera legitima begäranden. Dessa attacker blir allt mer sofistikerade och större i storlek och effekt. De kan riktas till valfri slutpunkt som kan nås offentligt via internet.

Utforma och skapa DDoS-skydd kräver planering och design för en mängd olika fellägen. Följande är rekommenderade metoder för att skapa DDoS anpassningsbara tjänster i Azure.

**Bästa praxis**: Kontrollera att säkerhet är en prioritet under hela livscykeln för ett program, från design och implementering till distribution och drift. Program kan ha buggar som gör att en relativt låg volym av begäranden om att använda en massa resurser, vilket resulterar i avbrott i tjänsten.  
**Information om**: För att skydda en tjänst som körs på Microsoft Azure, bör du ha en god förståelse av programarkitekturen och fokusera på den [fem grundpelare för programkvalitet](https://docs.microsoft.com/azure/architecture/guide/pillars). Du bör känna till vanliga trafikvolymer modellen anslutning mellan programmet och andra program och Tjänsteslutpunkter som exponeras för det offentliga internet.

Se till att ett program är tillräckligt flexibel för att hantera DOS-attacker som riktas mot själva programmet är viktigast. Säkerhet och sekretess är inbyggt i Azure-plattformen, från och med den [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl). SDL-processen hanterar säkerheten i varje utvecklingsfas och ser till att Azure uppdateras ständigt för att göra det ännu säkrare.

**Bästa praxis**: Programmen utformas för [skala horisontellt](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) att uppfylla behovet av en förstärkt belastning, särskilt i händelse av en DDoS-attack. Om ditt program är beroende av en enda instans av en tjänst, skapas en enskild felpunkt. Etablering av flera instanser kan systemet mer skalbart och återhämtningsbart mer.  
**Information om**: För [Azure App Service](../app-service/app-service-value-prop-what-is.md)väljer en [App Service-plan](../app-service/overview-hosting-plans.md) som erbjuder flera instanser.

Konfigurera var och en av dina roller att använda för Azure Cloud Services, [flera instanser](../cloud-services/cloud-services-choose-me.md).

För [Azure Virtual Machines](../virtual-machines/windows/overview.md), se till att din VM-arkitektur innehåller fler än en virtuell dator och att varje virtuell dator ingår i en [tillgänglighetsuppsättning](../virtual-machines/virtual-machines-windows-manage-availability.md). Vi rekommenderar att du använder virtuella skalningsuppsättningar för funktioner för automatisk skalning.

**Bästa praxis**: Skikta säkerhetsskyddet i ett program minskar risken för angrepp. Implementera säkra utformning för dina program med hjälp av de inbyggda funktionerna i Azure-plattformen.  
**Information om**: Risken för angrepp ökar i storlek (ytan) av programmet. Du kan minska ytan med hjälp av listan över tillåtna program ska stänga exponerade IP-adressutrymmet och lyssningsportar som inte behövs på belastningsutjämnarna ([Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md) och [Azure Application Gateway](../application-gateway/application-gateway-create-probe-portal.md)).

[Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md) är ett annat sätt att minska risken för angrepp. Du kan använda [tjänsttaggar](../virtual-network/security-overview.md#service-tags) och [programsäkerhetsgrupper](../virtual-network/security-overview.md#application-security-groups) att minska komplexiteten för att skapa säkerhetsregler och konfigurera nätverkssäkerhet, som en naturlig förlängning av ett programs struktur.

Du bör distribuera Azure-tjänster i en [virtuellt nätverk](../virtual-network/virtual-networks-overview.md) när det är möjligt. Detta gör att service-resurser kommunicera via privata IP-adresser. Azure-tjänsttrafiken från ett virtuellt nätverk använder offentliga IP-adresser som käll-IP-adresser som standard.

Med hjälp av [tjänstslutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md) växlar tjänsten trafik för att använda privata vnet-adresser som käll-IP-adresser när de får åtkomst till Azure-tjänsten från ett virtuellt nätverk.

Ofta ser vi kundernas lokala resurser komma angripna tillsammans med sina resurser i Azure. Om du ansluter en lokal miljö till Azure, minimera exponering av lokala resurser till det offentliga internet.

Azure har två DDoS [Tjänsterbjudanden](../virtual-network/ddos-protection-overview.md) som ger skydd mot nätverksattacker:

- Grundläggande skydd är integrerad i Azure som standard utan extra kostnad. Ger skydd mot vanliga attacker på nätverkslager via ständig övervakning och i realtid minskning skalbarhet och kapacitet för globalt distribuerade Azure-nätverket. Basic kräver konfiguration eller programmet användarändringar och hjälper dig att skydda alla Azure-tjänster, inklusive PaaS-tjänster som Azure DNS.
- Standard protection tillhandahåller avancerade DDoS-skyddsfunktioner mot nätverksattacker. Det är automatiskt justerade för att skydda din specifika Azure-resurser. Skydd är enkelt att använda under genereringen av virtuella nätverk. Det kan också göras när du har skapat och kräver inga ändringar i programmet eller resursen.

## <a name="enable-azure-policy"></a>Aktivera Azure Policy
[Azure Policy](../governance/policy/overview.md) är en tjänst i Azure som används för att skapa, tilldela och hantera principer. Dessa principer tillämpar regler och effekterna på resurserna, så att resurserna följer företagets standarder och serviceavtal. Azure Policy uppfyller detta behov genom att utvärdera dina resurser för icke-kompatibilitet med tilldelade principer.

Aktivera Azure Policy att övervaka och tillämpa din organisations skriftliga princip. Det säkerställer efterlevnaden av företagets eller bestämmelsemässiga säkerhetskrav genom att hantera principer centralt över dina hybridmolnarbetsbelastningar. Lär dig hur du [skapa och hantera principer för att tvinga kompatibilitet](../governance/policy/tutorials/create-and-manage.md). Se [Azure Policy-definitionsstruktur](../governance/policy/concepts/definition-structure.md) en översikt av elementen i en princip.

Här följer några beprövade metoder för att följa när du antar Azure Policy:

**Bästa praxis**: Principen har stöd för flera typer av effekter. Du kan läsa om dem i [Azure Policy-definitionsstruktur](../governance/policy/concepts/definition-structure.md#policy-rule). Verksamheten kan påverkas negativt av den **neka** effekt och **åtgärda** i kraft, så starta med den **granska** att begränsa risken för negativ inverkan från princip.   
**Information om**: [Starta principdistributioner i granskningsläge](../governance/policy/concepts/definition-structure.md#policy-rule) och sedan fortsätta senare till **neka** eller **åtgärda**. Testa och granska resultatet av spårningsseffekt innan du går vidare till **neka** eller **åtgärda**.

Mer information finns i [skapa och hantera principer för att tvinga kompatibilitet](../governance/policy/tutorials/create-and-manage.md).

**Bästa praxis**: Identifiera rollerna som är ansvarig för att övervaka bryter mot policyer och säkerställer rätt Reparationsåtgärd utförs snabbt.   
**Information om**: Har den tilldelade rollen övervaka efterlevnad via den [Azure-portalen](../governance/policy/how-to/get-compliance-data.md#portal) eller via den [kommandoraden](../governance/policy/how-to/get-compliance-data.md#command-line).

**Bästa praxis**: Azure Policy är en teknisk representation av en organisations skriftliga riktlinjer. Mappa alla Azure-principer till organisationens principer för att minska förvirring och ökar konsekvens.   
**Information om**: Mappning av dokumentet i dokumentationen för din organisation eller i Azure principen genom att lägga till en referens till organisationens lösenordsprincip i Azure [principbeskrivningen](../governance/policy/concepts/definition-structure.md#display-name-and-description) eller Azure policy [initiativ](../governance/policy/concepts/definition-structure.md#initiatives) Beskrivning.

## <a name="monitor-azure-ad-risk-reports"></a>Övervaka Azure AD risk rapporter
Merparten av säkerhetsbrott sker när angripare får tillgång till en miljö genom att stjäla en användares identitet. Identifiera komprometterade identiteter är ingen enkelt uppgift. Azure AD använder anpassningsbar machine learning-algoritmer och heuristik för att identifiera misstänkta åtgärder relaterade till dina användarkonton. Varje identifierad misstänkt åtgärd lagras i en post som kallas en [riskhändelsen](../active-directory/reports-monitoring/concept-risk-events.md). Riskhändelser registreras i Azure AD-säkerhetsgrupper rapporter. Mer information finns i om den [användare i farozonen](../active-directory/reports-monitoring/concept-user-at-risk.md) och [säkerhetsrapporten för riskfyllda inloggningar](../active-directory/reports-monitoring/concept-risky-sign-ins.md).

## <a name="next-steps"></a>Nästa steg
Se [säkerhet i Azure-metodtips och mönster](security-best-practices-and-patterns.md) för flera beprövade metoder för att använda när du utforma, distribuera och hantera dina molnlösningar med hjälp av Azure.

Följande resurser är tillgängliga för att tillhandahålla mer allmän information om Azure-säkerhet och relaterade Microsoft-tjänster:
* [Azure-Säkerhetsteamets blogg](https://blogs.msdn.microsoft.com/azuresecurity/) – uppdaterad information på senast inom Azure-säkerhet
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – där kan du rapportera säkerhetsproblem i Microsoft, inklusive problem med Azure, eller mejla till secure@microsoft.com
