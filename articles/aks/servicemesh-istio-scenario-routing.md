---
title: Använd Istio för intelligent routning
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du använder Istio för att tillhandahålla intelligent routning och distribuera kanarieversioner i ett AKS-kluster (Azure Kubernetes Service)
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 01a7764eb0a353e6842441093f70ad29c9316bbd
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668277"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Använda intelligenta routnings- och kanarieversioner med Istio i Azure Kubernetes Service (AKS)

[Istio][istio-github] är ett tjänstnät med öppen källkod som tillhandahåller en nyckeluppsättning funktioner över mikrotjänsterna i ett Kubernetes-kluster. Dessa funktioner omfattar trafikhantering, tjänstidentitet och säkerhet, principefterlevnad och observerbarhet. Mer information om Istio finns i dokumentationen [Vad är Istio?][istio-docs-concepts]

Den här artikeln visar hur du använder trafikhanteringsfunktionen i Istio. Ett exempel på AKS-röstningsapp används för att utforska intelligent routing och kanariefågelversioner.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Distribuera programmet
> * Uppdatera programmet
> * Rulla ut en kanariefågel release av ansökan
> * Slutföra distributionen

## <a name="before-you-begin"></a>Innan du börjar

> [!NOTE]
> Det här scenariot har testats `1.3.2`mot Istio-versionen .

Stegen som beskrivs i den här artikeln förutsätter att `1.13` du har skapat ett AKS-kluster `kubectl` (Kubernetes och högre, med RBAC aktiverat) och har upprättat en anslutning till klustret. Du måste också installera Istio i klustret.

Om du behöver hjälp med något av dessa objekt kan du se [AKS-snabbstarten][aks-quickstart] och [installera Istio i][istio-install] AKS-vägledningen.

## <a name="about-this-application-scenario"></a>Om det här programscenariot

Exemplet AKS röstningsapp innehåller två röstningsalternativ (**Katter** eller **Hundar**) för användare. Det finns en lagringskomponent som behåller antalet röster för varje alternativ. Dessutom finns det en analyskomponent som innehåller information om de avgivna rösterna för varje alternativ.

I det här programscenariot `1.0` börjar du med att `1.0` distribuera version av röstningsappen och versionen av analyskomponenten. Analyskomponenten ger enkla antal röster. Röstningsappen och analyskomponenten `1.0` interagerar med en version av lagringskomponenten, som backas upp av Redis.

Du uppgraderar analyskomponenten till version `1.1`, som ger antal och nu summor och procentsatser.

En delmängd av `2.0` användarna testar versionen av appen via en kanariefågelversion. Den här nya versionen använder en lagringskomponent som backas upp av en MySQL-databas.

När du är säker `2.0` på att versionen fungerar som förväntat på `2.0` din delmängd av användare, du distribuerar version till alla dina användare.

## <a name="deploy-the-application"></a>Distribuera programmet

Låt oss börja med att distribuera programmet till ditt AKS-kluster (Azure Kubernetes Service). Följande diagram visar vad som körs i `1.0` slutet av det här avsnittet - version av alla komponenter med inkommande begäranden som betjänas via Ingress-gatewayen i Istio:

![AKS-appens komponenter och routning.](media/servicemesh/istio/scenario-routing-components-01.png)

De artefakter du behöver följa tillsammans med den här artikeln är tillgängliga i [Azure-Samples/aks-voting-app][github-azure-sample] GitHub repo. Du kan antingen ladda ner artefakter eller klona repoen enligt följande:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Ändra till följande mapp i den nedladdade / klonade repo och köra alla efterföljande steg från denna mapp:

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

Skapa först ett namnområde i AKS-klustret för `voting` exempelappen AKS med namnet AKS med namnet:

```console
kubectl create namespace voting
```

Märk namnområdet `istio-injection=enabled`med . Den här etiketten instruerar Istio att automatiskt injicera istio-proxyservrar som sidovagnar i alla dina poddar i detta namnområde.

