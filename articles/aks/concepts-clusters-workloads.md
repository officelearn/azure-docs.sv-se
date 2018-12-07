---
title: Begrepp - grundläggande om Kubernetes för Azure Kubernetes Services (AKS)
description: Lär dig de grundläggande klustret och arbetsbelastningen komponenterna i Kubernetes och hur de relaterar till funktioner i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: f5695e52528c3384c46c49c5c5ec2e451bd0be7c
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998095"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Kubernetes grundläggande begrepp för Azure Kubernetes Service (AKS)

Eftersom programutveckling har flyttats till en behållarbaserad metod, blir behovet av att dirigera och hantera de sammankopplade resurserna viktigt. Kubernetes är den ledande plattformen som ger dig möjlighet att tillhandahålla tillförlitlig schemaläggning av arbetsbelastningar feltoleranta program. Azure Kubernetes Service (AKS) är en hanterad Kubernetes erbjudande som ytterligare förenklar behållarbaserade programdistribution och hantering.

Den här artikeln introducerar kärnkomponenter för Kubernetes-infrastrukturen som den *kluster master*, *noder*, och *nodpooler*. Arbetsbelastningen resurser, som *poddar*, *distributioner*, och *anger* också introduceras, tillsammans med hur du gruppera resurser i *namnområden*.

## <a name="what-is-kubernetes"></a>Vad är Kubernetes?

Kubernetes är en snabbt växande plattform som klarar behållarbaserade program och deras associerade nätverks- och lagringskomponenter. Fokus ligger på programbelastningar, inte de underliggande infrastruktur-komponenterna. Kubernetes tillhandahåller en deklarativ metod för distributioner som backas upp av en stabil uppsättning API: er för hanteringsåtgärder.

Du kan skapa och köra moderna och smidig mikrotjänstbaserade program som har nytta av Kubernetes samordna och hantera tillgängligheten för dessa programkomponenter. Kubernetes har stöd för både tillståndslösa och tillståndskänsliga program som teams arbete genom införande av mikrotjänstbaserade program.

Som en öppen plattform kan Kubernetes du skapa program med din önskade programmeringsspråk, operativsystem, bibliotek eller meddelanden bus. Befintliga kontinuerlig integrering och leverans (CI/CD) kan integreras med Kubernetes att schemalägga och distribuera versioner.

Azure Kubernetes Service (AKS) är en hanterad Kubernetes-tjänst som minskar komplexiteten för distribution och core hanteringsuppgifter, inklusive samordna uppgraderingar. Huvudservrar för AKS-kluster som hanteras av Azure-plattformen och du betalar bara för AKS-noder som kör dina program. AKS bygger på öppen källkod Azure Kubernetes Service Engine (aks-motor).

## <a name="kubernetes-cluster-architecture"></a>Arkitektur för Kubernetes-kluster

Ett Kubernetes-kluster är uppdelad i två komponenter:

- *Klustret master* noder ange Kubernetes kärntjänsterna och dirigering av arbetsbelastningar för program.
- *Noder* kör dina arbetsbelastningar för program.

![Kubernetes-kluster huvud- och noddatorer komponenter](media/concepts-clusters-workloads/cluster-master-and-nodes.png)

## <a name="cluster-master"></a>Klustret master

När du skapar ett AKS-kluster skapas och konfigureras automatiskt en kluster-master. Det här klustret master tillhandahålls som en hanterad Azure-resurs som tas ut från användaren. Det kostar ingenting för klustret endast de noder som är en del av AKS-klustret.

Kluster-master innehåller följande Kubernetes kärnkomponenter:

- *kube-apiserver* -API-servern är hur de underliggande Kubernetes-API: erna exponeras. Den här komponenten tillhandahåller interaktionen för hanteringsverktyg, till exempel `kubectl` eller Kubernetes-instrumentpanelen.
- *etcd* – om du vill underhålla tillståndet för ditt Kubernetes-kluster och konfiguration, med hög tillgänglighet *etcd* är ett nyckelvärde i Kubernetes.
- *kube-schemaläggare* – när du skapar eller skala program, Scheduler avgör vilka noder kan köra arbetsbelastningen och startar dem.
- *kube-controller-manager* – The Controller Manager övervakar ett antal mindre domänkontrollanter som utför åtgärder, till exempel replikera poddar och hantera åtgärder på noden.

Ger en enda klient kluster-master för AKS med en dedikerad API-server, Scheduler, osv. Du definierar antalet och storleken på noderna och Azure-plattformen konfigurerar säker kommunikation mellan kluster huvud- och noder. Interaktion med kluster sker via Kubernetes APIs som `kubectl` eller Kubernetes-instrumentpanelen.

