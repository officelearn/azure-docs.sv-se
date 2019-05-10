---
title: Stöd för principer för Azure Kubernetes Service (AKS)
description: Läs mer om Azure Kubernetes Service (AKS) supportprinciper delat ansvar och funktioner som är i förhandsversion (eller alpha eller beta).
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: 9b779021eca11638e8ee52ec11d082e5b0e89cd4
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506689"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Stöd för principer för Azure Kubernetes Service

Den här artikeln innehåller information om teknisk supportprinciper och begränsningar för Azure Kubernetes Service (AKS). Artikeln beskriver också hantering av worker, hanterade kontrollen plan komponenter, från tredje part komponenter med öppen källkod och säkerhets- eller patch-hantering.

## <a name="service-updates-and-releases"></a>Tjänstuppdateringar och versioner

* Viktig information finns i [viktig information om AKS](https://github.com/Azure/AKS/releases).
* Information om funktioner i förhandsversion, finns i [AKS förhandsgranskning funktioner och relaterade projekt](https://github.com/Azure/AKS/blob/master/previews.md).

## <a name="managed-features-in-aks"></a>Hanterade funktioner i AKS

Grundläggande infrastruktur som en tjänst (IaaS) cloud-komponenter, till exempel beräkning eller nätverkskomponenter, ger användare åtkomst till på låg nivå kontroller och anpassningsalternativ. Däremot ger AKS en nyckelfärdig Kubernetes-distribution som ger kunderna den gemensamma uppsättningen konfigurationer och funktioner som de behöver. AKS-kunder har begränsad anpassning, distribution och andra alternativ. Dessa kunder behöver inte bekymra dig om eller hantera Kubernetes-kluster direkt.

Med AKS, kommer kunden en fullständigt hanterad *kontrollplanet*. Kontrollplanet innehåller alla komponenter och tjänster som kunden måste köra och tillhandahålla Kubernetes-kluster till slutanvändare. Alla komponenter i Kubernetes bibehålls och hanteras av Microsoft.

Microsoft hanterar och övervakar följande komponenter via control-fönstret:

* Kubelet eller Kubernetes API-servrar
* Etcd eller en kompatibel nyckel / värdelagring, ger tjänstkvalitet (QoS), skalbarhet och runtime
* DNS-tjänster (till exempel kube-dns eller CoreDNS)
* Kubernetes-proxy eller nätverk

AKS är inte en helt hanterad lösning. Vissa komponenter, till exempel arbetsnoder, har *ansvar som delas*, där användare måste hjälpa dig att upprätthålla AKS-klustret. Indata från användaren krävs till exempel att tillämpa en säkerhetskorrigering för worker noden operativsystem (OS).

Tjänsterna är *hanteras* i den mening att Microsoft och AKS-teamet distribuerar fungerar och är ansvarig för tjänstens tillgänglighet och funktioner. Kunder kan inte ändra dessa hanterade komponenter. Microsoft begränsar anpassning för att säkerställa en konsekvent och skalbar användarupplevelse. En helt anpassningsbar lösning Se [AKS-motorn](https://github.com/Azure/aks-engine).

> [!NOTE]
> AKS arbetsnoder visas i Azure-portalen som vanliga Azure IaaS-resurser. Men de virtuella datorerna distribueras i en anpassad Azure-resursgrupp (föregås av prefixet MC\\*). Det är möjligt att ändra AKS arbetsnoder. Du kan till exempel använda Secure Shell (SSH) och ändra AKS arbetsnoder hur du ändrar den normala virtuella datorer (du kan dock ändra den grundläggande OS-avbildningen och ändringar kan inte sparas via en uppdatering eller starta om) och du kan koppla andra Azure-resurser till AKS arbetsnoder. Men när du gör ändringar *från band-hantering och anpassning,* AKS-klustret kan bli unsupportable. Undvik att ändra arbetsnoder såvida inte Microsoft Support dirigerar du kan göra ändringar.

## <a name="shared-responsibility"></a>Delat ansvar

När ett kluster skapas, definierar kunden worker Kubernetes-noderna som skapar AKS. Kundens arbetsbelastningar körs på noderna. Kunder som kan äger och visa eller ändra arbetsnoderna.

Eftersom kunden klusternoderna köra privata kod och lagra känsliga data, Microsoft Support kan komma åt dem på bara ett begränsat sätt. Microsoft Support kan inte logga in på, kör kommandon i eller visa loggar för dessa noder utan uttrycklig kunden behörighet eller behöver hjälp.

Eftersom arbetsnoder är känsliga tar Microsoft mycket försiktig så att begränsa hanteringen bakgrund. Din arbetsbelastning i många fall kommer att fortsätta att köras även om Kubernetes master-noder, etcd och andra Microsoft-hanterade komponenter misslyckas. Vårdslöst ändrade arbetsnoder kan orsaka förlust av data och arbetsbelastningar och kan återge klustret unsupportable.

## <a name="aks-support-coverage"></a>AKS supporttäckning

Microsoft tillhandahåller teknisk support för följande:

* Anslutning till alla Kubernetes-komponenter som Kubernetes-tjänst ger och har stöd för, till exempel API-servern.
* Hantering, drifttid, QoS och driften av Kubernetes styra plan tjänster (överordnade Kubernetes-noder, API-servern, etcd och kube-dns, till exempel).
* Etcd. Support omfattar automatiserad, transparent säkerhetskopieringar av alla etcd data varje halvtimme för katastrofåterställning planerings- och klustret återställningen av användartillstånd. Dessa säkerhetskopior är inte direkt tillgänglig för kunder eller användare. De säkerställer att datatillförlitlighet och konsekvens.
* Alla integreringspunkter i Azure-molnet provider-drivrutinen för Kubernetes. Dessa inkluderar integreringar till andra Azure-tjänster, till exempel belastningsutjämnare, beständiga volymer eller nätverk (Kubernetes och Azure CNI).
* Dataplaner komponenter som Kubernetes API-servern, etcd och kube-DNS-frågor eller problem om anpassning av kontrollen.
* Problem om nätverk, till exempel Azure CNI, kubenet, eller andra nätverksåtkomst och funktioner problem. Problem kan omfatta DNS-matchning, paketförlust, Routning och så vidare. Microsoft stöder olika scenarier för nätverk:
  * Kubenet (grundläggande) och avancerade nätverk (Azure CNI) i klustret och tillhörande komponenter
  * Anslutning till andra Azure-tjänster och program
  * Ingress-styrenheter och konfigurationer för inkommande eller load balancer
  * Nätverkets prestanda och svarstider

Microsoft ger inte teknisk support för följande:

* Frågor om hur du använder Kubernetes. Till exempel tillhandahåller Microsoft Support inte råd om hur du skapar anpassade ingress-styrenheter, Använd programbelastningar eller tillämpa från tredje part eller öppen källkod programvarupaket eller verktyg.
  > [!NOTE]
  > Microsoft Support kan ge råd AKS klusterfunktionaliteten, anpassning och justera (till exempel, serverproblem med Kubernetes och procedurer).
* Projekt med öppen källkod från tredje part som inte tillhandahålls som en del av Kubernetes styra plan eller distribueras med AKS-kluster. Dessa projekt kan innehålla Istio, Helm, Envoy eller andra.
  > [!NOTE]
  > Microsoft kan tillhandahålla bästa support för tredjeparts-projekt för öppen källkod, till exempel Helm och Kured. Om öppen källkod-verktyg från tredje part som integrerar med molnleverantören Kubernetes Azure eller andra AKS-specifika buggar, stöder Microsoft exempel och program från Microsoft-dokumentationen.
* Stängd källkod programvara från tredje part. Den här programvaran kan innehålla genomsökningsverktyg för säkerhet och nätverk enheter eller program.
* Problem om multicloud eller från flera leverantörer build-detaljer. Microsoft stöder till exempel inte problem med att köra en federerad multipublic leverantör molnlösning.
* Network anpassningar än de som anges i den [dokumentationen om AKS](https://docs.microsoft.com/azure/aks/).
  > [!NOTE]
  > Microsoft stöder problem och buggar som rör nätverkssäkerhetsgrupper (NSG). Microsoft Support kan till exempel besvara frågor om en NSG misslyckande med att uppdatera eller ett oväntat beteende för NSG eller load balancer.

## <a name="aks-support-coverage-for-worker-nodes"></a>AKS supporttäckning för arbetsnoder

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Microsofts ansvar för AKS-arbetsnoder

Microsoft och kunder delar ansvar för Kubernetes arbetsnoder där:

* Avbildningen har begärt tillägg (till exempel övervakning och nätverk agenter).
* Arbetsnoderna får operativsystemskorrigeringar automatiskt.
* Problem med Kubernetes styra plan komponenter som körs på arbetsnoderna åtgärdas automatiskt. Följande: komponenter
  * Kube-proxy
  * nätverk tunnlar som tillhandahåller kommunikationsvägar till Kubernetes master-komponenter
  * kubelet
  * Daemon för docker eller Moby

> [!NOTE]
> På en underordnad nod om en kontroll plan komponent inte är i drift, kan AKS-teamet behöva starta om hela arbetsnoden. På grund av deras begränsad åtkomst till kundens active arbetsbelastning och data startar AKS-teamet om en arbetsnod bara om kunden eskalerar problemet. Om möjligt arbetar AKS-teamet för att förhindra att en obligatorisk omstart påverkar programmet.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Kundens ansvarsområden för AKS-arbetsnoder

Microsoft inte automatiskt starta om arbetsnoder för att tillämpa korrigeringar för OS-nivå. Även om operativsystemskorrigeringar levereras till arbetsnoder, den *kunden* ansvarar för att starta om arbetsnoder för att tillämpa ändringarna. Delade bibliotek, daemons, till exempel SSD hybrid enhet (SSHD) och andra komponenter på nivån för det eller de OS korrigerade automatiskt.

Kunderna ansvarar för att köra Kubernetes uppgraderingar. De kan köra uppgraderingar via Kontrollpanelen i Azure eller Azure CLI. Detta gäller för uppdateringar som innehåller säkerhets- eller funktioner förbättringar av Kubernetes.

> [!NOTE]
> Eftersom AKS är en *hanterad tjänst*, dess slutpunkt mål omfattar borttagning av ansvar för korrigeringar, uppdateringar, och logga samling så att Tjänstehantering mer fullständig och laserpekaren. När tjänstens kapacitet för slutpunkt till slutpunkt management ökar kan kommande versioner utelämna vissa funktioner (till exempel omstart av noden och automatisk korrigering).

### <a name="security-issues-and-patching"></a>Uppdatering och säkerhetsproblem

Om en säker hittas i en eller flera komponenter i AKS kommer AKS-teamet korrigera alla berörda kluster för att åtgärda problemet. Du kan också får teamet användare uppgradera vägledningen.

För arbetarnoder att det finns en säkerhet fel påverkar, om en patch nedtid, AKS-teamet att uppdatera och meddela användare om ändringen.

När en säkerhetskorrigering kräver worker noden omstarter, meddelar Microsoft kunderna av det här kravet. Kunden ansvarar för att starta om eller uppdatera för att hämta kluster-korrigering. Om användarna inte tillämpa korrigeringar enligt AKS vägledning, fortsätter deras kluster sårbara för ett säkerhetsproblem.

### <a name="node-maintenance-and-access"></a>Noden underhåll och åtkomst

Arbetsnoderna är ett delat ansvar och ägs av kunder. Därför möjlighet kunder att logga in på sina arbetsnoder och göra potentiellt skadliga ändringar som kernel-uppdateringar och installera eller ta bort paket.

Om kunder har ändrat destruktiva eller orsaka att kontrollen plan delar frånkopplas eller bli icke-funktionella, AKS identifierar det här felet och återställa arbetsnoden automatiskt till det tidigare fungerande tillståndet.

Även om kunder kan logga in på och ändra arbetsnoder, rekommenderas detta inte eftersom ändringar kan göra ett kluster unsupportable.

## <a name="network-ports-access-and-nsgs"></a>Nätverksportar, åtkomst och NSG: er

Som en hanterad tjänst har AKS särskilda krav för nätverk och anslutning. Dessa krav är mindre flexibelt än att kraven för normal IaaS-komponenter. Åtgärder som anpassar NSG-regler som blockerar en viss port (till exempel med hjälp av brandväggsregler som blockerar utgående port 443) i AKS, och listan över tillåtna URL: er kan göra ditt kluster unsupportable.

> [!NOTE]
> För närvarande kan inte AKS du helt låsa utgående trafik från ditt kluster (till exempel explicit domän eller port listan över tillåtna program). Lista över webbadresser och portar kan ändras utan varning. Du kan hämta den uppdaterade listan genom att skapa ett Azure-supportärende. Listan är endast avsedd för kunder som är beredd att acceptera att deras klustret kan påverkas *när som helst.*

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Funktioner som inte stöds alpha- och betaversioner Kubernetes

AKS stöder endast stabila funktioner i det överordnade Kubernetes-projektet. Om inte annat dokumenterade stöder inte AKS alpha- och betaversioner funktioner som är tillgängliga i det överordnade Kubernetes-projektet.

I två situationer kan alpha eller beta-funktioner lanseras innan de är allmänt tillgängliga:

* Kunderna har uppfyllt med AKS produkten, support eller teknikteam och har blivit ombedd att testa de här nya funktionerna.
* De här funktionerna har [aktiverad som en funktionsflagga](https://github.com/Azure/AKS/blob/master/previews.md). Kunder måste uttryckligen väljer för att använda dessa funktioner.

## <a name="preview-features-or-feature-flags"></a>Funktioner i förhandsversion eller flaggor för funktionen

Microsoft släpper nya förhandsvisningsfunktioner eller funktioner bakom en funktionsflagga för funktioner och funktioner som kräver utökade testning och användarfeedback. Överväg att dessa funktioner som förhandsversion eller beta-funktioner.

Funktioner i förhandsversion eller funktionsflagga funktioner är inte avsedda för produktion. Pågående ändringar i API: er och beteende, felkorrigeringar och andra ändringar kan resultera i instabil kluster och driftstopp.

Funktioner i offentlig förhandsversion är faller under ”bästa prestanda” support som de här funktionerna finns i en förhandsversion och inte avsedd för produktion och stöds av team för AKS-teknisk support endast under kontorstid. Mer information finns:

* [Vanliga frågor om Azure-Support](https://azure.microsoft.com/en-us/support/faq/)

> [!NOTE]
> Förhandsversionsfunktioner gälla när Azure *prenumeration* nivå. Installera inte förhandsversionsfunktioner på en prenumeration för produktion. På en prenumeration för produktion förhandsversionsfunktioner ändra standard-API och påverka normal drift.

## <a name="upstream-bugs-and-issues"></a>Överordnad buggar och problem

Beroende på utveckling hastighet i överordnade Kubernetes-projektet uppstår buggar utan undantag. Några av de här felen kan inte korrigeras eller arbetat runt i AKS-systemet. I stället kräver felkorrigeringar större korrigeringar till överordnade projekt (som Kubernetes, noden eller worker-operativsystem och kernlar). För komponenter som Microsoft äger (till exempel Azure-molnet-provider), är AKS och Azure personal allt för att åtgärda problem uppströms i communityn.

När ett problem för teknisk support är roten orsakas av en eller flera överordnade buggar, kommer AKS support och engineering team:

* Identifiera och länka de överordnade buggarna med någon stödjande information för att förklara varför det här problemet påverkar ditt kluster eller en arbetsbelastning. Kunder får länkar till de nödvändiga lagringsplatserna så att de kan se problem och se när en ny version ger korrigeringar.
* Ange eventuella lösningar eller åtgärder. Om problemet kan undvikas, en [känt problem](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) kommer att fyllas i AKS-databasen. Kända problem Momsregistrering beskriver:
  * Problemet, däribland länkar till överordnad buggar.
  * Lösning och information om en uppgradering eller någon annan persistence av lösningen.
  * Omild tidslinjer den problemet ska ingå, baserat på den överordnade frisläppningstakt.