```console
kubectl label namespace voting istio-injection=enabled
```

Nu ska vi skapa komponenterna för AKS-röstningsappen. Skapa dessa komponenter `voting` i namnområdet som skapats i ett tidigare steg.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

Följande exempelutdata visar de resurser som skapas:

```output
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio har vissa specifika krav kring poddar och tjänster. Mer information finns i [dokumentationen för Istio-krav för poddar och tjänster][istio-requirements-pods-and-services].

Om du vill se de poddar som har skapats använder du kommandot [kubectl get pods][kubectl-get] enligt följande:

```console
kubectl get pods -n voting --show-labels
```

Följande exempelutdata visar att det `voting-app` finns tre instanser av `voting-analytics` `voting-storage` podden och en enda instans av både poddar och poddar. Var och en av kapslarna har två behållare. En av dessa behållare är komponenten, `istio-proxy`och den andra är:

```output
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

Om du vill se information om podden använder vi kommandot [kubectl][kubectl-describe] `voting-analytics` describe pod med etikettväljare för att välja podden. Vi filtrerar utdata för att visa information om de två behållarna som finns i podden:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

Du kan inte ansluta till röstningsappen förrän du har skapat Istio [Gateway][istio-reference-gateway] och [Virtual Service][istio-reference-virtualservice]. Dessa Istio-resurser dirigerar trafik från den standarda Istio-ingående gatewayen till vårt program.

> [!NOTE]
> En **gateway** är en komponent i utkanten av tjänstnätet som tar emot inkommande eller utgående HTTP- och TCP-trafik.
> 
> En **virtuell tjänst** definierar en uppsättning routningsregler för en eller flera måltjänster.

Använd `kubectl apply` kommandot för att distribuera yaml för gateway och virtuell tjänst. Kom ihåg att ange det namnområde som dessa resurser distribueras till.

```console
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

Följande exempelutdata visar den nya gateway- och virtuella tjänsten som skapas:

```output
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Hämta IP-adressen för Istio Ingress Gateway med följande kommando:

```output
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

Följande exempelutdata visar IP-adressen för Ingress Gateway:

```output
20.188.211.19
```

Öppna en webbläsare och klistra in IP-adressen. Exempelappen AKS-röstning visas.

![AKS-röstningsappen som körs i vårt Istio-aktiverat AKS-kluster.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

Informationen längst ned på skärmen visar att `1.0` appen `voting-app` använder `1.0` `voting-storage` version av och version av (Redis).

## <a name="update-the-application"></a>Uppdatera programmet

Nu ska vi distribuera en ny version av analyskomponenten. Den här `1.1` nya versionen visar summor och procentsatser utöver antalet för varje kategori.

Följande diagram visar vad som kommer att köras `1.1` i `voting-analytics` slutet av det här `voting-app` avsnittet - endast version av vår komponent har trafik dirigeras från komponenten. Även om `1.0` versionen `voting-analytics` av vår komponent fortsätter att `voting-analytics` köras och refereras av tjänsten, tillåter Istio trafik till och från den.

![AKS-appens komponenter och routning.](media/servicemesh/istio/scenario-routing-components-02.png)

Nu ska vi `1.1` distribuera `voting-analytics` en version av komponenten. Skapa den här `voting` komponenten i namnområdet:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

Följande exempelutdata visar de resurser som skapas:

```output
deployment.apps/voting-analytics-1-1 created
```

Öppna exempelappen AKS-röstning i en webbläsare igen med hjälp av IP-adressen för Istio Ingress Gateway som erhölls i föregående steg.

Din webbläsare växlar mellan de två vyerna som visas nedan. Eftersom du använder en Kubernetes-tjänst för komponenten [Service][kubernetes-service] `voting-analytics` med`app: voting-analytics`endast en enda etikettväljare ( ) använder Kubernetes standardbeteendet för avrundning mellan poddar som matchar den väljaren. I det här fallet `1.0` är `1.1` det `voting-analytics` både version och av dina poddar.

![Version 1.0 av analyskomponenten som körs i vår AKS-röstningsapp.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![Version 1.1 av analyskomponenten som körs i vår AKS-röstningsapp.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Du kan visualisera växlingen mellan `voting-analytics` de två versionerna av komponenten enligt följande. Kom ihåg att använda IP-adressen för din egen Istio Ingress Gateway.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

Följande exempelutdata visar den relevanta delen av den returnerade webbplatsen när webbplatsen växlar mellan versioner:

```output
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Låsa trafiken till version 1.1 av ansökan