Den här bakgrunden hanterat kluster innebär att du inte behöver konfigurera komponenter som en högtillgänglig *etcd* store, men det innebär också att du inte åtkomst till klustret master direkt. Uppgradering till Kubernetes orkestreras via Azure CLI eller Azure-portalen som uppgraderar klustret master och sedan på noderna. Du kan granska master loggarna kluster via Azure Log Analytics för att felsöka eventuella problem.

Om du vill konfigurera kluster-master i ett visst sätt eller behöver direkt åtkomst till dem. Du kan distribuera din egen Kubernetes-kluster med [aks-engine][aks-engine].

## <a name="nodes-and-node-pools"></a>Noder och nodpooler

Om du vill köra dina program och tjänsterna du behöver ett Kubernetes *noden*. Ett AKS-kluster har en eller flera noder, vilket är en Azure-dator (VM) som kör Kubernetes noden komponenter och behållaren runtime:

- Den `kubelet` är Kubernetes-agenten som bearbetar orchestration-begäranden från klustret master och schemaläggning i de begärda behållarna som körs.
- Virtuella nätverk som hanteras av den *kube-proxy* på varje nod. Proxyvägar nätverkstrafik och hanterar IP-adresser för tjänster och poddar.
- Den *behållare runtime* är den komponent som gör att program i behållare att köra och interagera med ytterligare resurser, till exempel virtuella nätverk och lagring. I AKS används Docker som behållare runtime.

