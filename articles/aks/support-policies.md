---
title: Support principer för Azure Kubernetes service (AKS)
description: Lär dig mer om Azure Kubernetes service (AKS) support principer, delade ansvar och funktioner i för hands versionen (eller alpha eller beta).
services: container-service
author: jnoller
ms.topic: article
ms.date: 01/24/2020
ms.author: jenoller
ms.openlocfilehash: 775992d090b951c8de6fce36377dc91d6e017399
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85558083"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Support principer för Azure Kubernetes-tjänsten

Den här artikeln innehåller information om tekniska support principer och begränsningar för Azure Kubernetes service (AKS). Artikeln innehåller också information om hantering av arbetsnoder, hanterade kontroll Plans komponenter, komponenter från tredje part med öppen källkod och säkerhets-eller uppdaterings hantering.

## <a name="service-updates-and-releases"></a>Service uppdateringar och versioner

* Versions information finns i [AKS-versions anteckningar](https://github.com/Azure/AKS/releases).
* Information om funktioner i för hands version finns i [AKS Preview-funktioner och relaterade projekt](https://github.com/Azure/AKS/blob/master/previews.md).

## <a name="managed-features-in-aks"></a>Hanterade funktioner i AKS

Grundläggande infrastruktur komponenter för infrastruktur som en tjänst (IaaS), till exempel Compute-eller nätverks komponenter, ger användarna åtkomst till lågnivå kontroller och anpassnings alternativ. Som kontrast tillhandahåller AKS en nyckel färdig Kubernetes-distribution som ger kunderna en gemensam uppsättning konfigurationer och funktioner som de behöver. AKS-kunder har begränsad anpassning, distribution och andra alternativ. Dessa kunder behöver inte bekymra sig om eller hantera Kubernetes-kluster direkt.

Med AKS får kunden ett fullständigt hanterat *kontroll plan*. Kontroll planet innehåller alla komponenter och tjänster som kunden behöver för att kunna hantera och tillhandahålla Kubernetes-kluster till slutanvändare. Alla Kubernetes-komponenter upprätthålls och drivs av Microsoft.

Microsoft hanterar och övervakar följande komponenter i kontroll fönstret:

* Kubelet-eller Kubernetes-API-servrar
* Etcd eller ett kompatibelt nyckel värdes lager som tillhandahåller QoS (Quality of Service), skalbarhet och körning
* DNS-tjänster (till exempel Kube-DNS eller CoreDNS)
* Kubernetes-proxy eller nätverk

AKS är inte en fullständigt hanterad kluster lösning. Vissa komponenter, till exempel arbetsnoder, har *delat ansvar*, där användarna måste underhålla AKS-klustret. Användarindata krävs, t. ex. för att tillämpa en säkerhets korrigering för en säkerhets korrigering av en arbetsnods operativ system (OS).

Tjänsterna *hanteras* i den mening att Microsoft och AKS-teamet distribuerar, agerar och ansvarar för tjänstens tillgänglighet och funktioner. Kunder kan inte ändra dessa hanterade komponenter. Microsoft begränsar anpassningen för att säkerställa en konsekvent och skalbar användar upplevelse. En helt anpassningsbar lösning finns i [AKS-motorn](https://github.com/Azure/aks-engine).

## <a name="shared-responsibility"></a>Delat ansvar

När ett kluster skapas definierar kunden de Kubernetes Worker-noder som AKS skapar. Kund arbets belastningar körs på dessa noder. Kunder som äger och kan visa eller ändra arbetsnoder.

Eftersom kundklusternoder kör privat kod och lagrar känsliga data kan Microsoft Support komma åt dem på ett begränsat sätt. Microsoft Support kan inte logga in på, köra kommandon i eller Visa loggar för dessa noder utan uttrycklig kund behörighet eller hjälp.

Eftersom arbetsnoder är känsliga, tar Microsoft utmärkt att begränsa sin bakgrunds hantering. I många fall kommer arbets belastningen fortsätta att köras även om Kubernetes-huvudnoderna, etcd och andra Microsoft-hanterade komponenter Miss fungerar. Carelessly ändrade arbetsnoder kan orsaka förlust av data och arbets belastningar och kan rendera klustret kan inte användas.

## <a name="aks-support-coverage"></a>Support täckning för AKS

Microsoft tillhandahåller teknisk support för följande:

> [!NOTE]
> Alla kluster åtgärder som vidtas av Microsoft Support görs med användar medgivande under en inbyggd Kubernetes ["Edit"](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) -roll för namnet `aks-support-rolebinding` . Med den här rollen AKS-support aktive rad kan du redigera kluster konfiguration och resurser för att felsöka och diagnostisera kluster problem, men rollen kan inte ändra behörigheter eller skapa roller eller roll bindningar. Roll åtkomst är bara aktiverat under aktiva support biljetter med JIT-åtkomst (just-in-Time).

* Anslutning till alla Kubernetes-komponenter som Kubernetes-tjänsten tillhandahåller och stöder, t. ex. API-servern.
* Hantering, drift tid, QoS och drift av Kubernetes Control plan-tjänster (Kubernetes Master Nodes, API-Server, etcd och Kube-DNS, till exempel).
* Etcd. Support omfattar automatiserade, transparenta säkerhets kopieringar av alla etcd-data var 30: e minut för katastrof planering och återställning av kluster tillstånd. Dessa säkerhets kopior är inte direkt tillgängliga för kunder eller användare. De säkerställer data tillförlitlighet och konsekvens. Etcd. återställning på begäran eller återställning stöds inte som en funktion.
* Alla integrerings platser i Azure Cloud Provider-drivrutinen för Kubernetes. Detta inkluderar integreringar i andra Azure-tjänster, till exempel belastningsutjämnare, beständiga volymer eller nätverk (Kubernetes och Azure CNI).
* Frågor eller problem med anpassning av kontroll Plans komponenter som Kubernetes API-Server, etcd och Kube-DNS.
* Problem med nätverk, till exempel Azure CNI, Kubernetes eller andra problem med nätverks åtkomst och funktioner. Problem kan vara DNS-matchning, paket förlust, Routning och så vidare. Microsoft har stöd för olika nätverks scenarier:
  * Kubernetes (Basic) och Advanced Networking (Azure CNI) i klustret och associerade komponenter
  * Anslutning till andra Azure-tjänster och-program
  * Ingångs-och ingångs-eller belastnings Utjämnings konfigurationer
  * Nätverks prestanda och svars tid

Microsoft tillhandahåller inte teknisk support för följande:

* Frågor om hur du använder Kubernetes. Microsoft Support ger dig till exempel inte råd om hur du skapar anpassade ingångs styrenheter, använder program arbets belastningar eller använder program varu paket från tredje part eller öppen källkod eller verktyg.
  > [!NOTE]
  > Microsoft Support kan informera om AKS kluster funktioner, anpassning och justering (till exempel problem med Kubernetes-åtgärder och procedurer).
* Projekt med öppen källkod från tredje part som inte tillhandahålls som en del av Kubernetes-kontroll planet eller distribueras med AKS-kluster. Dessa projekt kan innehålla Istio, Helm, mottagare eller andra.
  > [!NOTE]
  > Microsoft kan tillhandahålla bästa möjliga support för projekt med öppen källkod från tredje part, till exempel Helm och Kured. Om verktyget med öppen källkod integrerar med Kubernetes Azure-molnet eller andra AKS buggar, stöder Microsoft exempel och program från Microsoft-dokumentationen.
* Program vara från sluten program vara från tredje part. Den här program varan kan innehålla verktyg för säkerhets genomsökning och nätverks enheter eller program.
* Problem om multimoln eller multileverantörs-build-timeout. Microsoft stöder till exempel inte problem som rör körning av en federerad lösning för en federerad multipublik moln leverantör.
* Andra nätverks anpassningar än de som anges i [AKS-dokumentationen](https://docs.microsoft.com/azure/aks/).
  > [!NOTE]
  > Microsoft stöder problem och buggar som är relaterade till nätverks säkerhets grupper (NSG: er). Microsoft Support kan till exempel svara på frågor om ett NSG-fel för att uppdatera eller en oväntad NSG eller belastnings Utjämnings funktion.

## <a name="aks-support-coverage-for-worker-nodes"></a>Stöd för AKS-täckning för arbetsnoder

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Microsoft-ansvar för AKS Worker-noder

Microsoft och våra kunder delar ansvar för Kubernetes arbetsnoder där:

* Bas operativ system avbildningen har nödvändiga tillägg (till exempel övervaknings-och nätverks agenter).
* Arbetsnoderna får OS-uppdateringar automatiskt.
* Problem med de Kubernetes Control plan-komponenter som körs på arbetsnoderna åtgärdas automatiskt. Komponenterna innehåller följande:
  * Kube-proxy
  * Nätverks tunnlar som tillhandahåller kommunikations vägar till Kubernetes huvud komponenter
  * Kubelet
  * Docker eller Moby daemon

> [!NOTE]
> Om en kontroll Plans komponent inte fungerar på en arbetsnoden kan AKS-teamet behöva starta om enskilda komponenter eller hela arbetsnoden. De här omstarts åtgärderna automatiseras och tillhandahåller automatisk reparation av vanliga problem. Dessa omstarter sker bara på _nodnivå_ och inte i klustret om det inte finns något underhåll eller avbrott i nödfall.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Kund ansvar för AKS Worker-noder

Microsoft startar inte automatiskt om arbetsnoder för att tillämpa uppdateringar på operativ system nivå. Även om OS-korrigeringsfiler levereras till arbetsnoderna ansvarar *kunden* för att starta om arbetsnoderna för att tillämpa ändringarna. Delade bibliotek, bakgrunds program, till exempel SSHD (solid-state hybrid Drive) och andra komponenter på nivån för systemet eller operativ systemet, korrigeras automatiskt.

Kunderna ansvarar för att köra Kubernetes-uppgraderingar. De kan köra uppgraderingar via Azures kontroll panel eller Azure CLI. Detta gäller för uppdateringar som innehåller förbättringar av säkerhet eller funktioner i Kubernetes.

#### <a name="user-customization-of-worker-nodes"></a>Användar anpassning av arbetsnoder
> [!NOTE]
> AKS Worker-noder visas i Azure Portal som vanliga Azure IaaS-resurser. Men de här virtuella datorerna distribueras till en anpassad Azure-resurs grupp (föregås av MC \\ *). Det går att utöka AKS Worker-noder från sina Grundkonfigurationer. Du kan till exempel använda SSH (Secure Shell) för att ändra AKS Worker-noder på samma sätt som du ändrar normala virtuella datorer. Du kan dock inte ändra bas operativ system avbildningen. Eventuella anpassade ändringar kanske inte behålls genom en uppgradering, skalning, uppdatering eller omstart. Att göra ändringar *utanför band och utanför omfånget för AKS-API: n* leder **dock**till att AKS-klustret inte stöds. Undvik att ändra arbetsnoder om inte Microsoft Support dirigerar dig om att göra ändringar.

Om du skickar åtgärder som inte stöds enligt definitionen ovan, till exempel out-of-band-deallokering av alla agent-noder, återges inte klustret. AKS förbehåller sig rätten att arkivera kontroll plan som har kon figurer ATS ur support rikt linjerna för utökade perioder som är lika med och bortom 30 dagar. AKS upprätthåller säkerhets kopiering av etcd metadata i kluster och kan enkelt allokera om klustret. Den här Omallokeringen kan initieras av en pågående åtgärd som tar klustret tillbaka till support, till exempel en uppgradering eller skalning till aktiva agent-noder.

AKS hanterar livs cykeln och åtgärder för arbetsnoder på uppdrag av kunder – det **går inte**att ändra de IaaS-resurser som är associerade med arbetsnoderna. Ett exempel på en åtgärd som inte stöds är att anpassa VM Scale set för en resurspool genom att manuellt ändra konfigurationer på VMSS via VMSS-portalen eller VMSS API.
 
AKS rekommenderar att du använder [Kubernetes daemonsets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)för arbets belastnings bara konfigurationer eller paket.

Med hjälp av Kubernetes Privileged daemonsets och init containers kan kunder finjustera och ändra eller installera program vara från tredje part på klustrets arbetsnoder. Exempel på sådana anpassningar är att lägga till anpassade program för säkerhets genomsökning eller uppdatera sysctl-inställningar.

Även om detta är en rekommenderad sökväg, om ovanstående krav är uppfyllda, kan AKS Engineering and support inte hjälpa till med fel sökning eller diagnostisering av brutna/icke-funktionella ändringar eller sådana som gör att noden inte är tillgänglig på grund av en kund distribuerad daemonset.

> [!NOTE]
> AKS som en *hanterad tjänst* har slut mål, till exempel borttagning av ansvar för korrigeringar, uppdateringar och logg insamling för att göra tjänst hanteringen mer komplett och praktisk. När tjänstens kapacitet för slut punkt till slut punkt ökar kan framtida versioner utesluta vissa funktioner (till exempel omstart av nod och automatisk uppdatering).

### <a name="security-issues-and-patching"></a>Säkerhets problem och uppdatering

Om det finns ett säkerhets fel i en eller flera komponenter i AKS, kommer AKS-teamet att uppdatera alla berörda kluster för att undvika problemet. Alternativt ger teamet användarna uppgraderings vägledning.

För arbetsnoder som ett säkerhets fel påverkar, om en uppdatering utan avbrott är tillgänglig, kommer AKS-teamet att tillämpa korrigeringen och meddela användare om ändringen.

När en säkerhets korrigering kräver omstarter av arbetsnoden meddelar Microsoft kunderna om detta krav. Kunden ansvarar för att starta om eller uppdatera för att hämta kluster korrigeringen. Om användarna inte tillämpar korrigeringarna enligt AKS-vägledningen fortsätter deras kluster att vara sårbara för säkerhets problemet.

### <a name="node-maintenance-and-access"></a>Underhåll och åtkomst till nod

Arbetsnoder är ett delat ansvar och ägs av kunder. Därför har kunderna möjlighet att logga in på sina arbetsnoder och göra potentiellt skadliga ändringar, till exempel kernel-uppdateringar och installera eller ta bort paket.

Om kunderna gör destruktiva ändringar eller gör att kontroll Plans komponenter går offline eller inte fungerar, kommer AKS att upptäcka detta fel och automatiskt återställa arbetsnoden till föregående fungerande tillstånd.

Även om kunder kan logga in på och ändra arbetsnoder, så kommer detta inte att undvikas eftersom ändringar kan göra att ett kluster inte stöds.

## <a name="network-ports-access-and-nsgs"></a>Nätverks portar, åtkomst och NSG: er

Som en hanterad tjänst har AKS särskilda nätverks-och anslutnings krav. Dessa krav är mindre flexibla än kraven för normala IaaS-komponenter. I AKS, åtgärder som att anpassa NSG-regler, blockera en speciell port (till exempel genom att använda brand Väggs regler som blockerar utgående port 443) och vit listning URL: er kan göra att klustret inte stöds.

> [!NOTE]
> För närvarande tillåter AKS inte att du fullständigt låser ut utgående trafik från klustret. Om du vill kontrol lera listan över URL: er och portar som klustret kan använda för utgående trafik, se [begränsa utgående trafik](limit-egress-traffic.md).

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Funktioner som inte stöds av alpha och beta Kubernetes

AKS stöder endast stabila funktioner i det överordnade Kubernetes-projektet. Om inget annat har dokumenterats stöder AKS inte alfa-och beta funktioner som är tillgängliga i det överordnade Kubernetes-projektet.

I två scenarier kan alfa-eller beta funktioner distribueras innan de är allmänt tillgängliga:

* Kunderna har uppfyllt AKS produkt, support eller teknik team och har blivit ombedd att testa de här nya funktionerna.
* De här funktionerna har [Aktiver ATS av en funktions flagga](https://github.com/Azure/AKS/blob/master/previews.md). Kunderna måste uttryckligen välja att använda dessa funktioner.

## <a name="preview-features-or-feature-flags"></a>Förhands gransknings funktioner eller funktions flaggor

För funktioner och funktioner som kräver utökad testning och feedback från användaren, släpper Microsoft nya för hands versions funktioner eller funktioner bakom en funktions flagga. Tänk på dessa funktioner som för hands version eller beta funktioner.

Funktionerna för förhands granskning eller funktions flagga är inte avsedda för produktion. Pågående ändringar i API: er och beteende, fel korrigeringar och andra ändringar kan resultera i instabila kluster och stillestånds tid.

Funktioner som ingår i den offentliga för hands versionen är under stöd för bästa prestanda eftersom dessa funktioner är i för hands version och inte är avsedda för produktion och stöds endast av AKS-teamen för teknisk support under kontors tid. Mer information finns i:

* [Vanliga frågor och svar om support för Azure](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> För hands versions funktionerna börjar gälla på Azures *prenumerations* nivå. Installera inte för hands versions funktioner i en produktions prenumeration. I en produktions prenumeration kan förhands gransknings funktioner ändra standard-API-beteendet och påverka vanliga åtgärder.

## <a name="upstream-bugs-and-issues"></a>Överordna buggar och problem

Med tanke på hastigheten på utvecklingen i det överordnade Kubernetes-projektet, uppstår buggar. Vissa av dessa buggar kan inte korrigeras eller bearbetas runt i AKS-systemet. I stället kräver fel korrigeringar större korrigeringar för att överföra projekt (till exempel Kubernetes, Node-eller Worker-operativsystem och kernels). För komponenter som Microsoft äger (t. ex. Azure Cloud Provider) är AKS och Azure personal engagerade i att åtgärda problem med överströms i communityn.

När ett tekniskt support ärende är rot som orsakas av en eller flera överströms buggar, kommer AKS support och teknik team att:

* Identifiera och länka de överordnade buggar med all ytterligare information som kan hjälpa dig att förklara varför det här problemet påverkar klustret eller arbets belastningen. Kunderna får länkar till de nödvändiga databaserna så att de kan se problemen och se när en ny version kommer att tillhandahålla korrigeringar.
* Tillhandahålla potentiella lösningar eller begränsningar. Om problemet kan undvikas kommer ett [känt problem](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) att arkiveras i AKS-lagringsplatsen. Den kända problem filen förklarar:
  * Problemet, inklusive länkar till överordnade buggar.
  * Lösning och information om en uppgradering eller en annan beständig lösning.
  * Tuffa tids linjer för ärendets inkludering baserat på den överordnade versionen takt.
