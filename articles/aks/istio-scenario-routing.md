---
title: Intelligent Routning och Kanarie-versioner med Istio i Azure Kubernetes service (AKS)
description: Lär dig hur du använder Istio för att tillhandahålla intelligent Routning och distribuera Kanarie-versioner i ett Azure Kubernetes service-kluster (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 7baa2adbd615a449c73e70e1b96524fc1e18b25d
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000177"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Använda intelligent Routning och Kanarie-versioner med Istio i Azure Kubernetes service (AKS)

[Istio][istio-github] är ett nät med öppen källkod som ger en nyckel uppsättning över mikrotjänster i ett Kubernetes-kluster. Dessa funktioner omfattar trafik hantering, tjänst identitet och säkerhet, princip tillämpning och upprätthållande. Mer information om Istio finns i den officiella [Istio?][istio-docs-concepts] -dokumentationen.

Den här artikeln visar hur du använder funktionerna för trafik hantering i Istio. Ett exempel på en AKS röstnings app används för att utforska intelligenta routing-och Kanarie-versioner.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Distribuera programmet
> * Uppdatera programmet
> * Distribuera en Kanarie-version av programmet
> * Slutför distributionen

## <a name="before-you-begin"></a>Innan du börjar

> [!NOTE]
> Det här scenariot har testats mot `1.1.3`Istio-versionen.

De steg som beskrivs i den här artikeln förutsätter att du har skapat ett `1.11` AKS-kluster (Kubernetes och senare, med RBAC aktiverat `kubectl` ) och har upprättat en anslutning till klustret. Du måste också ha Istio installerat i klustret.

Om du behöver hjälp med något av dessa objekt kan du läsa snabb starten för [AKS][aks-quickstart] och [Installera Istio i AKS][istio-install] -vägledningen.

## <a name="about-this-application-scenario"></a>Om det här program scenariot

Exemplet på AKS röstning innehåller två röstnings alternativ (**katter** eller **hundar**) till användare. Det finns en lagrings komponent som behåller antalet röster för varje alternativ. Det finns dessutom en analys komponent som innehåller information kring de röster som omvandlas för varje alternativ.

I det här program scenariot börjar du med att `1.0` distribuera versionen av röstnings appen `1.0` och versionen av Analytics-komponenten. Analytics-komponenten ger enkla räknare för antalet röster. Röstnings appen och analys komponenten interagerar `1.0` med versionen av Storage-komponenten, som backas upp av Redis.

Du uppgraderar Analytics-komponenten till version `1.1`, som innehåller antal och nu summor och procent.

En delmängd användare test version `2.0` av appen via en Kanarie-version. Den här nya versionen använder en lagrings komponent som backas upp av en MySQL-databas.

När du är säker på att `2.0` versionen fungerar som förväntat på din delmängd av användarna kan `2.0` du distribuera versionen till alla dina användare.

## <a name="deploy-the-application"></a>Distribuera programmet

Vi börjar med att distribuera programmet till ditt Azure Kubernetes service-kluster (AKS). Följande diagram visar vad som körs i slutet av det här avsnittet – versionen `1.0` av alla komponenter med inkommande begär Anden som servas via Istio ingress Gateway:

![AKS röstnings program komponenter och routning.](media/istio/components-and-routing-01.png)

De artefakter som du måste följa tillsammans med den här artikeln finns i avsnittet [Azure-samples/AKS-röstning-app][github-azure-sample] GitHub lagrings platsen. Du kan antingen hämta artefakterna eller klona lagrings platsen på följande sätt:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Ändra till följande mapp i den nedladdade/klonade lagrings platsen och kör alla efterföljande steg från den här mappen:

```console
cd scenarios/intelligent-routing-with-istio
```

Börja med att skapa ett namn område i AKS-klustret för exempel AKS röstnings `voting` appen enligt följande:

```azurecli
kubectl create namespace voting
```

Namnge namn området med `istio-injection=enabled`. Den här etiketten instruerar Istio att automatiskt mata in Istio-proxyservrarna som sidvagn i alla dina poddar i det här namn området.

```azurecli
kubectl label namespace voting istio-injection=enabled
```

Nu ska vi skapa komponenterna för AKS röstning-appen. Skapa de här komponenterna i `voting` namn området som du skapade i föregående steg.

```azurecli
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

Följande exempel på utdata visar de resurser som skapas:

```console
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio har vissa särskilda krav kring poddar och tjänster. Mer information finns i Istio- [krav för poddar-och tjänst dokumentation][istio-requirements-pods-and-services].

Om du vill se poddar som har skapats använder du kommandot [kubectl get poddar][kubectl-get] enligt följande:

```azurecli
kubectl get pods -n voting
```

Följande exempel på utdata visar att `voting-app` det finns tre instanser av Pod och en enda instans av `voting-analytics` både och `voting-storage` poddar. Varje poddar har två behållare. En av dessa behållare är komponenten och den andra är `istio-proxy`:

```console
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s
```

Om du vill se information om Pod använder du [kubectl Beskrivning Pod][kubectl-describe]. Ersätt namnet på Pod med namnet på en POD i ditt eget AKS-kluster från föregående utdata:

```azurecli
kubectl describe pod voting-app-1-0-956756fd-d5w7z --namespace voting
```

`istio-proxy` Behållaren har automatiskt matats in av Istio för att hantera nätverks trafiken till och från dina komponenter, som du ser i följande exempel utdata:

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         docker.io/istio/proxyv2:1.1.3
[...]
```

Du kan inte ansluta till röstnings appen förrän du har skapat Istio- [gatewayen][istio-reference-gateway] och den [virtuella tjänsten][istio-reference-virtualservice]. Dessa Istio-resurser dirigerar trafik från standardvärdet för Istio-inkommande Gateway till vårt program.

> [!NOTE]
> En **Gateway** är en komponent i kanten av service nätet som tar emot inkommande eller utgående http-och TCP-trafik.
> 
> En **virtuell tjänst** definierar en uppsättning regler för routning av en eller flera mål tjänster.

`kubectl apply` Använd kommandot för att distribuera gatewayen och yaml för virtuella tjänster. Kom ihåg att ange det namn område som resurserna distribueras till.

```azurecli
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

Följande exempel på utdata visar den nya gatewayen och den virtuella tjänst som skapas:

```console
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Hämta IP-adressen för Istio-gatewayen med hjälp av följande kommando:

```azurecli
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

Följande exempel på utdata visar IP-adressen för den inkommande gatewayen:

```
20.188.211.19
```

Öppna en webbläsare och klistra in den i IP-adressen. Exemplet på AKS röstning visas.

![AKS röstnings app som körs i vårt Istio-aktiverade AKS-kluster.](media/istio/deploy-app-01.png)

Informationen längst ned på skärmen visar att appen använder `1.0` version av `voting-app` och version `1.0` av `voting-storage` (Redis).

## <a name="update-the-application"></a>Uppdatera programmet

Nu ska vi distribuera en ny version av Analytics-komponenten. Den här nya `1.1` versionen visar summor och procent satser utöver antalet för varje kategori.

Följande diagram visar vad som kommer att köras i slutet av den här delen – endast versionen `1.1` av vår `voting-analytics` komponent har `voting-app` trafik som dirigerats från komponenten. Även om versionen `1.0` av vår `voting-analytics` komponent fortsätter att köras och refereras av `voting-analytics` tjänsten, tillåter Istio-proxyservrarna trafik till och från den.

![AKS röstnings program komponenter och routning.](media/istio/components-and-routing-02.png)

Nu ska vi distribuera `1.1` version `voting-analytics` av komponenten. Skapa den här komponenten i `voting` namn området:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

Följande exempel på utdata visar de resurser som skapas:

```console
deployment.apps/voting-analytics-1-1 created
```

Öppna AKS röstnings app i en webbläsare igen med hjälp av IP-adressen för Istio ingress gateway som hämtades i föregående steg.

Webbläsaren växlar mellan de två vyer som visas nedan. Eftersom du använder en Kubernetes [-tjänst][kubernetes-service] för `voting-analytics` komponenten med endast en enda etikett väljare (`app: voting-analytics`) använder Kubernetes standard beteendet för resursallokering mellan den poddar som matchar den väljaren. I det här fallet är det både version `1.0` och `1.1` `voting-analytics` poddar.

![Version 1,0 av analys komponenten som körs i vår AKS röstnings app.](media/istio/deploy-app-01.png)

![Version 1,1 av analys komponenten som körs i vår AKS röstnings app.](media/istio/update-app-01.png)

Du kan visualisera växlingen mellan de två versionerna av `voting-analytics` komponenten på följande sätt. Kom ihåg att använda IP-adressen för din egen Istio ingress-Gateway.

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

Powershell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

Följande exempel på utdata visar relevant del av den returnerade webbplatsen som plats växlar mellan versioner:

```
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Lås trafik till version 1,1 av programmet

Nu ska vi låsa `1.1` trafiken till endast version `voting-analytics` av komponenten `voting-storage` och till version `1.0` av komponenten. Du definierar sedan routningsregler för alla andra komponenter.

> * En **virtuell tjänst** definierar en uppsättning regler för routning av en eller flera mål tjänster.
> * En **mål regel** definierar Traffic policies-och versions principer.
> * En **princip** definierar vilka autentiseringsmetoder som kan godkännas för arbets belastning (ar).

Använd kommandot för att ersätta den virtuella tjänst definitionen `voting-app` i och lägga till [mål regler][istio-reference-destinationrule] och [virtuella tjänster][istio-reference-virtualservice] för de andra komponenterna. `kubectl apply` Du lägger till en [princip][istio-reference-policy] i `voting` namn området för att säkerställa att all kommunikation mellan tjänster skyddas med ömsesidig TLS och klient certifikat.

* Principen har `peers.mtls.mode` ställt in för `STRICT` att säkerställa att ömsesidig TLS upprätthålls `voting` mellan dina tjänster i namn området.
* Vi anger `trafficPolicy.tls.mode` också till `ISTIO_MUTUAL` i alla våra mål regler. Istio tillhandahåller tjänster med starka identiteter och skyddar kommunikationen mellan tjänster med ömsesidiga TLS-och klient certifikat som Istio transparent hanterar.

```azurecli
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

Följande exempel på utdata visar den nya principen, mål reglerna och de virtuella tjänster som uppdateras/skapas:

```console
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Om du öppnar AKS röstnings app i en webbläsare igen används `1.1` `voting-app` bara den nya versionen `voting-analytics` av komponenten.

![Version 1,1 av analys komponenten som körs i vår AKS röstnings app.](media/istio/update-app-01.png)

Du kan visualisera att du nu bara dirigeras till versionen `1.1` `voting-analytics` av-komponenten enligt följande. Kom ihåg att använda IP-adressen för din egen Istio ingress-Gateway:

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

Powershell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

Följande exempel på utdata visar relevant del av den returnerade webbplatsen:

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Nu ska vi bekräfta att Istio använder ömsesidig TLS för att säkra kommunikationen mellan var och en av våra tjänster. För det här ska vi använda kommandot [authn TLS-check][istioctl-authn-tls-check] på `istioctl` klientens binärfil, som har följande formulär.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Den här uppsättningen kommandon ger information om åtkomst till de angivna tjänsterna, från alla poddar som finns i ett namn område och matchar en uppsättning etiketter:

Bash

```bash
# mTLS configuration between each of the istio ingress pods and the voting-app service
kubectl get pod -n istio-system -l app=istio-ingressgateway | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.istio-system voting-app.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-analytics service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-analytics.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-storage service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
kubectl get pod -n voting -l app=voting-analytics,version=1.1 | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local
```

Powershell

```powershell
# mTLS configuration between each of the istio ingress pods and the voting-app service
(kubectl get pod -n istio-system -l app=istio-ingressgateway | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".istio-system") voting-app.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-analytics service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-analytics.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-analytics,version=1.1 | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }
```

I följande exempel på utdata visas att ömsesidig TLS upprätthålls för var och en av våra frågor ovan. Utdata visar också principen och mål reglerna som tillämpar ömsesidig TLS:

```console
# mTLS configuration between istio ingress pods and the voting-app service
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

