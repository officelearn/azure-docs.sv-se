---
title: Gateway-loggning, granskning och synlighet i Azure Australien
description: Hur du konfigurerar loggning, granskning och synlighet inom de australiska regionerna för att uppfylla de särskilda kraven i den australiska regeringens politik, förordningar och lagstiftning.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: b7a9f28d06b5e921b5f1b8defa151641bb039940
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990205"
---
# <a name="gateway-logging-auditing-and-visibility-in-azure-australia"></a>Gateway-loggning, granskning och synlighet i Azure Australien

Att upptäcka och reagera på cyberhot säkerhetshot är beroende av att skapa, samla in och analysera data som rör driften av ett system.

Microsoft har inbyggda verktyg i Azure som hjälper dig att implementera loggning, granskning och synlighet för att hantera säkerheten för dina system som distribueras i Azure. Det finns också en referens arkitektur som överensstämmer med ACSC-konsument vägledningen (australisk cyberhot Security Centre) och avsikten med informations säkerhets hand boken (ISM).

Gateways fungerar som mekanismer för informations flödes kontroll på nätverks nivå och kan också styra information i de högre skikten i OSI-modellen (Open system Interconnect). Gatewayer är nödvändiga för att kontrol lera data flöden mellan säkerhets domäner och förhindra obehörig åtkomst från externa nätverk. Med hänsyn till den kritiska gatewayen vid kontroll av informations flödet mellan säkerhets domäner, kan eventuella problem, särskilt vid högre klassificeringar, få allvarliga konsekvenser. Därför är robusta mekanismer för att meddela personal till situationer som kan orsaka cyberhot säkerhets incidenter särskilt viktiga för gatewayer.

Implementering av loggnings-och aviserings funktioner för gatewayer kan hjälpa till att identifiera cyberhot säkerhets incidenter, försök till intrång och ovanliga användnings mönster. Lagring av händelse loggar på en separat säker logg server ökar dessutom svårigheterna för en angripare för att ta bort loggnings information för att förstöra bevis på en riktad cyberhot-intrång.

## <a name="australian-cyber-security-centre-acsc-requirements"></a>ACSC-krav (australisk cyberhot Security Centre)

De övergripande säkerhets kraven för samväldet samordnings system definieras i ACSC information Security manual (ISM). För att hjälpa samsignde entiteter att uppfylla dessa krav i Azure, *ACSC-konsument guide – Microsoft Azure vid skyddad* och *ACSC certifierings rapport – Microsoft Azure* publikationer information följande särskilda krav gäller till loggning, granskning och synlighet:

1. För att minimera riskerna som uppstår vid användning av delade underliggande moln resurser, måste samväldet entiteter välja att Microsoft Azure tillhandahållna funktioner, inklusive Azure Security Center, Azure Monitor, Azure Policy och Azure Advisor för att hjälpa entiteter att utföra Real tids övervakning av sina Azure-arbetsbelastningar

2. ACSC rekommenderar också att samväldet entiteter vidarebefordrar alla behöriga säkerhets loggar till ACSC för hela den australiska regeringens övervakning

3. För att hjälpa till med risk minskning bör samväldet entiteter konfigureras inom sina Azure-prenumerationer:

    * Aktivera Azure Security Center
    * Uppgradera till standard nivån
    * Aktivera automatisk etablering av Microsoft Monitoring Agent till virtuella Azure-datorer som stöds
    * Regelbundet granska, prioritise och minimera säkerhets rekommendationer och aviseringar på Security Center-instrumentpanelen

4. Myndighets enheter måste aktivera logg-och händelse vidarebefordran från Azure-prenumerationen till ACSC för att tillhandahålla ACSC med insyn i den här vägledningen. Azure Event Hubs ger möjlighet att utföra extern logg strömning till ACSC eller lokala system som ägs av samväldet entiteten

5. Samväldet samväldet-enheter ska justera den loggning som de gör i Azure till de krav som anges i ISM

6. Microsoft behåller loggar i Azure i 90 dagar. Kundentiteter måste implementera en logg lagrings ordning för att säkerställa att loggar kan behållas under de sju år som krävs enligt PEERCACHELAGRING-AFDA

7. Samväldet-entiteter som har lokala eller Azure-baserade funktioner för säkerhets information och händelse hantering (SIEM) kan också vidarebefordra loggar till dessa system

