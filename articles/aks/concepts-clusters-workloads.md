---
title: Begrepp - Kubernetes grunderna för Azure Kubernetes Services (AKS)
description: Lär dig kubernetes grundläggande kluster- och arbetsbelastningskomponenter och hur de relaterar till funktioner i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: bcf56aa89a42d65fdb7bf03696faad13c64cbc8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259648"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Kubernetes kärnbegrepp för Azure Kubernetes Service (AKS)

När programutveckling rör sig mot en behållarbaserad metod är behovet av att orkestrera och hantera resurser viktigt. Kubernetes är den ledande plattformen som ger möjlighet att tillhandahålla tillförlitlig schemaläggning av feltoleranta programarbetsbelastningar. Azure Kubernetes Service (AKS) är ett hanterat Kubernetes-erbjudande som ytterligare förenklar distribution och hantering av behållarbaserade program.

Den här artikeln introducerar grundläggande Kubernetes infrastrukturkomponenter som *kontrollplan,* *noder*och *nodpooler*. Arbetsbelastningsresurser som *poddar,* *distributioner*och *uppsättningar* introduceras också, tillsammans med hur resurser *grupperas*i namnområden .

## <a name="what-is-kubernetes"></a>Vad är Kubernetes?

Kubernetes är en plattform som utvecklas snabbt och som hanterar behållarbaserade program och tillhörande nätverks- och lagringskomponenter. Fokus ligger på programarbetsbelastningar, inte de underliggande infrastrukturkomponenterna. Kubernetes tillhandahåller en deklarativ metod för distributioner, med stöd av en robust uppsättning API:er för hanteringsåtgärder.

Du kan skapa och köra moderna, bärbara, mikrotjänstbaserade program som drar nytta av Kubernetes som orkestrerar och hanterar tillgängligheten för dessa programkomponenter. Kubernetes stöder både tillståndslösa och tillståndskänsliga program som team framsteg genom antagandet av mikrotjänster-baserade program.

Som en öppen plattform kan du med Kubernetes skapa dina program med önskat programmeringsspråk, operativsystem, bibliotek eller meddelandebuss. Befintliga ci/cd-verktyg (continuous integration and continuous delivery) kan integreras med Kubernetes för att schemalägga och distribuera utgåvor.

Azure Kubernetes Service (AKS) tillhandahåller en hanterad Kubernetes-tjänst som minskar komplexiteten för distribution och kärnhanteringsuppgifter, inklusive samordning av uppgraderingar. AKS-kontrollplanet hanteras av Azure-plattformen och du betalar bara för AKS-noderna som kör dina program. AKS är byggt ovanpå Azure Kubernetes Service Engine med öppen källkod ([aks-engine][aks-engine]).

## <a name="kubernetes-cluster-architecture"></a>Kubernetes klusterarkitektur

Ett Kubernetes-kluster är indelat i två komponenter:

- *Kontrollplannoder* tillhandahåller kubernetes-kärntjänster och orkestrering av programarbetsbelastningar.
- *Noder* kör dina programarbetsbelastningar.

