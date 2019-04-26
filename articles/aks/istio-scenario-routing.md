---
title: Intelligent Routning och kanarieversioner med Istio i Azure Kubernetes Service (AKS)
description: Lär dig hur du använder Istio att ange intelligent Routning och distribuera kanarieversioner i ett kluster i Azure Kubernetes Service (AKS)
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 12/03/2018
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 0a4e5e7e310a9949ee59291c2032eafda46955a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60465943"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Använd intelligenta Routning och kanarieversioner med Istio i Azure Kubernetes Service (AKS)

[Istio] [ istio-github] är en öppen källkod service-nät som ger en nyckeluppsättning av funktioner i mikrotjänster i ett Kubernetes-kluster. Dessa funktioner innefattar trafikhantering, identitet och säkerhet, genomförande av principer och observability. Mer information om Istio finns i officiellt [vad är Istio?] [ istio-docs-concepts] dokumentation.

Den här artikeln visar hur du använder funktionen trafik för hantering av Istio. En exempel AKS röstningsapp används för att utforska intelligent Routning och kanarieversioner.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Distribuera programmet
> * Uppdatera programmet
> * Distribuera en kontrollvärde version av programmet
> * Slutför installationen

## <a name="before-you-begin"></a>Innan du börjar

Stegen som beskrivs i den här artikeln förutsätter att du har skapat ett AKS-kluster (Kubernetes 1.10 och ovan, med RBAC aktiverad) och har upprättat en `kubectl` anslutning med klustret. Du måste också Istio installeras i klustret.

Om du behöver hjälp med något av dessa objekt läser den [AKS-Snabbstart] [ aks-quickstart] och [installera Istio i AKS][istio-install].

## <a name="about-this-application-scenario"></a>Om det här programscenariot

Exemplet AKS röstningsapp tillhandahåller två röstningsalternativ (katt eller hund). Det finns en storage-komponent som kvarstår antalet röster för varje alternativ. Det finns också en analytics-komponent som tillhandahåller information kring de rösterna för varje alternativ.

I den här artikeln börjar du genom att distribuera version *1.0* röstningsapp och version *1.0* för analytics-komponenten. Komponenten analytics ger enkel antal för antal röster. Röstningsapp och analytics komponenten interagerar med version *1.0* av Lagringskomponenten som backas upp av Redis.

Uppgradering av komponenten analytics till version *1.1*, som innehåller antalet och nu summor och procenttal.

En delmängd av användarna testversion *2.0* av appen via en canary release. Den här nya versionen använder en storage-komponent som backas upp av en MySQL-databas.

När du är säker på att version *2.0* fungerar som förväntat på din underuppsättning med användare du lanserar version *2.0* till alla dina användare.

## <a name="deploy-the-application"></a>Distribuera programmet

Låt oss börja genom att distribuera programmet till ditt kluster i Azure Kubernetes Service (AKS). Följande diagram visar vad går att köra i slutet av det här avsnittet - version *1.0* av alla komponenter med inkommande begäranden via Istio ingress-gateway:

![AKS rösta appkomponenter och routning.](media/istio/components-and-routing-01.png)

Artefakter som du måste följa den här artikeln är tillgängliga i den [Azure-Samples/aks-rösta-app] [ github-azure-sample] GitHub-lagringsplatsen. Du kan ladda ned artefakter, eller så kan du klona lagringsplatsen på följande sätt:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Ändra till följande mapp i den nedladdade / klonade lagringsplatsen och köra alla efterföljande steg från denna mapp:

```console
cd scenarios/intelligent-routing-with-istio
```

Börja med att skapa ett namnområde i AKS-kluster för AKS röstande exempelappen med namnet *röstande* på följande sätt:

```console
kubectl create namespace voting
```

Etiketten namnområdet med `istio-injection=enabled`. Den här etiketten instruerar Istio att automatiskt mata in istio-proxyservrar som sidecar-filer i alla dina poddar i det här namnområdet.

```console
kubectl label namespace voting istio-injection=enabled
```

Nu ska vi skapa komponenter för appen AKS rösta. Skapa de här komponenterna i den *röstande* namnområdet som skapades i föregående steg.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

Följande Exempelutdata visar resurserna som har skapats:

```
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio har vissa krav gällande poddar och tjänster. Mer information finns i den [Istio krav för Poddar och tjänster dokumentation][istio-requirements-pods-and-services].

Visa poddarna som har skapats genom att använda den [kubectl hämta poddar] [ kubectl-get] -kommandot enligt följande:

```console
kubectl get pods -n voting
```

Följande Exempelutdata visar att det finns tre instanser av den *rösta app* pod och en enda instans av både den *rösta analytics* och *rösta storage* poddar. Varje poddarna har två behållare. En av de här behållarna är komponenten och den andra är den *istio proxy*:

```
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-669f99dcc8-lzh7k   2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-bdmld         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-gcrng         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-strzc         2/2       Running   0          1m
voting-storage-1-0-7954799d96-5fv9r     2/2       Running   0          1m
```

Om du vill se information om en pod, använda den [kubectl beskriver pod][kubectl-describe]. Ersätt namnet pod med namnet på en pod i din egen AKS-kluster från föregående utdata:

```console
kubectl describe pod voting-app-1-0-6c65c4bdd4-bdmld --namespace voting
```

Den *istio proxy* behållare automatiskt har varit införs av Istio att hantera nätverkstrafik till och från dina komponenter som visas i följande Exempelutdata:

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         dockerhub.azk8s.cn/istio/proxyv2:1.0.4
[...]
```

Du kan inte ansluta till röstningsapp förrän du har skapat Istio [Gateway] [ istio-reference-gateway] och [virtuella][istio-reference-virtualservice]. Resurserna Istio dirigera trafik från standardgateway för Istio ingång till vårt program.

> [!NOTE]
> En *Gateway* är en komponent i utkanten av tjänsten-nät som tar emot inkommande eller utgående HTTP- och TCP-trafik.
>
> En *virtuella* definierar en uppsättning regler för routning för en eller flera måltjänster.

Använd den `istioctl` klienten binära för att distribuera Gateway och Virtual Service yaml. Precis som med de `kubectl apply` kommandot, Kom ihåg att ange det namnområde som dessa resurser har distribuerats till.

```console
istioctl create -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

Hämta IP-adressen för Istio Ingress gatewayen med följande kommando:

```console
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

Följande Exempelutdata visar IP-adressen för den inkommande gatewayen:

```
52.187.250.239
```

Öppna en webbläsare och klistra in IP-adressen. Exemplet AKS röstningsapp visas.

![AKS rösta appen som körs i vår Istio aktiverat AKS-kluster.](media/istio/deploy-app-01.png)

Information längst ned på skärmen visar att appen använder version *1.0* av den *rösta app* och version *1.0* (Redis) som lagringsalternativet.

## <a name="update-the-application"></a>Uppdatera programmet

Vi ska vi distribuera en ny version av komponenten analytics. Den här nya versionen *1.1* visar summor och procentsatser förutom antalet för varje kategori.

Följande diagram visar vad går att köra i slutet av det här avsnittet - endast version *1.1* av våra *rösta analytics* komponenten har trafiken från den *rösta app* komponenten. Även om version *1.0* av våra *rösta analytics* komponenten fortsätter att köras och refererar till den *rösta analytics* tjänsten Istio proxyservrar nekar trafik till och från den.

![AKS rösta appkomponenter och routning.](media/istio/components-and-routing-02.png)

Nu ska vi distribuera version *1.1* av den *rösta analytics* komponent. Skapa den här komponenten i den *röstande* namnområde:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

Öppna exemplet AKS är röstningsapp i en webbläsare, som med hjälp av IP-adressen för gatewayen Istio ingående hämtade i föregående steg.

Webbläsaren växlar mellan de två vyerna som visas nedan. Eftersom du använder ett Kubernetes [Service] [ kubernetes-service] för den *rösta analytics* komponenten med endast en enskild etikett väljare (`app: voting-analytics`), Kubernetes använder beteendet för resursallokering mellan poddarna som matchar den Väljaren. I det här fallet är det både version *1.0* och *1.1* av din *rösta analytics* poddar.

![Version 1.0 av komponenten analytics som körs i vår AKS rösta app.](media/istio/deploy-app-01.png)

![Version 1.1 av komponenten analytics som körs i vår AKS rösta app.](media/istio/update-app-01.png)