Nu ska vi låsa trafiken `1.1` till `voting-analytics` endast version `1.0` av `voting-storage` komponenten och till versionen av komponenten. Du definierar sedan routningsregler för alla andra komponenter.

> * En **virtuell tjänst** definierar en uppsättning routningsregler för en eller flera måltjänster.
> * En **målregel** definierar trafikprinciper och versionsspecifika principer.
> * En **princip** definierar vilka autentiseringsmetoder som kan accepteras på arbetsbelastningar.

Använd `kubectl apply` kommandot för att ersätta definitionen `voting-app` av virtuell tjänst på din och lägga till [målregler][istio-reference-destinationrule] och [virtuella tjänster][istio-reference-virtualservice] för de andra komponenterna. Du lägger [Policy][istio-reference-policy] till `voting` en princip i namnområdet för att säkerställa att all kommunikation mellan tjänster skyddas med hjälp av ömsesidiga TLS- och klientcertifikat.

* Principen har `peers.mtls.mode` angett `STRICT` att säkerställa att ömsesidig TLS tillämpas `voting` mellan dina tjänster inom namnområdet.
* Vi ställer `trafficPolicy.tls.mode` också `ISTIO_MUTUAL` in till i alla våra destinationsregler. Istio tillhandahåller tjänster med starka identiteter och säkrar kommunikation mellan tjänster med hjälp av ömsesidiga TLS- och klientcertifikat som Istio hanterar transparent.

```console
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

Följande exempelutdata visar den nya principen, målreglerna och virtuella tjänster som uppdateras/skapas:

```output
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Om du öppnar AKS-röstningsappen i en `1.1` webbläsare `voting-analytics` igen används bara `voting-app` den nya versionen av komponenten av komponenten.