# mTLS configuration between each of the voting-app pods and the voting-analytics service
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

# mTLS configuration between each of the voting-app pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Distribuera en Kanarie-version av programmet

Nu ska vi distribuera en ny version `2.0` `voting-app`av-, `voting-analytics`-och `voting-storage` -komponenterna. Den nya `voting-storage` komponenten använder mysql i stället för Redis `voting-analytics` , `voting-app` och-komponenterna uppdateras så att de kan använda den här nya `voting-storage` komponenten.

`voting-app` Komponenten stöder nu funktioner för funktions flagga. Med den här funktions flaggan kan du testa funktionen för Istio för en delmängd av användarna.

Följande diagram visar vad du kommer att ha i slutet av det här avsnittet.

* Versionen `1.0` `1.1` `1.0` `voting-storage` av `voting-app` komponenten,versionenavkomponentenoch`voting-analytics` versionen av komponenten kan kommunicera med varandra.
* Versionen `2.0` `2.0` `2.0` `voting-storage` av `voting-app` komponenten,versionenavkomponentenoch`voting-analytics` versionen av komponenten kan kommunicera med varandra.
* `2.0` Versionen`voting-app` av komponenten är endast tillgänglig för användare som har en angiven funktions flagga angiven. Den här ändringen hanteras med en funktions flagga via en cookie.