![Azure-dator och stödresurser för ett Kubernetes-nod](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

Azure VM-storlek för noderna definierar hur många processorer, hur mycket minne och storlek och typ av lagring som är tillgängliga (till exempel högpresterande SSD och regelbundna HDD). Om du räknar behov av program som kräver stora mängder CPU och minne eller lagring med höga prestanda, planera på lämpligt sätt nodstorlek. Du kan även skala upp antalet noder i AKS-kluster för att möta efterfrågan.

I AKS baserat VM-avbildning för noderna i klustret för närvarande på Ubuntu Linux. När du skapar ett AKS-kluster eller skala upp antalet noder, skapar det begärda antalet virtuella datorer i Azure-plattformen och konfigurerar dem. Det finns ingen manuell konfiguration som du kan utföra.

Om du vill använda en annan värd OS, körning av behållare, eller anpassade paket kan du distribuera din egen Kubernetes-kluster med [aks-engine][aks-engine]. Den överordnade `aks-engine` släpper funktioner och ange konfigurationsalternativ innan de stöds officiellt i AKS-kluster. Till exempel om du vill använda Windows-behållare eller en behållare runtime än Docker, du kan använda `aks-engine` att konfigurera och distribuera ett Kubernetes-kluster som uppfyller dina befintliga behov.

### <a name="resource-reservations"></a>Resurs-reservationer

Du behöver inte hantera Kubernetes kärnkomponenter på varje nod som den *kubelet*, *kube-proxy*, och *kube-dns*, men de använder några av de tillgängliga beräkningsresurs. Om du vill behålla noden prestandan och funktionaliteten är följande beräkningsresurser reserveras på varje nod:

- **CPU** – 60ms
- **Minne** – 20% upp till 4 GiB

Reservationerna innebär att mängden tillgängliga CPU och minne för dina program kan verka mindre än noden själva innehåller. Om det finns resursbegränsningar på grund av antalet program som körs, reservationerna Kontrollera CPU och minne är fortfarande tillgänglig för Kubernetes kärnkomponenter. Resurs-reservationer kan inte ändras.

Exempel:

- **Standard DS2 v2** nodstorlek innehåller 2 virtuella processorer och 7 GiB minne
    - 20% av 7 GiB minne = 1,4 GiB
    - Totalt *(7 1.4) = 5,6 GiB* minne är tillgänglig för noden
    
- **Standard E4s v3** nodstorlek innehåller 4 virtuella processorer och 32 GiB minne
    - 20% av 32 GiB minne = 6.4 GiB, men AKS endast reserverar högst 4 GiB
    - Totalt *(32-4) = 28 GiB* är tillgänglig för noden
    
Den underliggande noden OS kräver också vissa delar av processor och minne resurser för att slutföra sin egen kärnfunktioner.

### <a name="node-pools"></a>Nodpooler

Noder i samma konfiguration grupperas tillsammans i *nodpooler*. Ett Kubernetes-kluster innehåller en eller flera nodpooler. Det inledande antalet noder och storleken som definieras när du skapar ett AKS-kluster, vilket skapar en *standard nodpool*. Den här noden standardadresspool i AKS innehåller de underliggande virtuella datorer som kör din agent noder.

När du skalar eller uppgradera ett AKS-kluster, utföra åtgärden mot standardpoolen för noden. För uppgradering schemaläggs behållare som körs på andra noder i nodpoolen tills alla noder har uppgraderats.

## <a name="pods"></a>Poddar

Kubernetes använder *poddar* att köra en instans av programmet. En pod representerar en instans av programmet. Poddar har vanligtvis en 1:1-mappning med en behållare, även om det är avancerade scenarier där en pod kan innehålla flera behållare. Dessa flera behållare poddar schemaläggs tillsammans på samma nod och att behållare delar relaterade resurser.

När du skapar en pod, kan du definiera *resursgränser* att begära en viss mängd resurser som CPU eller minne. Kubernetes-Scheduler försöker schemalägga poddarna ska köras på en nod med tillgängliga resurser för att uppfylla begäran. Du kan också ange maximala resursbegränsningar som förhindrar att en viss pod förbrukar för mycket beräkningsresurs från den underliggande noden. Ett bra tips är att inkludera resursgränser för alla poddarna för att förstå vilka resurser som behövs och tillåts Kubernetes Scheduler.

Mer information finns i [Kubernetes-poddar] [ kubernetes-pods] och [Kubernetes pod livscykel][kubernetes-pod-lifecycle].

En pod är en logisk resurs, men behållarna finns där programmet arbetsbelastningar körs. Poddar är normalt tillfälliga, disponibla resurser och individuellt schemalagda poddar missar några av de hög tillgänglighet och redundans funktionerna som Kubernetes tillhandahåller. I stället poddar vanligtvis distribueras och hanteras av Kubernetes *domänkontrollanter*, till exempel kontrollanten distribution.

## <a name="deployments-and-yaml-manifests"></a>Distributioner och YAML-manifest

En *distribution* representerar en eller flera identiska poddar, hanteras av kontrollanten Kubernetes-distribution. En distribution definierar antalet *repliker* (poddar) för att skapa, och Kubernetes Scheduler säkerställer att nya poddarna schemaläggs på felfria noder om poddar eller noder får problem kan.

Du kan uppdatera distributioner om du vill ändra konfigurationen av poddar, behållaravbildning används eller ansluten lagring. Kontrollanten distribution tömmer avslutas ett visst antal repliker, skapar repliker från den nya distributionsdefinitionen och fortsätter processen tills alla repliker i distributionen uppdateras.

De flesta tillståndslösa program i AKS ska använda distributionsmodellen i stället för att schemalägga enskilda poddar. Kubernetes kan övervaka hälsotillstånd och status för distributioner för att se till att de nödvändiga antal repliker som körs i klustret. När du bara schemalägga enskilda poddar poddarna inte startas om om de stöter på ett problem och ändras inte på felfria noder om deras aktuella noden påträffar ett problem.

Om ett program kräver ett kvorum av instanser ska alltid vara tillgänglig för hantering av beslut om, kan du inte vill en uppdateringsprocess stör den möjligheten. *Pod avbrott budgetar* kan användas för att definiera hur många repliker i en distribution kan stängas av under en uppdatering eller nod-uppgradering. Om du har till exempel *5* repliker i distributionen, kan du definiera en pod-avbrott i *4* för att endast tillåta en replik från att ta bort/schemaläggas i taget. Som med pod resursbegränsningar är bästa praxis att definiera pod avbrott budgetar på program som kräver ett minsta antal repliker alltid måste finnas.

Distributioner vanligtvis skapas och hanteras med `kubectl create` eller `kubectl apply`. Om du vill skapa en distribution, definierar du en manifestfil i formatet YAML (YAML Ain't Markup Language). I följande exempel skapas en grundläggande distribution av NGINX-webbservern. Distributionen anger *3* repliker som ska skapas och den porten *80* vara öppna för behållaren. Resursbegäranden och begränsningar definieras också för CPU och minne.

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

Mer komplexa program kan skapas genom att även inkludera tjänster, till exempel belastningsutjämnare i YAML-manifestet.

Mer information finns i [Kubernetes-distributioner][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Pakethantering med Helm

Ett vanligt sätt att hantera program i Kubernetes är med [Helm][helm]. Du kan skapa och använda befintliga offentliga Helm *diagram* som innehåller en paketerad version programkod och Kubernetes YAML-manifestet för att distribuera resurser. Dessa Helm-diagram kan lagras lokalt eller ofta i en fjärransluten lagringsplats, till exempel en [lagringsplatsen för Azure Container Registry Helm-diagrammet][acr-helm].

Om du vill använda Helm en serverkomponent kallas *Tiller* är installerad i Kubernetes-klustret. Tiller hanterar installationen av diagram i klustret. Själva Helm-klienten har installerats lokalt på datorn eller kan användas inom de [Azure Cloud Shell][azure-cloud-shell]. Du kan söka efter eller skapa Helm-diagram med klienten och sedan installera dem till ditt Kubernetes-kluster.

![Helm innehåller en klient- och serversidan Tiller-komponenten, som skapar resurser i Kubernetes-kluster](media/concepts-clusters-workloads/use-helm.png)

Mer information finns i [installera program med Helm i Azure Kubernetes Service (AKS)][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets och DaemonSets

Kontrollanten distribution använder Kubernetes Scheduler för att köra ett visst antal repliker på någon tillgänglig nod med tillgängliga resurser. Den här metoden för att använda distributioner kanske är tillräckligt för tillståndslösa program, men inte för program som kräver en beständig namngivningskonvention eller lagring. Kontrollanten distributionen titta inte program som kräver en replik ska finnas på varje nod eller valda noderna i ett kluster, på hur replikerna är fördelade på noderna.

Det finns två Kubernetes-resurser som kan du hantera dessa typer av program:

- *StatefulSets* -underhålla tillståndet för program utöver en enskild pod-livscykeln, t.ex lagring.
- *DaemonSets* – se till att en instans som körs på varje nod, tidigt i processen för Kubernetes bootstrap.

### <a name="statefulsets"></a>StatefulSets

Ofta moderna programutveckling syftar för tillståndslösa program, men *StatefulSets* kan användas för tillståndskänsliga program, till exempel program som innehåller databaskomponenter. En StatefulSet liknar en distribution i att en eller flera identiska poddar skapas och hanteras. Repliker i en StatefulSet följer en korrekt, sekventiella metod för distribution, skalning, uppgraderingar och uppsägningar. Spara som schemaläggs repliker med en StatefulSet, namngivningskonvention, nätverksnamn och lagring.

Du definierar programmet i YAML-format med `kind: StatefulSet`, och kontrollanten StatefulSet hanterar distribution och hantering av de nödvändiga replikerna. Data skrivs till beständig lagring, som tillhandahålls av Azure Managed Disks eller Azure Files. Med StatefulSets, den underliggande beständig lagringen finns kvar även när StatefulSet tas bort.

Mer information finns i [Kubernetes StatefulSets][kubernetes-statefulsets].

Repliker i en StatefulSet schemaläggs och körs över alla tillgängliga noder i ett AKS-kluster. Om du vill se till att minst en pod i din körs på en nod kan du istället använda ett DaemonSet.

### <a name="daemonsets"></a>DaemonSets

Du kan behöva köra en viss pod på alla för specifika Logginsamling eller behoven för övervakning, eller valda noder. En *DaemonSet* igen används för att distribuera en eller flera identiska poddar, men kontrollanten DaemonSet säkerställer att varje nod som anges kör en instans av din pod.

Kontrollanten DaemonSet kan schemalägga poddar på noder tidigt i startprocessen kluster, innan standard Kubernetes scheduler har startats. Den här möjligheten säkerställer att poddar i en DaemonSet har startats innan traditionella poddar i en distribution eller StatefulSet schemaläggs.

Som StatefulSets, ett DaemonSet har definierats som en del av en YAML-definition med `kind: DaemonSet`.

Mer information finns i [Kubernetes DaemonSets][kubernetes-daemonset].

## <a name="namespaces"></a>Namnområden

Kubernetes-resurser, till exempel poddar och distributioner, är logiskt grupperade i en *namnområde*. De här grupperna är ett sätt att dela upp ett AKS-kluster och begränsa åtkomsten för att skapa, visa eller hantera resurser logiskt. Du kan skapa namnområden för att avgränsa affärsgrupper, till exempel. Användare kan bara interagera med resurser i deras tilldelade namnområden.

![Kubernetes-namnområden logiskt dela resurser och program](media/concepts-clusters-workloads/namespaces.png)

Följande namnrymder är tillgängliga när du skapar ett AKS-kluster:

- *standard* – det här namnområdet är där poddar och distributioner skapas som standard när ingen har angetts. Du kan distribuera program direkt i standardnamnområdet utan att skapa ytterligare logiska uppsägningar i mindre miljöer. När du interagerar med Kubernetes-API, till exempel med `kubectl get pods`, standardnamnområde som ska användas när inget har angetts.
- *kube system* – det här namnområdet är där kärnresurser finns till exempel nätverksfunktioner som DNS- och proxy- eller Kubernetes-instrumentpanelen. Du kan normalt inte distribuera dina egna program i det här namnområdet.
- *kube-offentlig* – det här namnområdet är vanligtvis inte används, men kan användas för resurser som ska visas i hela klustret och kan ses av alla användare.

Mer information finns i [Kubernetes namnområden][kubernetes-namespaces].

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskriver några av Kubernetes kärnkomponenter samt hur de används till AKS-kluster. Mer information om core Kubernetes och AKS-begrepp finns i följande artiklar:

- [Kubernetes / AKS åtkomst och identitet][aks-concepts-identity]
- [Kubernetes / AKS-säkerhet][aks-concepts-security]
- [Kubernetes / AKS virtuella nätverk][aks-concepts-network]
- [Kubernetes / AKS-lagring][aks-concepts-storage]
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