Du kan visualisera växla mellan de två versionerna av den *rösta analytics* komponent på följande sätt. Kom ihåg att använda IP-adressen för en egen Istio Ingress-Gateway.

```console
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

Följande Exempelutdata visar den aktuella delen av webbplatsen returnerade som platsen växlar mellan versioner:

```
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Låsa trafik till programmets version 1.1

Nu ska vi låsa trafik till endast version *1.1* av den *rösta analytics* komponenten och till version *1.0* av den *rösta storage* komponenten. Därefter anger du routningsregler för alla andra komponenter.

> * En *virtuella* definierar en uppsättning regler för routning för en eller flera måltjänster.
> * En *Målregel* definierar trafik och specifika principer för version.
> * En *princip* definierar vilka autentiseringsmetoder kan godkännas för workload(s).

Du använder den `istioctl` binära klienten att ersätta virtuella tjänstdefinitionen på din *rösta app* och Lägg till [mål regler] [ istio-reference-destinationrule] och [ Tjänster för virtuella] [ istio-reference-virtualservice] för andra komponenter.

Du också lägga till en [princip] [ istio-reference-policy] till den *röstande* namnområdet för att se till att alla kommunikation mellan tjänster skyddas med ömsesidig TLS- och klientcertifikat.

Eftersom det inte finns en befintlig virtuell tjänstdefinition för *rösta app* du ersätta, använder de `istioctl replace` -kommandot enligt följande:

```console
istioctl replace -f istio/step-2a-update-voting-app-virtualservice.yaml --namespace voting
```

Följande Exempelutdata visar Istio Virtual Service har uppdaterats:

```
Updated config virtual-service/voting/voting-app to revision 141902
```

Använd sedan den `istioctl create` kommando för att lägga till den nya principen och den nya mål-regler och tjänster för virtuella för alla andra komponenter.

* Principen har `peers.mtls.mode` inställd `STRICT` så att ömsesidig TLS tillämpas mellan dina tjänster inom den *röstande* namnområde.
* Du också ange den `trafficPolicy.tls.mode` till `ISTIO_MUTUAL` i alla våra mål-regler. Istio tillhandahåller tjänster med stark identiteter och skyddar kommunikationen mellan tjänster med hjälp av ömsesidig TLS- och klientcertifikat som Istio hanterar transparent.

```console
istioctl create -f istio/step-2b-add-routing-for-all-components.yaml --namespace voting
```

Följande Exempelutdata visar den nya principen, mål-regler och tjänster för virtuella skapas:

```
Created config policy/voting/default to revision 142118
Created config destination-rule/voting/voting-app at revision 142119
Created config destination-rule/voting/voting-analytics at revision 142120
Created config virtual-service/voting/voting-analytics at revision 142121
Created config destination-rule/voting/voting-storage at revision 142122
Created config virtual-service/voting/voting-storage at revision 142123
```

Om du öppnar AKS röstningsapp i en webbläsare, endast den nya versionen *1.1* av den *rösta analytics* komponent som används av den *rösta app* komponent.

![Version 1.1 av komponenten analytics som körs i vår AKS rösta app.](media/istio/update-app-01.png)

Du kan enkelt visualisera att vi nu bara dirigeras till version *1.1* av din *rösta analytics* komponent på följande sätt. Kom ihåg att använda IP-adressen för din Istio ingress-gateway.

Du kan visualisera att du nu bara dirigeras till version *1.1* av din *rösta analytics* komponent på följande sätt. Kom ihåg att använda IP-adressen för en egen Istio Ingress-Gateway:

```azurecli
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

Följande Exempelutdata visar den aktuella delen av webbplatsen returnerade:

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Bekräfta att Istio använder ömsesidig TLS för att skydda kommunikationen mellan var och en av våra tjänster. Följande kommandon Kontrollera TLS-inställningarna för var och en av de *rösta app* tjänster:

```console
istioctl authn tls-check voting-app.voting.svc.cluster.local
istioctl authn tls-check voting-analytics.voting.svc.cluster.local
istioctl authn tls-check voting-storage.voting.svc.cluster.local
```

Den här följande Exempelutdata visar att ömsesidig TLS tillämpas för varje tjänst via principer och regler för mål:

```
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Distribuera en kontrollvärde version av programmet