![AKS röstnings program komponenter och routning.](media/istio/components-and-routing-03.png)

Börja med att uppdatera Istio-destinations reglerna och de virtuella tjänsterna så att de passar dessa nya komponenter. De här uppdateringarna säkerställer att du inte dirigerar trafik felaktigt till de nya komponenterna och att användarna inte får oväntad åtkomst:

```azurecli
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

Följande exempel på utdata visar de mål regler och virtuella tjänster som uppdateras:

```console
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Nu ska vi lägga till Kubernetes-objekten för de nya versions `2.0` komponenterna. Du uppdaterar `voting-storage` också tjänsten så att den `3306` inkluderar porten för MySQL:

```azurecli
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

Följande exempel på utdata visar att Kubernetes-objekten har uppdaterats eller skapats:

```console
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Vänta tills alla versions `2.0` poddar körs. Använd kommandot [kubectl get poddar][kubectl-get] för att visa alla poddar i `voting` namn området:

```azurecli
kubectl get pods --namespace voting
```

Nu bör du kunna växla mellan version `1.0` och version `2.0` (Kanarie) för röstnings programmet. Funktions flaggan växla längst ned på skärmen anger en cookie. Den här cookien används av `voting-app` den virtuella tjänsten för att dirigera användare till den `2.0`nya versionen.