![Kubernetes styr plan- och nodkomponenter](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>Kontrollplan

När du skapar ett AKS-kluster skapas och konfigureras ett kontrollplan automatiskt. Det här kontrollplanet tillhandahålls som en hanterad Azure-resurs som är abstraherad från användaren. Det kostar inget för kontrollplanet, bara noderna som ingår i AKS-klustret.

Kontrollplanet innehåller följande centrala Kubernetes-komponenter:

- *kube-apiserver* - API-servern är hur de underliggande Kubernetes API:erna exponeras. Den här komponenten tillhandahåller interaktionen `kubectl` för hanteringsverktyg, till exempel eller kubernetes instrumentpanelen.
- *etcd* - För att upprätthålla tillståndet för din Kubernetes kluster och konfiguration, är den högtillgängliga *ETCD* en nyckelvärde butik inom Kubernetes.
- *kube-scheduler* - När du skapar eller skalar program bestämmer Scheduler vilka noder som kan köra arbetsbelastningen och startar dem.
- *kube-controller-manager* - Controller Manager övervakar ett antal mindre styrenheter som utför åtgärder som att replikera poddar och hantera nodåtgärder.

AKS tillhandahåller ett kontrollplan för en innehavare, med en dedikerad API-server, Scheduler, etc. Du definierar antalet och storleken på noderna och Azure-plattformen konfigurerar den säkra kommunikationen mellan kontrollplanet och noderna. Interaktion med kontrollplanet sker via Kubernetes `kubectl` API:er, till exempel eller kubernetes instrumentpanelen.

Detta hanterade kontrollplan innebär att du inte behöver konfigurera komponenter som en högtillgänglighet *etcd-butik,* men det innebär också att du inte kan komma åt kontrollplanet direkt. Uppgraderingar till Kubernetes orkestreras via Azure CLI eller Azure-portalen, som uppgraderar kontrollplanet och sedan noderna. Om du vill felsöka möjliga problem kan du granska kontrollplansloggarna via Azure Monitor-loggar.

Om du behöver konfigurera kontrollplanet på ett visst sätt eller behöver direkt åtkomst till det kan du distribuera ditt eget Kubernetes-kluster med [aks-motorn][aks-engine].

För tillhörande metodtips finns i [Metodtips för klustersäkerhet och uppgraderingar i AKS][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Noder och nodpooler

Om du vill köra dina program och supporttjänster behöver du *en Kubernetes-nod*. Ett AKS-kluster har en eller flera noder, som är en virtuell Azure-dator (VM) som kör kubernetes-nodkomponenterna och behållarkörningen:

- Den `kubelet` är Kubernetes agent som bearbetar orkestrering begäranden från kontrollplanet och schemaläggning av att köra de begärda behållarna.
- Virtuella nätverk hanteras av *kube-proxy* på varje nod. Proxyn dirigerar nätverkstrafik och hanterar IP-adressering för tjänster och poddar.
- *Behållarkörningen* är den komponent som gör att behållarprogram kan köras och interagera med ytterligare resurser som det virtuella nätverket och lagringen. I AKS används Moby som containerkörning.

![Virtuell Azure-dator och stödresurser för en Kubernetes-nod](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

Azure VM-storleken för dina noder definierar hur många processorer, hur mycket minne och storlek och typ av lagringsutrymme som är tillgängligt (till exempel högpresterande SSD eller vanlig hårddisk). Om du räknar med ett behov av program som kräver stora mängder CPU och minne eller högpresterande lagring planerar du nodstorleken därefter. Du kan också skala upp antalet noder i AKS-klustret för att möta efterfrågan.

I AKS baseras den virtuella datorn för noderna i klustret för närvarande på Ubuntu Linux eller Windows Server 2019. När du skapar ett AKS-kluster eller skalar upp antalet noder skapar Azure-plattformen det begärda antalet virtuella datorer och konfigurerar dem. Det finns ingen manuell konfiguration för dig att utföra. Agentnoder faktureras som vanliga virtuella datorer, så alla rabatter du har på den virtuella datorns storlek som du använder (inklusive [Azure-reservationer)][reservation-discounts]tillämpas automatiskt.

Om du behöver använda ett annat värdoperativsystem, behållarkörning eller inkludera anpassade paket kan du distribuera ditt eget Kubernetes-kluster med [aks-engine][aks-engine]. Uppströmsversioner `aks-engine` funktioner och ger konfigurationsalternativ innan de officiellt stöds i AKS kluster. Om du till exempel vill använda en annan containerkörning än `aks-engine` Moby kan du använda för att konfigurera och distribuera ett Kubernetes-kluster som uppfyller dina aktuella behov.

### <a name="resource-reservations"></a>Resursreservationer

Nodresurser används av AKS för att få noden att fungera som en del av klustret. Detta kan skapa en avvikelse mellan nodens totala resurser och de resurser som kan allokeras när de används i AKS. Detta är viktigt att notera när du ställer in begäranden och gränser för användarbegränsade poddar.

Så här hittar du en nods allokerbara resurser:
```kubectl
kubectl describe node [NODE_NAME]

```

För att upprätthålla nodprestanda och funktionalitet reserveras resurser på varje nod av AKS. När en nod växer sig större i resurser växer resursreservationen på grund av en högre mängd användaredigerad poddar som behöver hanteras.

>[!NOTE]
> Omsalettföring av AKS-tillägg som Behållarstatistik (OMS) förbrukar ytterligare nodresurser.

- **CPU** - reserverad CPU är beroende av nodtyp och klusterkonfiguration som kan orsaka mindre allokerbar processor på grund av att du kör ytterligare funktioner

| CPU-kärnor på värd | 1 | 2 | 4 | 8 | 16 | 32|64|
|---|---|---|---|---|---|---|---|
|Kube-reserverade (millicores)|60|100|140|180|260|420|740|

- **Minne** - minne som används av AKS innehåller summan av två värden.

1. Kubelet daemon installeras på alla Kubernetes-agentnoder för att hantera skapande och avslutning av behållare. Som standard på AKS har den här demonen följande vräkningsregel: *memory.available<750Mi*, vilket innebär att en nod alltid måste ha minst 750 Mi allokerbar hela tiden.  När en värd ligger under tröskelvärdet för tillgängligt minne avslutar kubelet en av de poddar som körs för att frigöra minne på värddatorn och skydda den. Detta är en reaktiv åtgärd när tillgängligt minne minskar utöver 750Mi tröskeln.

2. Det andra värdet är en progressiv minnesreservation för kubelet daemon att fungera korrekt (kube-reserverade).
    - 25% av de första 4 GB minne
    - 20 % av nästa 4 GB minne (upp till 8 GB)
    - 10 % av nästa 8 GB minne (upp till 16 GB)
    - 6 % av nästa 112 GB minne (upp till 128 GB)
    - 2% av alla minne över 128 GB

Ovanstående regler för minnes- och CPU-allokering används för att hålla agentnoder felfria, inklusive vissa värdsystemenheter som är kritiska för klusterhälsa. Dessa allokeringsregler gör också att noden rapporterar mindre allokerbart minne och processor än om den inte ingick i ett Kubernetes-kluster. Det går inte att ändra ovanstående resursreservationer.

Om en nod till exempel erbjuder 7 GB rapporterar den att 34 % av minnet inte kan allokeras ovanpå tröskelvärdet för hård vräkning på 750Mi.

`(0.25*4) + (0.20*3) = + 1 GB + 0.6GB = 1.6GB / 7GB = 22.86% reserved`

Förutom reservationer för Kubernetes själv, reserverar den underliggande noden OS också en mängd CPU- och minnesresurser för att underhålla OS-funktioner.

För associerade metodtips finns i [Metodtips för grundläggande schemaläggarfunktioner i AKS][operator-best-practices-scheduler].

### <a name="node-pools"></a>Nodpooler

Noder i samma konfiguration grupperas tillsammans i *nodpooler*. Ett Kubernetes-kluster innehåller en eller flera nodpooler. Det ursprungliga antalet noder och storlek definieras när du skapar ett AKS-kluster, vilket skapar en *standardnodpool*. Den här standardnodpoolen i AKS innehåller underliggande virtuella datorer som kör dina agentnoder.

> [!NOTE]
> Om du vill att klustret ska fungera tillförlitligt bör du köra minst 2 (två) noder i standardnodpoolen.

När du skalar eller uppgraderar ett AKS-kluster utförs åtgärden mot standardnodpoolen. Du kan också välja att skala eller uppgradera en viss nodpool. För uppgraderingsåtgärder schemaläggs behållare som körs på andra noder i nodpoolen tills alla noder har uppgraderats.

Mer information om hur du använder flera nodpooler i AKS finns i [Skapa och hantera flera nodpooler för ett kluster i AKS][use-multiple-node-pools].

### <a name="node-selectors"></a>Nodväljare

I ett AKS-kluster som innehåller flera nodpooler kan du behöva tala om för Kubernetes Scheduler vilken nodpool som ska användas för en viss resurs. Inkommande styrenheter bör till exempel inte köras på Windows Server-noder (för närvarande i förhandsversion i AKS). Med nodväljare kan du definiera olika parametrar, till exempel nodoperativsystemet, för att styra var en pod ska schemaläggas.

I följande grundläggande exempel schemaläggs en NGINX-instans på en Linux-nod med nodväljaren *"beta.kubernetes.io/os": linux*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx:1.15.12
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

Mer information om hur du styr var poddar är schemalagda finns [i Metodtips för avancerade schemaläggarfunktioner i AKS][operator-best-practices-advanced-scheduler].

## <a name="pods"></a>Skida

Kubernetes använder *poddar* för att köra en instans av ditt program. En pod representerar en enda instans av ditt program. Poddar har vanligtvis en 1:1-mappning med en behållare, även om det finns avancerade scenarier där en pod kan innehålla flera behållare. Dessa poddar med flera behållare schemaläggs tillsammans på samma nod och tillåter behållare att dela relaterade resurser.

När du skapar en pod kan du definiera *resursbegäranden för* att begära en viss mängd CPU- eller minnesresurser. Kubernetes Scheduler försöker schemalägga poddarna så att de körs på en nod med tillgängliga resurser för att uppfylla begäran. Du kan också ange maximala resursgränser som förhindrar att en viss pod förbrukar för mycket beräkningsresurs från den underliggande noden. En bästa praxis är att inkludera resursgränser för alla poddar för att hjälpa Kubernetes Scheduler att förstå vilka resurser som behövs och tillåts.

Mer information finns i [Kubernetes poddar][kubernetes-pods] och [Kubernetes pod lifecycle][kubernetes-pod-lifecycle].

En pod är en logisk resurs, men behållaren/behållarna är där programarbetsbelastningarna körs. Poddar är vanligtvis efemära, disponibla resurser och individuellt schemalagda poddar missar några av de funktioner med hög tillgänglighet och redundans som Kubernetes tillhandahåller. Poddar distribueras vanligtvis och hanteras av Kubernetes *Controllers,* till exempel distributionsstyrenheten.

## <a name="deployments-and-yaml-manifests"></a>Distributioner och YAML-manifest

En *distribution* representerar en eller flera identiska poddar som hanteras av Kubernetes Deployment Controller. En distribution definierar antalet *repliker* (poddar) som ska skapas, och Kubernetes Scheduler säkerställer att ytterligare poddar schemaläggs på felfria noder om poddar eller noder stöter på problem.

Du kan uppdatera distributioner för att ändra konfigurationen av poddar, behållaravbildning som används eller bifogad lagring. Distributionsstyrenheten tömmer och avslutar ett visst antal repliker, skapar repliker från den nya distributionsdefinitionen och fortsätter processen tills alla repliker i distributionen uppdateras.

De flesta tillståndslösa program i AKS bör använda distributionsmodellen i stället för att schemalägga enskilda poddar. Kubernetes kan övervaka hälsotillståndet och statusen för distributioner för att säkerställa att det erforderliga antalet repliker körs i klustret. När du bara schemalägger enskilda poddar startas poddarna inte om de stöter på ett problem och schemaläggs inte om på felfria noder om deras aktuella nod stöter på ett problem.

Om ett program kräver kvorum av instanser att alltid vara tillgängliga för förvaltningsbeslut som ska göras, vill du inte att en uppdateringsprocess för att störa den möjligheten. *Pod Avbrottsbudgetar* kan användas för att definiera hur många repliker i en distribution som kan tas bort under en uppdatering eller noduppgradering. Om du till exempel har *fem* repliker i distributionen kan du definiera en pod-störning på *4* så att endast en replik tas bort/schemaläggs om åt gången. Precis som med pod-resursgränser är en bästa praxis att definiera pod störningar budgetar för program som kräver ett minsta antal repliker att alltid vara närvarande.

Distributioner skapas och `kubectl create` hanteras vanligtvis med eller `kubectl apply`. Om du vill skapa en distribution definierar du en manifestfil i YAML-formatet (YAML Ain't Markup Language). I följande exempel skapas en grundläggande distribution av NGINX-webbservern. Distributionen anger *3* repliker som ska skapas och att port *80* ska vara öppen på behållaren. Resursbegäranden och -begränsningar har också definierats för CPU och minne.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

Mer komplexa program kan skapas genom att även inkludera tjänster som belastningsutjämnare i YAML-manifestet.

Mer information finns i [Kubernetes-distributioner][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Pakethantering med Helm

En vanlig metod för att hantera applikationer i Kubernetes är med [Helm][helm]. Du kan skapa och använda befintliga offentliga *Helm-diagram* som innehåller en paketerad version av programkod och Kubernetes YAML-manifest för att distribuera resurser. Dessa Helm-diagram kan lagras lokalt eller ofta i en fjärrdatabas, till exempel en [Azure Container Registry Helm-diagramrepo][acr-helm].

Om du vill använda Helm installeras en serverkomponent som heter *Tiller* i kubernetes-klustret. Tiller hanterar installationen av diagram i klustret. Helm-klienten själv installeras lokalt på din dator eller kan användas i [Azure Cloud Shell][azure-cloud-shell]. Du kan söka efter eller skapa Helm-diagram med klienten och sedan installera dem i Kubernetes-klustret.

![Helm innehåller en klientkomponent och en Tiller-komponent på serversidan som skapar resurser i Kubernetes-klustret](media/concepts-clusters-workloads/use-helm.png)

Mer information finns [i Installera program med Helm i Azure Kubernetes Service (AKS)][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets och DaemonSets

Distributionsstyrenheten använder Kubernetes Scheduler för att köra ett visst antal repliker på en tillgänglig nod med tillgängliga resurser. Den här metoden för att använda distributioner kan vara tillräcklig för tillståndslösa program, men inte för program som kräver en beständig namngivningskonvention eller lagring. För program som kräver att en replik finns på varje nod, eller valda noder, i ett kluster, tittar distributionsstyrenheten inte på hur repliker fördelas över noderna.

Det finns två Kubernetes-resurser som gör att du kan hantera dessa typer av program:

- *StatefulSets* - Underhåll tillståndet för program utanför en enskild pod livscykel, till exempel lagring.
- *DaemonSets* - Se till att varje nod körs tidigt i Kubernetes bootstrap-processen.

### <a name="statefulsets"></a>StatefulSets

Modern applikationsutveckling syftar ofta till tillståndslösa program, men *StatefulSets* kan användas för tillståndskänsliga program, till exempel program som innehåller databaskomponenter. En StatefulSet liknar en distribution genom att en eller flera identiska poddar skapas och hanteras. Repliker i en StatefulSet följer en graciös, sekventiell metod för distribution, skala, uppgraderingar och uppsägningar. Med en StatefulSet (som repliker är omlagda) kvarstår namngivningskonventionen, nätverksnamn och lagring.

Du definierar programmet i `kind: StatefulSet`YAML-format med , och StatefulSet Controller hanterar sedan distributionen och hanteringen av de nödvändiga replikerna. Data skrivs till beständig lagring som tillhandahålls av Azure Managed Disks eller Azure Files. Med StatefulSets förblir den underliggande beständiga lagringen även när statefulSet tas bort.

Mer information finns i [Kubernetes StatefulSets][kubernetes-statefulsets].

Repliker i en StatefulSet schemaläggs och körs över alla tillgängliga noder i ett AKS-kluster. Om du behöver se till att minst en pod i setet körs på en nod kan du i stället använda en DaemonSet.

### <a name="daemonsets"></a>DemonSet

För specifika logginsamlings- eller övervakningsbehov kan du behöva köra en viss pod på alla, eller valda, noder. En *DaemonSet* används igen för att distribuera en eller flera identiska poddar, men DaemonSet Controller ser till att varje nod som anges kör en instans av podden.

DaemonSet Controller kan schemalägga poddar på noder tidigt i klusterstartprocessen innan kubernetes-schemaläggaren har startat. Den här möjligheten säkerställer att poddar i en DaemonSet startas innan traditionella poddar i en distribution eller StatefulSet schemaläggs.

Liksom StatefulSets definieras en DaemonSet som en `kind: DaemonSet`del av en YAML-definition med .

Mer information finns i [Kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> Om du använder tillägget Virtuella noder skapas inte poddar på den virtuella noden om du använder [tillägget Virtuella noder.](virtual-nodes-cli.md#enable-virtual-nodes-addon)

## <a name="namespaces"></a>Namnområden

Kubernetes-resurser, till exempel poddar och distributioner, grupperas logiskt i ett *namnområde*. Dessa grupperingar är ett sätt att logiskt dela upp ett AKS-kluster och begränsa åtkomsten till att skapa, visa eller hantera resurser. Du kan till exempel skapa namnområden för att avgränsa företagsgrupper. Användare kan bara interagera med resurser inom sina tilldelade namnområden.

![Kubernetes namnområden för att logiskt dela upp resurser och program](media/concepts-clusters-workloads/namespaces.png)

När du skapar ett AKS-kluster är följande namnområden tillgängliga:

- *standard* - Det här namnområdet är där poddar och distributioner skapas som standard när ingen anges. I mindre miljöer kan du distribuera program direkt till standardnamnområdet utan att skapa ytterligare logiska separationer. När du interagerar med Kubernetes `kubectl get pods`API, till exempel med , används standardnamnområdet när ingen anges.
- *kube-system* - Det här namnområdet är där kärnresurser finns, till exempel nätverksfunktioner som DNS och proxy, eller Kubernetes-instrumentpanelen. Du distribuerar vanligtvis inte dina egna program till det här namnområdet.
- *kube-public* - Det här namnområdet används vanligtvis inte, men kan användas för att resurser ska vara synliga i hela klustret och kan visas av alla användare.

Mer information finns i [Kubernetes namnområden][kubernetes-namespaces].

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskriver några av de centrala Kubernetes-komponenterna och hur de gäller för AKS-kluster. Mer information om kubernetes och AKS-huvudbegrepp finns i följande artiklar:

- [Kubernetes / AKS tillgång och identitet][aks-concepts-identity]
- [Kubernetes / AKS säkerhet][aks-concepts-security]
- [Kubernetes / AKS virtuella nätverk][aks-concepts-network]
- [Kubernetes / AKS lagring][aks-concepts-storage]
- [Kubernetes / AKS skala][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
