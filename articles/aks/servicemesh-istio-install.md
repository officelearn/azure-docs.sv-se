---
title: Installera Istio i Azure Kubernetes Service (AKS)
description: Lär dig hur du installerar och använder Istio för att skapa ett tjänstnät i ett AKS-kluster (Azure Kubernetes Service)
author: paulbouwer
ms.topic: article
ms.date: 02/19/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: f0fe4ab46bfe5c0c0c2ea67aa2e2694321628be5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136371"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Installera och använda Istio i Azure Kubernetes Service (AKS)

[Istio][istio-github] är ett tjänstnät med öppen källkod som tillhandahåller en nyckeluppsättning funktioner över mikrotjänsterna i ett Kubernetes-kluster. Dessa funktioner omfattar trafikhantering, tjänstidentitet och säkerhet, principefterlevnad och observerbarhet. Mer information om Istio finns i dokumentationen [Vad är Istio?][istio-docs-concepts]

Den här artikeln visar hur du installerar Istio. Istio-klientbinären `istioctl` installeras på klientdatorn och Istio-komponenterna installeras i ett Kubernetes-kluster på AKS.

> [!NOTE]
> Följande instruktioner refererar till `1.4.0`Istio-versionen .
>
> Istio-utgåvorna `1.4.x` har testats av Istio-teamet mot `1.13` `1.14`Kubernetes versioner , . `1.15` Du hittar ytterligare Istio-versioner på [GitHub - Istio Releases][istio-github-releases], information om var och en av utgåvorna på [Istio News][istio-release-notes] och kubernetes-versioner som stöds på [Istio General FAQ][istio-faq].

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Ladda ner och installera Istio istioctl-klienten binär
> * Installera Istio på AKS
> * Verifiera Istio-installationen
> * Få tillgång till tilläggen
> * Avinstallera Istio från AKS

## <a name="before-you-begin"></a>Innan du börjar

Stegen som beskrivs i den här artikeln förutsätter att du `1.13` har skapat ett AKS-kluster (Kubernetes och högre, med RBAC aktiverat) och har upprättat en `kubectl` anslutning till klustret. Om du behöver hjälp med något av dessa objekt, sedan se [AKS snabbstart][aks-quickstart].