Nu ska vi distribuera en ny version *2.0* av den *rösta app*, *rösta analytics*, och *rösta storage* komponenter. Den nya *rösta storage* komponent använder MySQL i stället för Redis och *rösta app* och *rösta analytics* komponenter uppdateras så att de kan använda den här nya *rösta storage* komponent.

Den *rösta app* komponenten stöder nu flaggan för funktionen. Den här flaggan för funktionen kan du testa canary release möjligheterna för Istio för en delmängd användare.

Följande diagram visar vad går att köra i slutet av det här avsnittet.

* Version *1.0* av den *rösta app* komponenten, version *1.1* av den *rösta analytics* komponent och version  *1.0* av den *rösta storage* komponent ska kunna kommunicera med varandra.
* Version *2.0* av den *rösta app* komponenten, version *2.0* av den *rösta analytics* komponent och version  *2.0* av den *rösta storage* komponent ska kunna kommunicera med varandra.
* Version *2.0* av den *rösta app* komponent är endast tillgängliga för användare som har en specifik funktion-flaggan inställd. Den här ändringen hanteras med hjälp av en funktionsflagga via en cookie.

![AKS rösta appkomponenter och routning.](media/istio/components-and-routing-03.png)

Uppdatera först Istio mål regler och tjänster för virtuella att serva för dessa nya komponenter. De här uppdateringarna se till att du inte dirigerar trafik felaktigt till de nya komponenterna och användarna inte får oväntat åtkomst:

```console
istioctl replace -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

Följande Exempelutdata visar de mål regler och tjänster för virtuella har uppdaterats:

```
Updated config destination-rule/voting/voting-app to revision 150930
Updated config virtual-service/voting/voting-app to revision 150931
Updated config destination-rule/voting/voting-analytics to revision 150937
Updated config virtual-service/voting/voting-analytics to revision 150939
Updated config destination-rule/voting/voting-storage to revision 150940
Updated config virtual-service/voting/voting-storage to revision 150941
```

Nu ska vi lägga till Kubernetes-objekten för den nya versionen *2.0* komponenter. Du även uppdatera den *rösta storage* så att den *3306* porten för MySQL:

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

Följande Exempelutdata visar Kubernetes-objekten har uppdaterats eller skapat:

```
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Vänta tills versionen *2.0* poddar körs. Använd den [kubectl hämta poddar] [ kubectl-get] kommando för att visa alla poddar i den *röstande* namnområde:

```azurecli
kubectl get pods --namespace voting
```

Du ska nu kunna växla mellan versionen *1.0* och version *2.0* (kontrollvärde) i röstningsprogrammet. Funktionen flaggan växlingsknappen längst ned på skärmen anger en cookie. Cookien används av den *rösta app* virtuella-tjänsten för att dirigera användare till den nya versionen *2.0*.

![Version 1.0 av AKS rösta-app – funktionsflagga har inte angetts.](media/istio/canary-release-01.png)

![Version 2.0 av appen AKS rösta - funktionen flaggan är inställd.](media/istio/canary-release-02.png)

Röst antalet skiljer sig mellan versioner av appen. Detta visar att du använder två olika lagringsenheter serverdelar.

## <a name="finalize-the-rollout"></a>Slutför installationen

När du har har testat canary release, uppdatera den *rösta app* virtuella-tjänsten för att dirigera all trafik till version *2.0* av den *rösta app* komponent. Alla användare se sedan version *2.0* av programmet, oavsett om funktionsflagga har angetts eller inte:

![AKS rösta appkomponenter och routning.](media/istio/components-and-routing-04.png)

Uppdatera alla mål regler för att ta bort versionerna av komponenterna du vill inte längre aktiv. Sedan uppdatera alla virtuella tjänster om du vill stoppa refererar till dessa versioner.

Eftersom det finns inte längre all trafik till någon av de äldre versionerna av komponenter, kan du nu ta bort alla distributioner för dessa komponenter.

![AKS rösta appkomponenter och routning.](media/istio/components-and-routing-05.png)

Du nu distribuerat har en ny version av appen rösta AKS.

## <a name="next-steps"></a>Nästa steg

Du kan utforska ytterligare scenarier med hjälp av den [Istio Bookinfo program exempel][istio-bookinfo-example].

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/spec-requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#Gateway
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#VirtualService
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#DestinationRule
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./istio-install.md