8. Samväldet entiteter bör implementera Network Watcher flödes loggar för nätverks säkerhets grupper (NSG: er) och Virtual Machines. Dessa loggar bör lagras i ett dedikerat lagrings konto som bara innehåller säkerhets loggar och åtkomst till lagrings kontot bör skyddas med rollbaserade åtkomst kontroller

9. Samväldet entiteter måste implementera ACSC-konsument vägledning för att säkerställa att Azure-arbetsbelastningar uppfyller syftet med ISM för loggning och övervakning. Samväldet entiteter måste också välja Azure-funktioner som hjälper ACSC att ta emot övervakning, aviseringar och loggar i real tid som är kopplade till den australiska regeringens användning av Azure

## <a name="architecture"></a>Arkitektur

För att på ett säkert sätt förstå nätverks trafiken som går in och lämnar din Azure-miljö måste den nödvändiga loggningen vara aktive rad på rätt uppsättning komponenter. Detta säkerställer att miljön är fullständigt synlig och innehåller nödvändiga data för att utföra analyser.

![Azures övervaknings arkitektur](media/visibility.png)

## <a name="components"></a>Komponenter

Arkitekturen som visas ovan består av diskreta komponenter som tillhandahåller funktionen för antingen logg källor, logg insamling, logg kvarhållning, logg analys eller incident svar. Den här arkitekturen omfattar enskilda komponenter som vanligt vis är tillgängliga i Azure-distributioner med Internet.

|Funktioner|Komponenter|
|---|---|
|Logg källor|<ul><li>Application Gateway</li><li>VPN Gateway</li><li>Azure Firewall</li><li>Virtuella nätverks enheter</li><li>Azure Load Balancer</li><li>Virtuella datorer</li><li>DNS-servrar (Domain Naming system)</li><li>Syslog-och/eller logg samlings servrar</li><li>Nätverkssäkerhetsgrupper (NSG)</li><li>Azure-aktivitetsloggen</li><li>Azure Diagnostic-logg</li><li>Azure Policy</li></ul>|
|Logginsamling|<ul><li>Event Hubs</li><li>Network Watcher</li><li>Log Analytics</li></ul>|
|Logg kvarhållning|<ul><li>Azure Storage</li></ul>|
|Logganalyser|<ul><li>Azure Security Center (ASC)</li><li>Azure Advisor</li><li>Log Analytics lösningar<ul><li>Trafikanalys</li><li>DNS Analytics (förhandsversion)</li><li>Log Analytics-aktivitet</li></ul></li><li>SIEM</li><li>ACSC</li></ul>|
|Incidenthantering|<ul><li>Azure-aviseringar</li><li>Azure Automation</li></ul>|
|

Arkitekturen fungerar genom att först skapa loggar från de nödvändiga källorna och sedan samla in dem i centraliserade databaser. När du har samlat in loggarna kan de vara:

* används av Azure Analysis Services för att få insikt,
* vidarebefordra till externa system eller
* kom Arkiverad på lagring för långsiktig kvarhållning.

Om du vill svara på viktiga händelser eller incidenter som identifieras av analys verktyg kan du konfigurera aviseringar och automatisera automatisering för att vidta nödvändiga åtgärder för proaktiv hantering och svar.

## <a name="general-guidance"></a>Allmänna riktlinjer

När du implementerar de komponenter som anges i den här artikeln gäller följande allmänna rikt linjer:

* Verifiera regionens tillgänglighet för tjänster, och se till att alla data ligger kvar inom godkända platser och distribueras till AU central eller AU Central 2 som första preferens för skyddade arbets belastningar

* Gå till *certifierings rapporten för Azure-ACSC – skyddad 2018* -publikation för att se om det finns certifierings status för enskilda tjänster och utföra själv utvärdering av alla relevanta komponenter som inte ingår i rapporten enligt ACSC-.  *Microsoft Azure vid skyddad*

* För komponenter som inte refereras till i den här artikeln bör samväldet entiteter följa de principer som finns för att skapa, samla in, analysera och behålla loggar

* Identifiera och prioritise loggning, granskning och synlighet på hög värdes system samt alla ingångs-och utgångs punkter i nätverket för system som finns i Azure

* Konsolidera loggar och minimera antalet instanser av loggnings verktyg som lagrings konton, Log Analytics arbets ytor och Event Hubs

* Begränsa administrativa privilegier genom rollbaserade åtkomst kontroller

