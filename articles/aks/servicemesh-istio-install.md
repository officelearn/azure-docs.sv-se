---
title: Installera Istio i Azure Kubernetes service (AKS)
description: Lär dig hur du installerar och använder Istio för att skapa ett service nät i ett Azure Kubernetes service-kluster (AKS)
author: paulbouwer
ms.topic: article
ms.date: 02/19/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: f0fe4ab46bfe5c0c0c2ea67aa2e2694321628be5
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136371"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Installera och använda Istio i Azure Kubernetes service (AKS)

[Istio][istio-github] är ett nät med öppen källkod som ger en nyckel uppsättning över mikrotjänster i ett Kubernetes-kluster. Dessa funktioner omfattar trafik hantering, tjänst identitet och säkerhet, princip tillämpning och upprätthållande. Mer information om Istio finns i den officiella [Istio?][istio-docs-concepts] -dokumentationen.

Den här artikeln visar hur du installerar Istio. Istio-`istioctl`-klientens binärfil installeras på klient datorn och Istio-komponenterna installeras i ett Kubernetes-kluster på AKS.

> [!NOTE]
> Följande instruktioner hänvisar till Istio-versionen `1.4.0`.
>
> Istio `1.4.x`-versioner har testats av Istio-teamet mot Kubernetes-versioner `1.13`, `1.14`, `1.15`. Du hittar fler Istio-versioner på [GitHub-Istio-][istio-github-releases]versioner, information om var och en av versionerna på [Istio nyheter][istio-release-notes] och Kubernetes-versioner som stöds på [Istio allmänna vanliga frågor och svar][istio-faq].

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Hämta och installera Istio istioctl-klientens binärfil
> * Installera Istio på AKS
> * Verifiera Istio-installationen
> * Få åtkomst till tilläggen
> * Avinstallera Istio från AKS

## <a name="before-you-begin"></a>Innan du börjar

De steg som beskrivs i den här artikeln förutsätter att du har skapat ett AKS-kluster (Kubernetes `1.13` och senare, med RBAC aktiverat) och har upprättat en `kubectl` anslutning till klustret. Om du behöver hjälp med något av dessa objekt kan du se snabb starten för [AKS][aks-quickstart].

Se till att du har läst dokumentationen för [Istio-prestanda och skalbarhet](https://istio.io/docs/concepts/performance-and-scalability/) för att förstå de ytterligare resurs kraven för att köra Istio i ditt AKS-kluster. Kärn-och minnes kraven varierar beroende på din särskilda arbets belastning. Välj ett lämpligt antal noder och storlek för virtuella datorer för din installation.

Den här artikeln separerar installations vägledningen för Istio till flera diskreta steg. Slut resultatet är detsamma i strukturen som den officiella Istio installations [vägledningen][istio-install-istioctl].

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-components-on-aks"></a>Installera Istio-komponenterna på AKS

Vi kommer att installera [Grafana][grafana] och [Kiali][kiali] som en del av vår Istio-installation. Grafana tillhandahåller instrument paneler för analys och övervakning, och Kiali tillhandahåller en instrument panel för service nät. I vår installation kräver var och en av dessa komponenter autentiseringsuppgifter som måste tillhandahållas som en [hemlighet][kubernetes-secrets].

Innan vi kan installera Istio-komponenterna måste vi skapa hemligheterna för både Grafana och Kiali. Dessa hemligheter måste installeras i `istio-system` namn området som används av Istio, så vi måste skapa namn området också. Vi måste använda alternativet `--save-config` när du skapar namn området via `kubectl create` så att installations programmet för Istio kan köra `kubectl apply` på det här objektet i framtiden.

```console
kubectl create namespace istio-system --save-config
```

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - create secrets for Grafana and Kiali](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash check for CRDs](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell check for CRDs](includes/servicemesh/istio/install-create-secrets-powershell.md)]

::: zone-end

### <a name="install-istio-components"></a>Installera Istio-komponenter

Nu när vi har skapat Grafana-och Kiali-hemligheterna i vårt AKS-kluster är det dags att installera Istio-komponenterna. 

Installations metoden för [Helm][helm] för Istio kommer att bli inaktuell i framtiden. Den nya installations metoden för Istio utnyttjar `istioctl`-klientens binärfil, [konfigurations profilerna för Istio][istio-configuration-profiles]och den nya [specifikationen och API: et för Istio Control plan][istio-control-plane]. Det här nya tillvägagångs sättet är vad vi ska använda för att installera Istio.