![Version 1.1 av analyskomponenten som körs i vår AKS-röstningsapp.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Du kan visualisera att du nu `1.1` bara `voting-analytics` dirigeras till en version av komponenten enligt följande. Kom ihåg att använda IP-adressen för din egen Istio Ingress Gateway:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

Följande exempelutdata visar den relevanta delen av den returnerade webbplatsen:

```output
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Låt oss nu bekräfta att Istio använder ömsesidig TLS för att säkra kommunikation mellan var och en av våra tjänster. För detta kommer vi att använda [kommandot authn tls-check][istioctl-authn-tls-check] på klientbinären, `istioctl` som tar följande form.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Den här uppsättningen kommandon innehåller information om åtkomsten till de angivna tjänsterna, från alla poddar som finns i ett namnområde och matchar en uppsättning etiketter:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

Följande exempelutdata visar att ömsesidig TLS tillämpas för var och en av våra frågor ovan. Resultatet visar också policy- och målreglerna som tillämpar den ömsesidiga TLS:a

```output
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

## <a name="roll-out-a-canary-release-of-the-application"></a>Rulla ut en kanariefågel release av ansökan

Nu ska vi distribuera `2.0` en `voting-app`ny `voting-analytics`version `voting-storage` av , och komponenter. Den `voting-storage` nya komponenten använder MySQL i `voting-app` `voting-analytics` stället för Redis och komponenterna uppdateras så att de kan använda den nya `voting-storage` komponenten.

Komponenten `voting-app` stöder nu funktionen flaggfunktioner. Med den här funktionsflaggan kan du testa Istios kanariefågelversionskapacitet för en delmängd användare.

Följande diagram visar vad du kommer att ha igång i slutet av det här avsnittet.

* Version `1.0` av `voting-app` komponenten, `1.1` `voting-analytics` version av `1.0` komponenten och versionen av komponenten `voting-storage` kan kommunicera med varandra.
* Version `2.0` av `voting-app` komponenten, `2.0` `voting-analytics` version av `2.0` komponenten och versionen av komponenten `voting-storage` kan kommunicera med varandra.
* Versionen `2.0` av `voting-app` komponenten är endast tillgänglig för användare som har en specifik funktionsflagguppsättning. Den här ändringen hanteras med hjälp av en funktionsflagga via en cookie.

![AKS-appens komponenter och routning.](media/servicemesh/istio/scenario-routing-components-03.png)

Uppdatera först Istio-målreglerna och virtuella tjänster för att tillgodose dessa nya komponenter. Dessa uppdateringar säkerställer att du inte dirigerar trafiken felaktigt till de nya komponenterna och att användarna inte får oväntad åtkomst:

```console
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

Följande exempelutdata visar målreglerna och virtuella tjänster som uppdateras:

```output
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Nu ska vi lägga till Kubernetes-objekten för de nya versionskomponenterna. `2.0` Du kan `voting-storage` också uppdatera `3306` tjänsten så att den inkluderar porten för MySQL:

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

Följande exempelutdata visar att Kubernetes-objekten har uppdaterats eller skapats:

```output
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Vänta tills `2.0` alla versionskapslar körs. Använd kommandot [kubectl get pods][kubectl-get] med `-w` klockväxeln för att `voting` titta efter ändringar på alla poddar i namnområdet:

```console
kubectl get pods --namespace voting -w
```

Du bör nu kunna växla `1.0` mellan `2.0` version och version (kanariefågel) av röstningsprogrammet. Funktionsflaggan växla längst ned på skärmen ställer in en cookie. Den här cookien `voting-app` används av den virtuella `2.0`tjänsten för att dirigera användare till den nya versionen .

![Version 1.0 av AKS-röstningsappen - funktionsflaggan är INTE inställd.](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![Version 2.0 av AKS-röstningsappen – funktionsflaggan är inställd.](media/servicemesh/istio/scenario-routing-canary-release-02.png)

Rösträkningen skiljer sig mellan appversionerna. Den här skillnaden belyser att du använder två olika lagringsstämmare.

## <a name="finalize-the-rollout"></a>Slutföra distributionen

När du har testat kanariefågelversionen uppdaterar `voting-app` du den virtuella tjänsten `2.0` för `voting-app` att dirigera all trafik till en version av komponenten. Alla användare ser `2.0` sedan versionen av programmet, oavsett om funktionsflaggan är inställd eller inte:

![AKS-appens komponenter och routning.](media/servicemesh/istio/scenario-routing-components-04.png)

Uppdatera alla målregler för att ta bort versionerna av de komponenter som du inte längre vill ha aktiva. Uppdatera sedan alla virtuella tjänster för att sluta referera till dessa versioner.

Eftersom det inte längre finns någon trafik till någon av de äldre versionerna av komponenterna kan du nu ta bort alla distributioner för dessa komponenter på ett säkert sätt.

![AKS-appens komponenter och routning.](media/servicemesh/istio/scenario-routing-components-05.png)

Du har nu rullat ut en ny version av AKS Voting App.

## <a name="clean-up"></a>Rensa 

Du kan ta bort AKS-röstningsappen som vi använde `voting` i det här scenariot från AKS-klustret genom att ta bort namnområdet enligt följande:

```console
kubectl delete namespace voting
```

Följande exempelutdata visar att alla komponenter i AKS-röstningsappen har tagits bort från AKS-klustret.

```output
namespace "voting" deleted
```

## <a name="next-steps"></a>Nästa steg

Du kan utforska ytterligare scenarier med hjälp av [exemplet Istio Bookinfo-program][istio-bookinfo-example].

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
[istio-install]: ./servicemesh-istio-install.md