![Version 1,0 av AKS röstning-appen-funktions flagga har inte angetts.](media/istio/canary-release-01.png)

![Version 2,0 av AKS röstning app – funktions flagga har angetts.](media/istio/canary-release-02.png)

Antalet röstningar skiljer sig mellan versionerna av appen. Den här skillnaden fokuserar på att du använder två olika lagrings Server delar.

## <a name="finalize-the-rollout"></a>Slutför distributionen

När du har testat Kanarie-versionen uppdaterar du den `voting-app` virtuella tjänsten för att dirigera all trafik till `2.0` version av `voting-app` komponenten. Alla användare ser sedan versionen `2.0` av programmet, oavsett om funktions flaggan är inställd eller inte:

![AKS röstnings program komponenter och routning.](media/istio/components-and-routing-04.png)

Uppdatera alla mål regler för att ta bort de versioner av de komponenter som du inte längre vill ha aktiva. Uppdatera sedan alla virtuella tjänster för att sluta referera till dessa versioner.

Eftersom det inte längre finns någon trafik till någon av de äldre versionerna av komponenterna kan du nu på ett säkert sätt ta bort alla distributioner för dessa komponenter.

![AKS röstnings program komponenter och routning.](media/istio/components-and-routing-05.png)

Nu har du distribuerat en ny version av AKS röstnings appen.

## <a name="clean-up"></a>Rensa 

Du kan ta bort AKS röstnings appen som vi använde i det här scenariot från ditt AKS `voting` -kluster genom att ta bort namn området enligt följande:

```azurecli
kubectl delete namespace voting
```

Följande exempel på utdata visar att alla komponenter i AKS röstnings program har tagits bort från ditt AKS-kluster.

```console
namespace "voting" deleted
```

## <a name="next-steps"></a>Nästa steg

Du kan utforska ytterligare scenarier med hjälp av [Istio Bookinfo-programexempelet][istio-bookinfo-example].

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio-github]: https://github.com/istio/istio

[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/prepare/requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/networking/v1alpha3/gateway/
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/networking/v1alpha3/virtual-service/
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/networking/v1alpha3/destination-rule/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[istioctl-authn-tls-check]: https://istio.io/docs/reference/commands/istioctl/#istioctl-authn-tls-check

[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./istio-install.md