> [!NOTE]
> Istio måste för närvarande vara schemalagda att köras på Linux-noder. Om du har Windows Server-noder i klustret måste du se till att Istio-poddar endast är schemalagda att köras på Linux-noder. Vi använder [Node-väljare][kubernetes-node-selectors] för att kontrol lera att poddar är schemalagda till rätt noder.

> [!CAUTION]
> Funktionerna [SDS (Secret Discovery service)][istio-feature-sds] och [Istio cni][istio-feature-cni] Istio är för närvarande i [alpha][istio-feature-stages], så tänk på detta innan du aktiverar dem. Dessutom är Kubernetes-funktionen för [tjänst kontots token för volym projektion][kubernetes-feature-sa-projected-volume] (ett krav för SDS) inte aktive rad i aktuella AKS-versioner.
Skapa en fil med namnet `istio.aks.yaml` med följande innehåll. Den här filen innehåller [Specifikations informationen för Istio Control plan][istio-control-plane] för att konfigurera Istio.

```yaml
apiVersion: install.istio.io/v1alpha2
kind: IstioControlPlane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
      # Enable mutual TLS for the control plane
      controlPlaneSecurityEnabled: true
      mtls:
        # Require all service to service communication to have mtls
        enabled: false
    grafana:
      # Enable Grafana deployment for analytics and monitoring dashboards
      enabled: true
      security:
        # Enable authentication for Grafana
        enabled: true
    kiali:
      # Enable the Kiali deployment for a service mesh observability dashboard
      enabled: true
    tracing:
      # Enable the Jaeger deployment for tracing
      enabled: true
```

Installera istio med kommandot `istioctl apply` och Specifikations filen ovan `istio.aks.yaml` Istio kontroll plan enligt följande:

```console
istioctl manifest apply -f istio.aks.yaml --logtostderr --set installPackagePath=./install/kubernetes/operator/charts
```

Installations programmet kommer att distribuera ett antal [CRDs][kubernetes-crd] och sedan hantera beroenden för att installera alla relevanta objekt som definierats för den här konfigurationen av Istio. Du bör se något som liknar följande utdata-kodfragment.

```console
Applying manifests for these components:
- Tracing
- EgressGateway
- NodeAgent
- Grafana
- Policy
- Citadel
- CertManager
- IngressGateway
- Injector
- Prometheus
- PrometheusOperator
- Kiali
- Telemetry
- Galley
- Cni
- Pilot
- Base
- CoreDNS
NodeAgent is waiting on a prerequisite...
Telemetry is waiting on a prerequisite...
Galley is waiting on a prerequisite...
Cni is waiting on a prerequisite...
Grafana is waiting on a prerequisite...
Policy is waiting on a prerequisite...
Citadel is waiting on a prerequisite...
EgressGateway is waiting on a prerequisite...
Tracing is waiting on a prerequisite...
Kiali is waiting on a prerequisite...
PrometheusOperator is waiting on a prerequisite...
IngressGateway is waiting on a prerequisite...
Prometheus is waiting on a prerequisite...
CertManager is waiting on a prerequisite...
Injector is waiting on a prerequisite...
Pilot is waiting on a prerequisite...
Applying manifest for component Base
Waiting for CRDs to be applied.
CRDs applied.
Finished applying manifest for component Base
Prerequisite for Tracing has completed, proceeding with install.
Prerequisite for Injector has completed, proceeding with install.
Prerequisite for Telemetry has completed, proceeding with install.
Prerequisite for Policy has completed, proceeding with install.
Prerequisite for PrometheusOperator has completed, proceeding with install.
Prerequisite for NodeAgent has completed, proceeding with install.
Prerequisite for IngressGateway has completed, proceeding with install.
Prerequisite for Kiali has completed, proceeding with install.
Prerequisite for EgressGateway has completed, proceeding with install.
Prerequisite for Galley has completed, proceeding with install.
Prerequisite for Grafana has completed, proceeding with install.
Prerequisite for Cni has completed, proceeding with install.
Prerequisite for Citadel has completed, proceeding with install.
Applying manifest for component Tracing
Prerequisite for Prometheus has completed, proceeding with install.
Prerequisite for Pilot has completed, proceeding with install.
Prerequisite for CertManager has completed, proceeding with install.
Applying manifest for component Kiali
Applying manifest for component Prometheus
Applying manifest for component IngressGateway
Applying manifest for component Policy
Applying manifest for component Telemetry
Applying manifest for component Citadel
Applying manifest for component Galley
Applying manifest for component Pilot
Applying manifest for component Injector
Applying manifest for component Grafana
Finished applying manifest for component Kiali
Finished applying manifest for component Tracing
Finished applying manifest for component Prometheus
Finished applying manifest for component Citadel
Finished applying manifest for component Policy
Finished applying manifest for component IngressGateway
Finished applying manifest for component Injector
Finished applying manifest for component Galley
Finished applying manifest for component Pilot
Finished applying manifest for component Grafana
Finished applying manifest for component Telemetry

Component IngressGateway installed successfully:
================================================

serviceaccount/istio-ingressgateway-service-account created
deployment.apps/istio-ingressgateway created
gateway.networking.istio.io/ingressgateway created
sidecar.networking.istio.io/default created
poddisruptionbudget.policy/ingressgateway created
horizontalpodautoscaler.autoscaling/istio-ingressgateway created
service/istio-ingressgateway created

...
```

