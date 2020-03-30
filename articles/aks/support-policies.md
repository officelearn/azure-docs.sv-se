---
title: Supportprinciper för Azure Kubernetes Service (AKS)
description: Lär dig mer om Azure Kubernetes Service (AKS) supportprinciper, delat ansvar och funktioner som finns i förhandsversion (eller alfa eller beta).
services: container-service
author: jnoller
ms.topic: article
ms.date: 01/24/2020
ms.author: jenoller
ms.openlocfilehash: a5d90106a85a61cbf499c4c08130392b922a45f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593588"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Supportprinciper för Azure Kubernetes Service

Den här artikeln innehåller information om principer för teknisk support och begränsningar för Azure Kubernetes Service (AKS). Artikeln beskriver också hantering av arbetsnoder, hanterade styrplankomponenter, komponenter med öppen källkod från tredje part och hantering av säkerhet eller korrigering.

## <a name="service-updates-and-releases"></a>Uppdateringar och versioner av tjänsten

* Information om versionen finns i [AKS-viktig information](https://github.com/Azure/AKS/releases).
* Information om funktioner i förhandsversionen finns i [AKS förhandsgranskningsfunktioner och relaterade projekt](https://github.com/Azure/AKS/blob/master/previews.md).

## <a name="managed-features-in-aks"></a>Hanterade funktioner i AKS

Basinfrastruktur som en tjänst (IaaS) molnkomponenter, till exempel beräknings- eller nätverkskomponenter, ger användarna åtkomst till kontroller på låg nivå och anpassningsalternativ. Aks tillhandahåller däremot en nyckelfärdig Kubernetes-distribution som ger kunderna den gemensamma uppsättning konfigurationer och funktioner de behöver. AKS-kunder har begränsad anpassning, distribution och andra alternativ. Dessa kunder behöver inte oroa sig för eller hantera Kubernetes kluster direkt.

Med AKS får kunden ett helt hanterat *kontrollplan.* Kontrollplanet innehåller alla komponenter och tjänster som kunden behöver för att driva och tillhandahålla Kubernetes-kluster till slutanvändare. Alla Kubernetes-komponenter underhålls och drivs av Microsoft.

Microsoft hanterar och övervakar följande komponenter via kontrollfönstret:

* Kubelet- eller Kubernetes API-servrar
* Etcd eller ett kompatibelt nyckelvärdeslager som tillhandahåller kvalitet på tjänsterna (QoS), skalbarhet och körning
* DNS-tjänster (till exempel kube-dns eller CoreDNS)
* Kubernetes proxy eller nätverk

AKS är inte en helt hanterad klusterlösning. Vissa komponenter, till exempel arbetsnoder, har *delat ansvar*, där användarna måste hjälpa till att underhålla AKS-klustret. Användarindata krävs till exempel för att tillämpa en operativsystemet för arbetsnod (OS).

Tjänsterna *hanteras* i den meningen att Microsoft och AKS-teamet distribuerar, driver och ansvarar för tjänstens tillgänglighet och funktionalitet. Kunder kan inte ändra dessa hanterade komponenter. Microsoft begränsar anpassningen för att säkerställa en konsekvent och skalbar användarupplevelse. En helt anpassningsbar lösning finns i [AKS Engine](https://github.com/Azure/aks-engine).

> [!NOTE]
> AKS-arbetsnoder visas i Azure-portalen som vanliga Azure IaaS-resurser. Men dessa virtuella datorer distribueras till en anpassad Azure-resursgrupp (föregås av MC\\*). Det är möjligt att ändra AKS-arbetsnoder. Du kan till exempel använda Secure Shell (SSH) för att ändra AKS-arbetsnoder på det sätt du ändrar normala virtuella datorer (du kan dock inte ändra grundoperativsystemets avbildning och ändringar kanske inte kvarstår genom en uppdatering eller omstart) och du kan koppla andra Azure-resurser till AKS arbetsnoder. Men när du gör ändringar *av bandhantering och anpassning* kan AKS-klustret inte kunna stödjas. Undvik att ändra arbetsnoder om inte Microsoft Support uppmanar dig att göra ändringar.

Utfärdande åtgärder som inte stöds enligt definitionen ovan, till exempel out of band deallocation av alla agentnoder, gör klustret stöds inte. AKS förbehåller sig rätten att arkivera kontrollplan som har konfigurerats av supportriktlinjer under längre perioder som är lika med och längre än 30 dagar. AKS underhåller säkerhetskopior av kluster etcd metadata och kan enkelt omfördela klustret. Den här omfördelningen kan initieras av en PUT-åtgärd som ger klustret tillbaka till stöd, till exempel en uppgradering eller skala till aktiva agentnoder.

## <a name="shared-responsibility"></a>Delat ansvar

När ett kluster skapas definierar kunden de Kubernetes-arbetsnoder som AKS skapar. Kundarbetsbelastningar körs på dessa noder. Kunderna äger och kan visa eller ändra arbetsnoderna.

Eftersom kundklusternoder kör privat kod och lagrar känsliga data kan Microsoft Support komma åt dem på ett begränsat sätt. Microsoft Support kan inte logga in på, köra kommandon i eller visa loggar för dessa noder utan uttrycklig kundbehörighet eller hjälp.

Eftersom arbetsnoder är känsliga är Microsoft mycket noga med att begränsa sin bakgrundshantering. I många fall fortsätter arbetsbelastningen att köras även om Kubernetes-huvudnoderna, etcd och andra Microsoft-hanterade komponenter misslyckas. Slarvigt modifierade arbetsnoder kan orsaka förluster av data och arbetsbelastningar och kan göra klustret inte stödbart.

## <a name="aks-support-coverage"></a>AKS-supporttäckning

Microsoft tillhandahåller teknisk support för följande:

* Anslutning till alla Kubernetes-komponenter som Kubernetes-tjänsten tillhandahåller och stöder, till exempel API-servern.
* Hantering, drifttid, QoS och drift av Kubernetes styr plantjänster (Kubernetes master noder, API-server, etcd och kube-dns, till exempel).
* Etcd. Supporten omfattar automatiska, transparenta säkerhetskopior av alla etcd-data var 30:e minut för katastrofplanering och återställning av klustertillstånd. Dessa säkerhetskopior är inte direkt tillgängliga för kunder eller användare. De säkerställer datatillförlitlighet och konsekvens.
* Alla integrationspunkter i Azure-molnproviderdrivrutinen för Kubernetes. Dessa inkluderar integreringar i andra Azure-tjänster, till exempel belastningsutjämnare, beständiga volymer eller nätverk (Kubernetes och Azure CNI).
* Frågor eller problem om anpassning av styrplankomponenter som Kubernetes API-server, etcd och kube-dns.
* Problem med nätverk, till exempel Azure CNI, kubenet eller andra problem med nätverksåtkomst och funktioner. Problem kan omfatta DNS-lösning, paketförlust, routning och så vidare. Microsoft stöder olika nätverksscenarier:
  * Kubenet (grundläggande) och avancerade nätverk (Azure CNI) i klustret och tillhörande komponenter
  * Anslutning till andra Azure-tjänster och -program
  * Ingående styrenheter och ingående eller belastningsutjämnare konfigurationer
  * Nätverksprestanda och svarstid

Microsoft tillhandahåller inte teknisk support för följande:

* Frågor om hur du använder Kubernetes. Microsoft Support ger till exempel inga råd om hur du skapar anpassade ingressstyrenheter, använder programarbetsbelastningar eller tillämpar programpaket eller verktyg med öppen källkod eller öppen källkod.
  > [!NOTE]
  > Microsoft Support kan ge råd om AKS-klusterfunktioner, anpassning och justering (till exempel Problem och procedurer för Kubernetes-åtgärder).
* Projekt med öppen källkod från tredje part som inte tillhandahålls som en del av Kubernetes-kontrollplanet eller distribueras med AKS-kluster. Dessa projekt kan omfatta Istio, Helm, Sändebud eller andra.
  > [!NOTE]
  > Microsoft kan ge stöd för bästa möjliga support för projekt med öppen källkod från tredje part som Helm och Kured. Om verktyget med öppen källkod från tredje part integreras med Kubernetes Azure-molnleverantör eller andra AKS-specifika fel stöder Microsoft exempel och program från Microsoft-dokumentation.
* Programvara från tredje part. Denna programvara kan innehålla verktyg för säkerhetsskanning och nätverksenheter eller programvara.
* Problem om multicloud eller multivendor build-outs. Microsoft stöder till exempel inte problem relaterade till att köra en federerad multipublic cloud-leverantörslösning.
* Andra nätverksanpassningar än de som anges i [AKS-dokumentationen](https://docs.microsoft.com/azure/aks/).
  > [!NOTE]
  > Microsoft stöder problem och buggar relaterade till nätverkssäkerhetsgrupper (NSG). Microsoft Support kan till exempel svara på frågor om ett NSG-fel att uppdatera eller ett oväntat NSG- eller belastningsutjämnare.For example, Microsoft Support can answer questions about an NSG failure to update or an unexpected NSG or load balancer behavior.

## <a name="aks-support-coverage-for-worker-nodes"></a>AKS-stödtäckning för arbetsnoder

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Microsoft-ansvar för AKS-arbetsnoder

Microsoft och kunder delar ansvaret för Kubernetes arbetsnoder där:

* Basen OS-avbildningen har nödvändiga tillägg (t.ex. övervaknings- och nätverksagenter).
* Arbetsnoderna tar emot OS-patchar automatiskt.
* Problem med Kubernetes-styrplankomponenter som körs på arbetsnoderna åtgärdas automatiskt. Komponenterna omfattar följande:
  * Kube-proxy
  * Nätverkstunnlar som tillhandahåller kommunikationsvägar till Kubernetes huvudkomponenter
  * Kubelet
  * Docker eller Moby demon

> [!NOTE]
> Om en kontrollplankomponent inte är i drift på en arbetsnod kan AKS-teamet behöva starta om enskilda komponenter eller hela arbetarnoden. Dessa omstartsåtgärder är automatiserade och ger automatisk reparation för vanliga problem. Dessa omstarter sker endast på _nodnivå_ och inte klustret om det inte finns ett nödunderhåll eller avbrott.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Kundansvar för AKS-arbetsnoder

Microsoft startar inte automatiskt om arbetsnoder för att använda patchar på OS-nivå. Även om OS-korrigeringar levereras till arbetsnoderna är *kunden* ansvarig för att starta om arbetsnoderna för att tillämpa ändringarna. Delade bibliotek, demoner som SSHD (Solid State Hybrid Drive) och andra komponenter på systemnivå eller operativsystem korrigeras automatiskt.

Kunderna ansvarar för att utföra Kubernetes uppgraderingar. De kan köra uppgraderingar via Azure-kontrollpanelen eller Azure CLI. Detta gäller för uppdateringar som innehåller förbättringar av säkerhet eller funktioner i Kubernetes.

> [!NOTE]
> Eftersom AKS är en *hanterad tjänst*är slutmålen att ta bort ansvaret för korrigeringar, uppdateringar och loggsamling för att göra tjänsthanteringen mer komplett och hands-off. När tjänstens kapacitet för hantering från till på sluttid ökar kan framtida versioner utelämna vissa funktioner (till exempel nodomstart och automatisk korrigering).

### <a name="security-issues-and-patching"></a>Säkerhetsproblem och korrigering

Om ett säkerhetsfel finns i en eller flera komponenter i AKS kommer AKS-teamet att korrigera alla berörda kluster för att minska problemet. Alternativt kommer teamet att ge användarna uppgraderingsvägledning.

För arbetsnoder som ett säkerhetsfel påverkar, om en noll-driftstopp patch är tillgänglig, kommer AKS-teamet att tillämpa den korrigeringsfilen och meddela användarna om ändringen.

När en säkerhetskorrigering kräver omstart av arbetsnoden meddelar Microsoft kunderna om detta krav. Kunden är ansvarig för omstart eller uppdatering för att hämta klusterkorrigeringen. Om användarna inte tillämpar korrigeringsfilerna enligt AKS-vägledningen fortsätter deras kluster att vara sårbart för säkerhetsproblemet.

### <a name="node-maintenance-and-access"></a>Underhåll och åtkomst för nod

Arbetsnoder är ett delat ansvar och ägs av kunder. På grund av detta har kunderna möjlighet att logga in på sina arbetsnoder och göra potentiellt skadliga ändringar som kernel-uppdateringar och installera eller ta bort paket.

Om kunder gör destruktiva ändringar eller orsakar att kontrollplankomponenter kopplas från eller blir icke-funktionella, identifierar AKS detta fel och återställer automatiskt arbetsnoden till föregående arbetstillstånd.

Även om kunder kan logga in på och ändra arbetsnoder, är det inte möjligt att göra detta eftersom ändringar kan göra ett kluster som inte kan stödjas.

## <a name="network-ports-access-and-nsgs"></a>Nätverksportar, åtkomst och NSG:er

Som en hanterad tjänst har AKS specifika nätverks- och anslutningskrav. Dessa krav är mindre flexibla än kraven för normala IaaS-komponenter. I AKS kan åtgärder som att anpassa NSG-regler, blockera en viss port (till exempel med hjälp av brandväggsregler som blockerar utgående port 443) och vitlistning av webbadresser göra klustret inte stödbart.

> [!NOTE]
> För närvarande tillåter AKS inte att du helt låser utgående trafik från klustret. Om du vill styra listan över webbadresser och portar som klustret kan använda för utgående trafik finns [begränsad utgående trafik](limit-egress-traffic.md).

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Funktioner för alfa- och betakubernät som inte stöds

AKS stöder endast stabila funktioner i projektet uppströms Kubernetes. Om inget annat dokumenteras stöder AKS inte alfa- och betafunktioner som är tillgängliga i projektet uppströms Kubernetes.

I två scenarier kan alfa- eller betafunktioner distribueras innan de är allmänt tillgängliga:

* Kunderna har träffat AKS-produkt-, support- eller ingenjörsteam och har ombetts att prova dessa nya funktioner.
* Dessa funktioner har [aktiverats av en funktionsflagga](https://github.com/Azure/AKS/blob/master/previews.md). Kunder måste uttryckligen välja att använda dessa funktioner.

## <a name="preview-features-or-feature-flags"></a>Förhandsgranska funktioner eller funktionsflaggor

För funktioner som kräver utökad testning och feedback från användare släpper Microsoft nya förhandsgranskningsfunktioner eller funktioner bakom en funktionsflagga. Betrakta dessa funktioner som förhandsversioner eller betafunktioner.

Förhandsgranskningsfunktioner eller funktioner med funktionsflagga är inte avsedda för produktion. Pågående ändringar i API:er och beteende, buggfixar och andra ändringar kan resultera i instabila kluster och driftstopp.

Funktioner i offentlig förhandsversion omfattas av "best effort"-stöd eftersom dessa funktioner är i förhandsversion och inte avsedda för produktion och stöds av AKS tekniska supportteam endast under kontorstid. För ytterligare information se:

* [Vanliga frågor och svar om Azure-support](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> Förhandsversionsfunktioner börjar gälla på *Azure-prenumerationsnivå.* Installera inte förhandsversionsfunktioner på en produktionsprenumeration. På en produktionsprenumeration kan förhandsversionsfunktioner ändra standard-API-beteende och påverka vanliga åtgärder.

## <a name="upstream-bugs-and-issues"></a>Uppströms buggar och problem

Med tanke på den snabba utvecklingen i projektet uppströms Kubernetes uppstår alltid buggar. Några av dessa buggar kan inte korrigeras eller bearbetas runt inom AKS-systemet. I stället kräver buggfixar större korrigeringar för uppströmsprojekt (till exempel Kubernetes, nod eller operativsystem för arbetare och kärnor). För komponenter som Microsoft äger (till exempel Azure-molnleverantören) har AKS och Azure-personal åtagit sig att åtgärda problem uppströms i communityn.

När ett problem med teknisk support orsakas av en eller flera uppströmsfel kommer AKS support- och ingenjörsteam att:

* Identifiera och länka uppströmsfelen med eventuella supportinformationer för att förklara varför det här problemet påverkar klustret eller arbetsbelastningen. Kunderna får länkar till de nödvändiga databaserna så att de kan titta på problemen och se när en ny version ger korrigeringar.
* Tillhandahålla potentiella lösningar eller mildrande åtgärder. Om problemet kan minskas kommer ett [känt problem](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) att arkiveras i AKS-databasen. Den kända frågan arkivering förklarar:
  * Problemet, inklusive länkar till uppströms buggar.
  * Lösningen och information om en uppgradering eller en annan varaktighet av lösningen.
  * Grova tidslinjer för att ta med frågan, baserat på uppströmsutgivningskaden.