* Använda Multi-Factor Authentication (MFA) för konton som administrerar eller konfigurerar resurser i Azure

* Se till att administratörer har de behörigheter som krävs för varje prenumeration när centralising logg insamling över flera prenumerationer

* Se till att nätverks anslutningen och all nödvändig proxykonfiguration för Virtual Machines, inklusive virtuella nätverks installationer (NVA), logg samlings servrar och DNS-servrar, för att ansluta till nödvändiga Azure-tjänster som Log Analytics arbets ytor, Event Hubs och lagring

* Konfigurera Microsoft Monitoring Agent (MMA) för användning av TLS-version 1,2

* Använd Azure Policy för att övervaka och genomdriva efterlevnad med krav

* Framtvinga kryptering på alla data centraler, till exempel lagring och databaser

* Använd lokalt redundant lagring (LRS) och ögonblicks bilder för tillgänglighet av lagrings konton och tillhör ande data

* Överväg Geo-redundant lagring (GRS) eller lagring utanför platsen för att anpassa med katastrof återställnings strategier

|Resource|URL|
|---|---|
|Australiska lagar och principer för regelefterlevnad|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Azure-produkter – australiska regioner och icke-regionala|[https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, Australien, centrala, Australien-centrala-2, Australien, östra, Australien, sydöstra](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|Fakta blad om Microsoft Azure Säkerhet och granskning logg hantering|[https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf)|
|Konfiguration av Microsoft Monitoring Agent|[https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)|
|

## <a name="component-guidance"></a>Vägledning för komponenter

Det här avsnittet innehåller information om syftet med varje komponent och dess roll i arkitekturen för övergripande loggning, granskning och synlighet. Ytterligare länkar finns för att få åtkomst till användbara resurser, till exempel referens dokumentation, guider och självstudier.

## <a name="log-sources"></a>Logg källor

Innan en analys, avisering eller rapportering kan slutföras, måste de nödvändiga loggarna genereras. Azure-loggar kategoriseras i kontroll-/hanterings loggar, data Plans loggar och bearbetade händelser.

|type|Beskrivning|
|---|---|
|Kontroll-/hanterings loggar|Ange information om Azure Resource Manager åtgärder|
|Data Plans loggar|Ange information om händelser som Aktiver ATS som en del av Azure-resursanvändningen, till exempel loggar i en virtuell dator och de diagnostikloggar som är tillgängliga via Azure Monitor|
|Bearbetade händelser|Ange information om analyserade händelser/aviseringar som har bearbetats av Azure, till exempel där Azure Security Center har bearbetat och analyserat prenumerationer för att tillhandahålla säkerhets aviseringar|
|

### <a name="application-gateway"></a>Application Gateway

Azure Application Gateway är en av de möjliga start punkterna i en Azure-miljö, så du måste samla in information som rör inkommande anslutningar som kommunicerar med webb program. Application Gateway kan tillhandahålla viktig information om användningen av webb program och hjälpa till att identifiera cyberhot säkerhets incidenter. Application Gateway skickar metadata till aktivitets loggen och diagnostikloggar i Azure Monitor där de kan användas i Log Analytics eller distribueras till ett Händelsehubben eller ett lagrings konto.

|Resurser|Länka|
|---|---|
|Dokumentation om Application Gateway|[https://docs.microsoft.com/azure/application-gateway/](https://docs.microsoft.com/azure/application-gateway/)|
|Snabb starts guide för Application Gateway|[https://docs.microsoft.com/azure/application-gateway/quick-create-portal](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)|
|

### <a name="vpn-gateway"></a>VPN Gateway

VPN Gateway är en potentiell start punkt för en mängd olika typer av kommunikation i Azure-miljön, till exempel anslutning till en lokal miljö och administrativ trafik. Loggning på VPN-gatewayer ger insikt och spårbarhet för de anslutningar som görs i Azure-miljön. Loggning kan tillhandahålla granskning och analys samt hjälpa till med identifiering eller undersökning av skadliga eller avvikande anslutningar. VPN Gateway loggar skickas till Azure Monitor aktivitets logg där de kan användas i Log Analytics eller distribueras till ett Händelsehubben eller ett lagrings konto.

|Resurser|Länka|
|---|---|
|Dokumentation om VPN Gateway|[https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway)|
|Australisk myndighets VPN Gateway vägledning|[Konfiguration av Azure-VPN Gateway](vpn-gateway.md)|
|

### <a name="azure-firewall"></a>Azure Firewall

Azure-brandväggen tillhandahåller en kontrollerad slut punkt från en Azure-miljö och de loggar som genereras, som innehåller information om försök och lyckade utgående anslutningar, är ett viktigt element i din loggnings strategi. Med dessa loggar kan du kontrol lera att systemen fungerar som de är utformade, samt hjälpa till att identifiera skadlig kod eller aktörer som försöker ansluta till icke-auktoriserade externa system. Azure-brandväggen skriver loggar till aktivitets loggen och diagnostikloggar i Azure Monitor där den kan användas i Log Analytics eller distribueras till ett Händelsehubben eller ett lagrings konto.

|Resurser|Länka|
|---|---|
|Dokumentation om Azure Firewall|[https://docs.microsoft.com/azure/firewall/](https://docs.microsoft.com/azure/firewall)|
|Självstudier: Övervaka Azure Firewall-loggar och mått|[https://docs.microsoft.com/azure/firewall/tutorial-diagnostics](https://docs.microsoft.com/azure/firewall/tutorial-diagnostics)|
|

### <a name="network-virtual-appliances-nva"></a>Virtuella nätverks enheter (NVA)

NVA kan användas för att komplettera de säkerhetsfunktioner som är tillgängliga internt i Azure. Loggarna som genereras på NVA kan vara värdefulla resurser för att identifiera cyberhot säkerhets incidenter och är en viktig del av en övergripande loggnings-, gransknings-och Synlighets strategi. Om du vill avbilda loggar från NVA använder du Microsoft Monitoring Agent (MMA). För NVA som inte har stöd för installation av MMA bör du överväga att använda en syslog eller en annan logg insamlings Server för att vidarebefordra loggar.

|Resurser|Länka|
|---|---|
|Översikt över virtuella nätverks enheter|[https://azure.microsoft.com/solutions/network-appliances](https://azure.microsoft.com/solutions/network-appliances)|
|Dokumentation om NVA|Läs leverantörens dokumentation om implementeringen av relevanta NVA i Azure|
|

### <a name="azure-load-balancer"></a>Azure Load Balancer

Azure Load Balancer loggar används för att få användbar information om anslutningar och användning som rör system som distribueras i Azure. Detta kan användas för övervakning av hälso tillstånd och tillgänglighet, men utgör även en annan viktig komponent för att få nödvändiga insikter om kommunikations trafik och upptäcka skadliga eller avvikande trafik mönster. Azure Load Balancer loggar till aktivitets loggen och diagnostikloggar i Azure Monitor där det kan användas i Log Analytics eller distribueras till ett Händelsehubben eller ett lagrings konto.

|Resurser|Länka|
|---|---|
|Azure Load Balancer dokumentation|[https://docs.microsoft.com/azure/load-balancer](https://docs.microsoft.com/azure/load-balancer)|
|Mått och hälsodiagnostik för Standard Load Balancer|[https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)|
|

### <a name="virtual-machines"></a>Virtuella datorer

Virtual Machines är slut punkter som skickar och tar emot nätverkskommunikation, bearbetar data och tillhandahåller tjänster. Som Virtual Machines kan vara värd för data eller viktiga system tjänster, se till att de fungerar korrekt och identifiera cyberhot säkerhets incidenter kan vara kritiska. Virtual Machines samla in olika händelse-och gransknings loggar som kan spåra systemets drift och de åtgärder som utförs i systemet. Loggar som samlas in på Virtual Machines kan vidarebefordras till en Log Analytics arbets yta med Microsoft Monitoring Agent där de kan analyseras av Azure Security Center och tillämpliga Log Analytics lösningar. Virtual Machines kan också integrera direkt med Azure Event Hubs eller till en SIEM, antingen direkt eller via en logg insamlings Server.

|Resurser|Länka|
|---|---|
|Virtuella datorer|[https://docs.microsoft.com/azure/virtual-machines](https://docs.microsoft.com/azure/virtual-machines)|
|Samla in data från Virtual Machines|[https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|
|Strömma virtuella dator loggar till Event Hubs|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs)|
|

### <a name="domain-name-services-dns-servers"></a>DNS-servrar (Domain Name Services)

DNS-server loggar ger viktig information som rör de tjänster som systemet försöker komma åt, antingen internt eller externt. Genom att fånga DNS-loggar kan du identifiera en cyberhot säkerhets incident och tillhandahålla inblick i typen av incident och de system som kan påverkas. Microsoft Management Agent (MMA) kan användas på DNS-servrar för att vidarebefordra loggarna till Log Analytics för användning i DNS-analys (för hands version).

|Resurser|Länka|
|---|---|
|Azure-namnmatchning för virtuella nätverk|[https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)|
|

### <a name="syslog-and-log-collection-servers"></a>Syslog-och logg insamlings servrar

Om du vill ta emot loggar från virtuella nätverks installationer eller anpassade säkerhets loggar från andra system för användning i en SIEM, kan dedikerade servrar distribueras i Azure virtuella nätverk. Syslog-loggar kan samlas in på en Syslog-server och vidarebefordras till Log Analytics för analys. En logg insamlings Server är ett allmänt villkor för alla funktioner för logg sammansättning och distribution som används av centraliserade övervaknings system eller Siem. Dessa kan användas för att förenkla nätverks arkitekturen och säkerheten och för att filtrera och aggregera loggar innan de distribueras till den centraliserade kapaciteten.

|Resurser|Länka|
|---|---|
|Syslog-datakällor i Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog)|
|Logg insamlings Server|Läs leverantörens dokumentation för mer information om övervakning och SIEM-arkitektur|
|

### <a name="network-security-groups-nsgs"></a>Nätverks säkerhets grupper (NSG: er)

NSG: er styr trafik till och från virtuella nätverk i Azure. NSG: er tillämpar regler för de trafikflöde som tillåts eller nekas, vilket omfattar trafik i Azure och mellan Azure och externa nätverk, till exempel lokalt eller Internet. NSG: er tillämpas på undernät i ett virtuellt nätverk eller enskilda nätverks gränssnitt. För att kunna samla in information om trafik som loggar in och lämnar system i Azure kan NSG-loggar aktive ras via funktionen Network Watcher NSG Flow-loggar. De här loggarna används för att skapa en bas linje för systemets standard drift och är data källan för Trafikanalys, vilket ger detaljerade insikter om trafik mönster i system som finns i Azure.

|Resurser|Länka|
|---|---|
|Dokumentation om nätverks säkerhets grupp|[https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|Introduktion till flödes loggning för nätverks säkerhets grupper|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|
|Självstudier: Logga nätverks trafik till och från en virtuell dator med hjälp av Azure Portal|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)|
|

### <a name="azure-activity-log"></a>Azure-aktivitetsloggen

Azure aktivitets logg, som är en del av Azure Monitor, är en prenumerations logg som ger inblick i händelser på prenumerations nivå som har inträffat i Azure. Aktivitets loggen kan hjälpa till att avgöra vad, vem och när som krävs för alla Skriv åtgärder (skicka, skicka och ta bort) ***på*** resurserna i en prenumeration. Aktivitets loggen är avgörande för spårning av konfigurations ändringar som gjorts i Azure-miljön. Azure aktivitets loggar är automatiskt tillgängliga för användning i Log Analytics-lösningar och kan skickas till Event Hubs eller Azure Storage för bearbetning eller kvarhållning.

|Resurser|Länka|
|---|---|
|Dokumentation om Azure aktivitets logg|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|Strömma Azure-aktivitets loggen till Event Hubs|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs)|
|

### <a name="azure-diagnostic-log"></a>Azure Diagnostic-logg

Azure Monitor diagnostikloggar loggar genereras av en Azure-tjänst som ger omfattande, frekventa data om driften av tjänsten. Diagnostikloggar ger inblick i driften av en resurs på en detaljerad nivå och kan användas för ett antal krav som granskning eller fel sökning. Azure Diagnostic-loggar är automatiskt tillgängliga för användning i Log Analytics lösningar och kan skickas till Event Hubs eller Azure Storage för bearbetning eller kvarhållning.

|Resurser|Länka|
|---|---|
|Dokumentation om Azure Diagnostic Log|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|Support tjänster för diagnostikloggar|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema)|
|

### <a name="azure-policy"></a>Azure Policy

Azure Policy tillämpar regler för hur resurser kan distribueras, till exempel typ, plats och konfiguration. Azure Policy kan konfigureras för att säkerställa att resurser bara kan distribueras om de uppfyller kraven. Azure Policy är en kärn komponent som upprätthåller integriteten i en Azure-miljö. Händelser som rör Azure Policy loggas i Azure aktivitets loggen och är automatiskt tillgängliga för användning i Log Analytics lösningar eller kan skickas till Event Hubs eller Azure Storage för bearbetning eller kvarhållning.

|Resurser|Länka|
|---|---|
|Dokumentation om Azure Policy|[https://docs.microsoft.com/azure/governance/policy](https://docs.microsoft.com/azure/governance/policy)|
|Använda Azure Policy-och Resource Manager-mallar med Azure-ritningar|[https://docs.microsoft.com/azure/governance/blueprints/overview](https://docs.microsoft.com/azure/governance/blueprints/overview)|
|

## <a name="log-collection"></a>Logg insamling

När du har genererat från flera logg källor måste loggar lagras på en centraliserad plats för kontinuerlig åtkomst och analys. Azure tillhandahåller flera metoder och alternativ för logg insamling som kan utnyttjas beroende på logg typen och kraven.

### <a name="event-hubs"></a>Event Hubs

Syftet med en Event Hub är att aggregera loggdata för olika källor för distribution. Från Händelsehubben kan loggdata skickas till en SIEM, till ACSC för efterlevnad och lagring för långsiktig kvarhållning av data.

|Resurser|Länka|
|---|---|
|Dokumentation om Event Hubs|[https://docs.microsoft.com/azure/event-hubs](https://docs.microsoft.com/azure/event-hubs)|
|Vägledning om Event Hubs och externa verktyg|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs)|
|

### <a name="log-analytics"></a>Log Analytics

Log Analytics är en del av Azure Monitor och används för logg analys. Log Analytics använder en arbets yta som lagrings funktion där loggdata kan göras tillgängliga för en mängd olika analys verktyg och lösningar som är tillgängliga i Azure. Log Analytics integreras med en mängd olika Azure-komponenter direkt, samt Virtual Machines via Microsoft Monitoring Agent.

|Resurser|Länka|
|---|---|
|Log Analytics-dokumentation|[https://docs.microsoft.com/azure/azure-monitor](https://docs.microsoft.com/azure/azure-monitor)|
|Självstudier: Analysera data i Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata)|
|

### <a name="network-watcher"></a>Network Watcher

Användningen av Network Watcher rekommenderas av ACSC för att hjälpa till att förstå och samla in nätverks trafik i en Azure-prenumeration. NSG Flow-loggar ger indata till Trafikanalys lösning i Log Analytics, vilket ger ökad synlighet, analys och rapportering internt via Azure. Network Watcher också tillhandahålla en paket insamlings funktion direkt från Azure Portal utan att behöva logga in på den virtuella datorn. Med paket fångst kan du skapa sessioner för att skapa paket och spåra trafik till och från en virtuell dator.

|Resurser|Länka|
|---|---|
|Network Watcher|[https://docs.microsoft.com/azure/network-watcher](https://docs.microsoft.com/azure/network-watcher)|
|Översikt över paket insamling|[https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview)|
|

## <a name="log-retention"></a>Logg kvarhållning

För myndigheter i Australien måste de loggar som samlas in i Azure behållas i enlighet med de nationella arkiven av Australien [administrations funktioner för AFDA ()](http://www.naa.gov.au/information-management/records-authorities/types-of-records-authorities/AFDA/index.aspx), som anger att loggar sparas i upp till sju år.

|Logg plats|Kvarhållningsperiod|
|---|---|
|Azure-aktivitetsloggen|Upp till 90 dagar|
|Log Analytics-arbetsyta|Upp till två år|
|Händelsehubb|Upp till sju dagar|
|

Det är ditt ansvar att se till att loggar arkiveras på lämpligt sätt för att uppfylla AFDA och andra lagstiftnings krav.

### <a name="azure-storage"></a>Azure Storage

Azure Storage är lagrings platsen för loggar för långsiktig kvarhållning i Azure. Azure Storage kan användas för att arkivera loggar från Azure, inklusive Event Hubs, Azure aktivitets logg och Azure Diagnostic-loggar. Perioden för kvarhållning av data i lagrings utrymmet kan anges till noll, eller så kan du ange ett antal dagar. En kvarhållning på noll dagar innebär att loggarna alltid sparas, annars kan värdet vara ett valfritt antal dagar mellan 1 och 2147483647.

|Resurser|Länka|
|---|---|
|Azure Storage-dokumentation|[https://docs.microsoft.com/azure/storage](https://docs.microsoft.com/azure/storage)|
|Samla in händelser via Azure Event Hubs i Azure Blob Storage eller Azure Data Lake Storage|[https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Självstudier: Arkivera Azure mått och loggdata med hjälp av Azure Storage|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data)|
|Azure Storage replikering|[https://docs.microsoft.com/azure/storage/common/storage-redundancy](https://docs.microsoft.com/azure/storage/common/storage-redundancy)|
|Skapa en ögonblicks bild av en BLOB|[https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)|
|

## <a name="log-analysis"></a>Logganalyser

När de har genererats och lagrats på en centraliserad plats, måste loggarna analyseras för att hjälpa till att identifiera försöks-eller lyckade säkerhets incidenter. När säkerhets incidenter identifieras behöver ett organ möjlighet att svara på dessa incidenter och spåra, innehålla och åtgärda eventuella hot.

### <a name="azure-security-center-asc"></a>Azure Security Center (ASC)

Azure Security Center tillhandahåller enhetlig säkerhets hantering och Avancerat skydd. Azure Security Center kan tillämpa säkerhets principer över arbets belastningar, begränsa exponeringen för hot och identifiera och reagera på attacker. Azure Security Center tillhandahåller instrument paneler och analys över flera olika Azure-komponenter. Användningen av Azure Security Center anges som ett krav i ACSC-konsument vägledningen.

|Resurser|Länka|
|---|---|
|Dokumentation om Azure Security Center|[https://docs.microsoft.com/azure/security-center](https://docs.microsoft.com/azure/security-center)|
|Snabbstart: Publicera din Azure-prenumeration till Security Center Standard|[https://docs.microsoft.com/azure/security-center/security-center-get-started](https://docs.microsoft.com/azure/security-center/security-center-get-started)|
|

### <a name="traffic-analytics"></a>Trafikanalys

Trafikanalys är en molnbaserad lösning som ger insyn i användar-och program aktivitet i Azure. Trafik analys analyser Network Watcher NSG flödes loggar för att ge insikter om trafikflöde i Azure. Trafikanalys används för att tillhandahålla instrument paneler, rapporter, analyser och händelse svar som är relaterade till nätverks trafiken som visas i virtuella nätverk. Trafikanalys ger betydande insikt och hjälper till att identifiera och lösa cyberhot säkerhets incidenter.

|Resurser|Länka|
|---|---|
|Trafikanalys dokumentation|[https://docs.microsoft.com/azure/network-watcher/traffic-analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)|
|

### <a name="azure-advisor"></a>Azure Advisor

Azure Advisor analyserar resurs konfigurationen och andra data för att rekommendera lösningar som hjälper till att förbättra prestanda, säkerhet och hög tillgänglighet för resurser samtidigt som du letar efter möjligheter att minska det totala antalet Azure-utgifter. Azure Advisor rekommenderas av ACSC och ger enkel åtkomst och detaljerad information om konfigurationen av Azure-miljön.

|Resurser|Länka|
|---|---|
|Dokumentation om Azure Advisor|[https://docs.microsoft.com/azure/advisor](https://docs.microsoft.com/azure/advisor)|
|Kom igång med Azure Advisor|[https://docs.microsoft.com/azure/advisor/advisor-get-started](https://docs.microsoft.com/azure/advisor/advisor-get-started)|
|

### <a name="dns-analytics-preview"></a>DNS Analytics (förhandsversion)

DNS-analys är en Log Analytics lösning som samlar in, analyserar och korrelerar Windows DNS analys-och gransknings loggar och andra relaterade data. DNS-analys identifierar klienter som försöker matcha skadliga domän namn, inaktuella resurs poster, vanliga domän namn och pratsam DNS-klienter. DNS-analys även insikter om begärd belastning på DNS-servrar och dynamiska DNS-registreringar. DNS-analys används för att tillhandahålla instrument paneler, rapporter, analyser och händelse svar som är relaterade till DNS-frågor som görs i en Azure-miljö. DNS-analys ger betydande insikt och hjälper till att identifiera och lösa cyberhot säkerhets incidenter.

|Resurser|Länka|
|---|---|
|Dokumentation om DNS Analytics|[https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics](https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics)|
|

### <a name="activity-log-analytics"></a>Log Analytics-aktivitet

Aktivitetslogganalys är en Log Analytics lösning som hjälper dig att analysera och söka i Azure aktivitets loggen över flera Azure-prenumerationer. Aktivitetslogganalys används för att tillhandahålla centraliserade instrument paneler, rapporter, analyser och event Response-funktioner relaterade till de åtgärder som utförs på resurser i hela Azure-miljön. Aktivitetslogganalys kan hjälpa till med granskning och undersökning.

|Resurser|Länka|
|---|---|
|Samla in och analysera Azure-aktivitetsloggar i Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)|
|

### <a name="security-information-and-event-management-siem"></a>Säkerhets information och händelse hantering (SIEM)

En SIEM är ett system som ger centraliserad lagring, granskning och analys av säkerhets loggar, med definierade mekanismer för att mata in en mängd olika loggdata och intelligenta verktyg för analys, rapportering och incident identifiering och-svar. Du kan använda SIEM-funktioner som innehåller information om Azure-loggning för att komplettera de säkerhetsfunktioner som tillhandahålls internt i Azure. Samväldet entiteter kan använda en SIEM som finns på Virtual Machines i Azure, lokalt eller som en SaaS-kapacitet (Software as a Service), beroende på särskilda krav.

|Resurser|Länka|
|---|---|
|Azure Sentinel (för hands version)|[https://azure.microsoft.com/services/azure-sentinel](https://azure.microsoft.com/services/azure-sentinel)|
|Dokumentation om SIEM|Läs leverantörens dokumentation för SIEM-arkitektur och vägledning|
|Använd Azure Monitor för att integrera med SIEM-verktyg|[https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)|
|

### <a name="australian-cyber-security-centre"></a>Australiskt cyberhot säkerhets Center

Det australiska cyberhot Security Centre (ACSC) är den australiska regeringens ledare för National cyberhot Security. Den samlar in cyberhot säkerhetsfunktioner från den australiska regeringen för att förbättra cyberhot-återhämtningen hos den australiska communityn och stödja den ekonomiska och sociala halon i Australien i den digitala tids åldern. ACSC rekommenderar att samväldet-entiteter vidarebefordrar alla systembaserade loggfiler, händelser och loggar till ACSC för hela den australiska regeringens övervakning.

|Resurser|Länka|
|---|---|
|Den australiska cyberhot Security Center-webbplatsen|[https://www.acsc.gov.au](https://www.acsc.gov.au)|
|

## <a name="incident-response"></a>Incidenthantering

Genom att skapa lämpliga loggar kan du samla in dem i centraliserade databaser och utföra analyser för att öka förståelsen av system och ger mekanismer för att identifiera cyberhot säkerhets incidenter. När incidenter eller händelser har identifierats är nästa steg att reagera på dessa händelser och utföra åtgärder för att underhålla system hälsan och skydda tjänster och data från kompromisser. Azure tillhandahåller en kombination av tjänster för att effektivt svara på händelser som inträffar.

### <a name="azure-alerts"></a>Azure-aviseringar

Azure-aviseringar kan användas för att meddela support och säkerhets personal som svar på specifika händelser. Detta gör det möjligt för en samväldet-entitet att proaktivt reagera på identifieringen av relevanta händelser som aktive ras av Analysis Services som anges i den här artikeln.

|Resurser|Länka|
|---|---|
|Översikt över aviseringar i Microsoft Azure|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts)|
|Hantera och åtgärda säkerhetsaviseringar i Azure Security Center|[https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)|
|Svara på händelser med Azure Monitor-aviseringar|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)|
|

### <a name="azure-automation"></a>Azure Automation

Azure Automation gör att samväldet-entiteter kan utlösa åtgärder som svar på händelser. Detta kan vara att starta en paket fångst på Virtual Machines, köra ett arbets flöde, stoppa eller starta Virtual Machines eller tjänster eller en rad andra uppgifter. Automation möjliggör snabbt svar på aviseringar utan manuella åtgärder, vilket minskar svars tiden och allvarlighets graden för en incident eller händelse.

|Resurser|Länka|
|---|---|
|Azure Automation dokumentation|[https://docs.microsoft.com/azure/automation](https://docs.microsoft.com/azure/automation)|
|Instruktions guide: Använda en avisering för att utlösa en Azure Automation Runbook|[https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)|
|

## <a name="next-steps"></a>Nästa steg

Läs artikeln om [SSL-säker fjärr administration](gateway-secure-remote-administration.md) för mer information om hur du hanterar din gateway-miljö säkert i Azure.