Nu har du distribuerat Istio till ditt AKS-kluster. För att säkerställa att vi har en lyckad distribution av Istio kan vi gå vidare till nästa avsnitt för att [validera Istio-installationen](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Verifiera Istio-installationen

Bekräfta först att de förväntade tjänsterna har skapats. Använd kommandot [kubectl get SVC][kubectl-get] för att visa de tjänster som körs. Fråga `istio-system`-namnrymden där Istio-och tilläggs komponenterna har installerats av `istio` Helm-diagrammet:

```console
kubectl get svc --namespace istio-system --output wide
```

Följande exempel på utdata visar de tjänster som nu ska köras:

- `istio-*` tjänster
- spårnings tjänster för `jaeger-*`, `tracing`och `zipkin`-tillägg
- `prometheus` mått tjänst för tillägg
- instrument panels tjänsten för analys och övervakning av `grafana`
- service instrument panel för `kiali` tillägg för service nät

Om `istio-ingressgateway` visar en extern IP-adress för `<pending>`väntar du några minuter tills en IP-adress har tilldelats av Azure-nätverk.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.116.147   <none>           3000/TCP                                                                                                                     92s   app=grafana
istio-citadel            ClusterIP      10.0.248.152   <none>           8060/TCP,15014/TCP                                                                                                           94s   app=citadel
istio-galley             ClusterIP      10.0.50.100    <none>           443/TCP,15014/TCP,9901/TCP,15019/TCP                                                                                         93s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.36.213    20.188.221.111   15020:30369/TCP,80:31368/TCP,443:30045/TCP,15029:32011/TCP,15030:31212/TCP,15031:32411/TCP,15032:30009/TCP,15443:30010/TCP   93s   app=istio-ingressgateway
istio-pilot              ClusterIP      10.0.23.222    <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                       93s   istio=pilot
istio-policy             ClusterIP      10.0.59.250    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                 93s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.123.219   <none>           443/TCP                                                                                                                      93s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.216.9     <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                       89s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                   96s   app=jaeger
jaeger-collector         ClusterIP      10.0.221.24    <none>           14267/TCP,14268/TCP,14250/TCP                                                                                                95s   app=jaeger
jaeger-query             ClusterIP      10.0.46.154    <none>           16686/TCP                                                                                                                    95s   app=jaeger
kiali                    ClusterIP      10.0.174.97    <none>           20001/TCP                                                                                                                    94s   app=kiali
prometheus               ClusterIP      10.0.245.226   <none>           9090/TCP                                                                                                                     94s   app=prometheus
tracing                  ClusterIP      10.0.249.95    <none>           9411/TCP                                                                                                                     95s   app=jaeger
zipkin                   ClusterIP      10.0.154.89    <none>           9411/TCP                                                                                                                     94s   app=jaeger
```

Bekräfta sedan att nödvändiga poddar har skapats. Använd kommandot [kubectl get poddar][kubectl-get] och fråga `istio-system`-namnrymden igen:

```console
kubectl get pods --namespace istio-system
```

Följande exempel på utdata visar de poddar som kör:

- `istio-*` poddar
- `prometheus-*` mått för Pod-tillägg
- instrument panelen för `grafana-*` tillägg för analys och övervakning av Pod
- Pod `kiali` för tillägg till tjänstens nät instrument panel

```console
NAME                                          READY   STATUS    RESTARTS   AGE
grafana-6bc97ff99-k9sk4                       1/1     Running   0          92s
istio-citadel-6b5c754454-tb8nf                1/1     Running   0          94s
istio-galley-7d6d78d7c5-zshsd                 2/2     Running   0          94s
istio-ingressgateway-85869c5cc7-x5d76         1/1     Running   0          95s
istio-pilot-787d6995b5-n5vrj                  2/2     Running   0          94s
istio-policy-6cf4fbc8dc-sdsg5                 2/2     Running   2          94s
istio-sidecar-injector-5d5b978668-wrz2s       1/1     Running   0          94s
istio-telemetry-5498db684-6kdnw               2/2     Running   1          94s
istio-tracing-78548677bc-74tx6                1/1     Running   0          96s
kiali-59b7fd7f68-92zrh                        1/1     Running   0          95s
prometheus-7c7cf9dbd6-rjxcv                   1/1     Running   0          94s
```

Alla poddar bör visa status för `Running`. Om din poddar inte har dessa status värden väntar du en minut eller två tills de gör det. Om en poddar rapporterar ett problem använder du kommandot [kubectl beskriver Pod][kubectl-describe] för att granska deras utdata och status.

## <a name="accessing-the-add-ons"></a>Åtkomst till tilläggen

Ett antal tillägg har installerats av Istio i vår installation ovan som tillhandahåller ytterligare funktioner. Webb programmen för tilläggen visas **inte** offentligt via en extern IP-adress. 

Använd kommandot `istioctl dashboard` för att få åtkomst till användar gränssnitten för tillägg. Det här kommandot använder [kubectl Port-Forward][kubectl-port-forward] och en slumpmässig port för att skapa en säker anslutning mellan din klient dator och den relevanta Pod i ditt AKS-kluster. Sedan öppnas webb programmet för tillägg automatiskt i din standard webbläsare.

Vi har lagt till ett extra säkerhets lager för Grafana och Kiali genom att ange autentiseringsuppgifter för dem tidigare i den här artikeln.

### <a name="grafana"></a>Grafana

Instrument paneler för analys och övervakning av Istio tillhandahålls av [Grafana][grafana]. Kom ihåg att använda de autentiseringsuppgifter som du skapade via Grafana-hemligheten tidigare när du uppmanas till det. Öppna Grafana-instrumentpanelen på ett säkert sätt:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Mått för Istio tillhandahålls av [Prometheus][prometheus]. Öppna Prometheus-instrumentpanelen på ett säkert sätt:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Spårning i Istio tillhandahålls av [Jaeger][jaeger]. Öppna Jaeger-instrumentpanelen på ett säkert sätt:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

En instrument panel för service nätets instrument panel tillhandahålls av [Kiali][kiali]. Kom ihåg att använda de autentiseringsuppgifter som du skapade via Kiali-hemligheten tidigare när du uppmanas till det. Öppna Kiali-instrumentpanelen på ett säkert sätt:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

Det finns ett enkelt gränssnitt till [mottagare][envoy] -proxyservrarna. Den innehåller konfigurations information och mått för en mottagare-proxy som körs i en angiven pod. Öppna mottagare-gränssnittet på ett säkert sätt enligt följande:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Avinstallera Istio från AKS

> [!WARNING]
> Om du tar bort Istio från ett system som körs kan det leda till trafik problem mellan dina tjänster. Se till att du har gjort att systemet fortfarande fungerar korrekt utan Istio innan du fortsätter.

### <a name="remove-istio-components-and-namespace"></a>Ta bort Istio-komponenter och namn område

Om du vill ta bort Istio från ditt AKS-kluster använder du kommandot `istioctl manifest generate` med Specifikations filen `istio.aks.yaml` Istio Control plan. Detta genererar det distribuerade manifestet, som vi ska skicka till `kubectl delete` för att ta bort alla installerade komponenter och `istio-system` namn området.

```console
istioctl manifest generate -f istio.aks.yaml -o istio-components-aks --logtostderr --set installPackagePath=./install/kubernetes/operator/charts 

kubectl delete -f istio-components-aks -R
```

### <a name="remove-istio-crds-and-secrets"></a>Ta bort Istio CRDs och hemligheter

Kommandona ovan tar bort alla Istio-komponenter och-namnrymder, men vi har fortfarande kvar med skapade Istio-hemligheter. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>Nästa steg

I följande dokumentation beskrivs hur du kan använda Istio för att tillhandahålla intelligent routning för att distribuera en Kanarie-version:

> [!div class="nextstepaction"]
> [AKS Istio intelligent routning-scenario][istio-scenario-routing]

För att utforska fler installations-och konfigurations alternativ för Istio, se följande officiella Istio-vägledning:

- [Istio – installations guider][istio-installation-guides]

Du kan också följa ytterligare scenarier som använder:

- [Exempel på Istio Bookinfo-program][istio-bookinfo-example]

Information om hur du övervakar ditt AKS-program med hjälp av Application Insights och Istio finns i följande Azure Monitor-dokumentation:

- [Program övervakning utan Instrumentation för Kubernetes-värdbaserade program][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-installation-guides]: https://istio.io/docs/setup/install/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-istioctl]: https://istio.io/docs/setup/install/istioctl/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./servicemesh-istio-scenario-routing.md