Kontrollera att du har läst dokumentationen [för Istio-prestanda och skalbarhet](https://istio.io/docs/concepts/performance-and-scalability/) för att förstå de ytterligare resurskraven för att köra Istio i AKS-klustret. Kärn- och minneskraven varierar beroende på din arbetsbelastning. Välj ett lämpligt antal noder och VM-storlek för att tillgodose din konfiguration.

Den här artikeln separerar Istio-installationsvägledningen i flera diskreta steg. Slutresultatet är detsamma i struktur som den officiella Istio [installationsvägledning][istio-install-istioctl].

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

Vi kommer att installera [Grafana][grafana] och [Kiali][kiali] som en del av vår Istio installation. Grafana tillhandahåller instrumentpaneler för analys och övervakning, och Kiali tillhandahåller en instrumentpanel för övervakning av servicenät. I vår konfiguration kräver var och en av dessa komponenter autentiseringsuppgifter som måste anges som en [hemlighet][kubernetes-secrets].

Innan vi kan installera Istio-komponenterna måste vi skapa hemligheter för både Grafana och Kiali. Dessa hemligheter måste installeras `istio-system` i namnområdet som kommer att användas av Istio, så vi måste skapa namnområdet också. Vi måste använda `--save-config` alternativet när du `kubectl create` skapar namnområdet via så att `kubectl apply` Istio-installationsprogrammet kan köras på det här objektet i framtiden.

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

Nu när vi har skapat Grafana- och Kiali-hemligheterna i vårt AKS-kluster är det dags att installera Istio-komponenterna. 

Helm-installationsmetoden för Istio kommer att vara föråldrad i framtiden. [Helm][helm] Den nya installationsmetoden `istioctl` för Istio utnyttjar klientbinären, [Istio-konfigurationsprofilerna][istio-configuration-profiles]och den nya [Istio-kontrollplansspecifikationen och api:et][istio-control-plane]. Denna nya metod är vad vi kommer att använda för att installera Istio.

> [!NOTE]
> Istio måste för närvarande schemaläggas för att köras på Linux-noder. Om du har Windows Server-noder i klustret måste du se till att Istio-poddarna endast är schemalagda att köras på Linux-noder. Vi använder [nodväljare][kubernetes-node-selectors] för att se till att poddar är schemalagda till rätt noder.

> [!CAUTION]
> [SDS (secret discovery service)][istio-feature-sds] och [Istio CNI][istio-feature-cni] Istio funktioner är för närvarande i [Alpha][istio-feature-stages], så tänkte bör ges innan du aktiverar dessa. Dessutom är [funktionen Service Account Token Volume Projection][kubernetes-feature-sa-projected-volume] Kubernetes (ett krav för SDS) inte aktiverad i aktuella AKS-versioner.
Skapa en `istio.aks.yaml` fil som anropas med följande innehåll. Den här filen kommer att innehålla [Itio kontrollplan spec][istio-control-plane] detaljer för att konfigurera Istio.

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

Installera istio `istioctl apply` med kommandot `istio.aks.yaml` och ovanstående Istio kontroll plan spec fil enligt följande:

```console
istioctl manifest apply -f istio.aks.yaml --logtostderr --set installPackagePath=./install/kubernetes/operator/charts
```

Installationsprogrammet distribuerar ett antal [CRD:er][kubernetes-crd] och hanterar sedan beroenden för att installera alla relevanta objekt som definierats för den här konfigurationen av Istio. Du bör se något i stil med följande utdatautdrag.

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

Nu har du distribuerat Istio till AKS-klustret. För att säkerställa att vi har en lyckad distribution av Istio, låt oss gå vidare till nästa avsnitt för att [validera Istio-installationen](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Verifiera Istio-installationen

Bekräfta först att de förväntade tjänsterna har skapats. Använd [kommandot kubectl get svc][kubectl-get] för att visa de tjänster som körs. Fråga `istio-system` namnområdet, där Istio- och tilläggskomponenterna `istio` installerades av Helm-diagrammet:

```console
kubectl get svc --namespace istio-system --output wide
```

Följande exempelutdata visar de tjänster som nu ska köras:

- `istio-*`Tjänster
- `jaeger-*`, `tracing`och `zipkin` tilläggsspårningstjänster
- `prometheus`tjänsten tilläggsmått
- `grafana`instrumentpanelstjänsten för tilläggsanalys och övervakning
- `kiali`instrumentpanelstjänsten för tilläggsnät

Om `istio-ingressgateway` visar en extern `<pending>`IP för , vänta några minuter tills en IP-adress har tilldelats av Azure-nätverk.

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

Bekräfta sedan att de nödvändiga poddarna har skapats. Använd kommandot [kubectl get pods][kubectl-get] och `istio-system` fråga namnområdet igen:

```console
kubectl get pods --namespace istio-system
```

I följande exempelutdata visas de poddar som körs:

- kapslarna `istio-*`
- pod `prometheus-*` för tilläggsmått
- instrumentpanelspodden `grafana-*` för tilläggsanalys och övervakning
- instrumentpanelspodden `kiali` för tilläggstjänstnät

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

Alla poddar ska visa statusen `Running`. Om dina poddar inte har dessa statusar, vänta en minut eller två tills de gör det. Om några poddar rapporterar ett problem använder du kommandot [kubectl describe pod][kubectl-describe] för att granska deras utdata och status.

## <a name="accessing-the-add-ons"></a>Komma åt tilläggen

Ett antal tillägg installerades av Istio i vår inställning ovan som ger ytterligare funktioner. Webbprogrammen för tilläggen exponeras **inte** offentligt via en extern IP-adress. 

Använd kommandot om du vill komma `istioctl dashboard` åt tilläggsanvändargränssnitten. Det här kommandot utnyttjar [kubectl port-forward][kubectl-port-forward] och en slumpmässig port för att skapa en säker anslutning mellan klientdatorn och den relevanta podden i AKS-klustret. Det kommer då automatiskt att öppna tillägget webbprogram i din standardwebbläsare.

Vi har lagt till ytterligare ett säkerhetslager för Grafana och Kiali genom att ange autentiseringsuppgifter för dem tidigare i den här artikeln.

### <a name="grafana"></a>Grafana

Instrumentpanelerna för analys och övervakning för Istio tillhandahålls av [Grafana][grafana]. Kom ihåg att använda de autentiseringsuppgifter som du skapade via Grafana hemligheten tidigare när du uppmanas. Öppna Grafana-instrumentpanelen på ett säkert sätt enligt följande:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Mått för Istio tillhandahålls av [Prometheus][prometheus]. Öppna Prometheus-instrumentpanelen säkert enligt följande:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Spårning inom Istio tillhandahålls av [Jaeger][jaeger]. Öppna Jaeger-instrumentpanelen på ett säkert sätt enligt följande:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali (olika)

En instrumentpanel för tjänstnäts observerbarhet tillhandahålls av [Kiali][kiali]. Kom ihåg att använda de autentiseringsuppgifter som du skapade via Kiali-hemligheten tidigare när du uppmanas att göra det. Öppna Kiali-instrumentpanelen på ett säkert sätt enligt följande:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

Ett enkelt gränssnitt till [sändebudets][envoy] fullmakter finns tillgängligt. Den innehåller konfigurationsinformation och mått för en envoy-proxy som körs i en angiven pod. Öppna gränssnittet för sändebud säkert enligt följande:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Avinstallera Istio från AKS

> [!WARNING]
> Om du tar bort Istio från ett system som körs kan det leda till trafikrelaterade problem mellan dina tjänster. Se till att du har gjort avsättningar för ditt system att fortfarande fungera korrekt utan Istio innan du fortsätter.

### <a name="remove-istio-components-and-namespace"></a>Ta bort Istio-komponenter och namnområde

Om du vill ta bort Istio `istioctl manifest generate` från `istio.aks.yaml` AKS-klustret använder du kommandot med spec-filen Istio control plane. Detta kommer att generera det distribuerade `kubectl delete` manifestet, som vi kommer `istio-system` att leda till för att ta bort alla installerade komponenter och namnområdet.

```console
istioctl manifest generate -f istio.aks.yaml -o istio-components-aks --logtostderr --set installPackagePath=./install/kubernetes/operator/charts 

kubectl delete -f istio-components-aks -R
```

### <a name="remove-istio-crds-and-secrets"></a>Ta bort Istio CRD och hemligheter

Ovanstående kommandon tar bort alla Istio-komponenter och namnområde, men vi är fortfarande kvar med genererade Istio hemligheter. 

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

I följande dokumentation beskrivs hur du kan använda Istio för att tillhandahålla intelligent routning för att distribuera en kanariefågelversion:

> [!div class="nextstepaction"]
> [AKS Istio intelligent routing scenario][istio-scenario-routing]

Mer information om installation och konfigurationsalternativ för Istio finns i följande officiella Istio-vägledning:

- [Istio - installationsguider][istio-installation-guides]

Du kan också följa ytterligare scenarier med hjälp av:

- [Exempel på Istio Bookinfo-program][istio-bookinfo-example]

Mer information om hur du övervakar ditt AKS-program med Application Insights och Istio finns i följande Azure Monitor-dokumentation:

- [Övervakning av nollinstrumenteringsprogram för Kubernetes värdprogram][app-insights]

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
