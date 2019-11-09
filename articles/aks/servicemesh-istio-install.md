---
title: Installera Istio i Azure Kubernetes service (AKS)
description: Lär dig hur du installerar och använder Istio för att skapa ett service nät i ett Azure Kubernetes service-kluster (AKS)
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 245ac3b1fd88b8d2430e9ddefef3562efd16e6d1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885387"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Installera och använda Istio i Azure Kubernetes service (AKS)

[Istio][istio-github] är ett nät med öppen källkod som ger en nyckel uppsättning över mikrotjänster i ett Kubernetes-kluster. Dessa funktioner omfattar trafik hantering, tjänst identitet och säkerhet, princip tillämpning och upprätthållande. Mer information om Istio finns i den officiella [Istio?][istio-docs-concepts] -dokumentationen.

Den här artikeln visar hur du installerar Istio. Istio-`istioctl`-klientens binärfil installeras på klient datorn och Istio-komponenterna installeras i ett Kubernetes-kluster på AKS.

> [!NOTE]
> De här anvisningarna hänvisar till Istio version `1.3.2`och använder minst Helm version `2.14.2`.
>
> Istio `1.3.x`-versioner har testats av Istio-teamet mot Kubernetes-versioner `1.13`, `1.14`, `1.15`. Du hittar fler Istio-versioner på [GitHub-Istio-][istio-github-releases]versioner, information om var och en av versionerna på [Istio nyheter][istio-release-notes] och Kubernetes-versioner som stöds på [Istio allmänna vanliga frågor och svar][istio-faq].

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Hämta och installera Istio istioctl-klientens binärfil
> * Installera Istio på AKS
> * Verifiera Istio-installationen
> * Få åtkomst till tilläggen
> * Avinstallera Istio från AKS

## <a name="before-you-begin"></a>Innan du börjar

De steg som beskrivs i den här artikeln förutsätter att du har skapat ett AKS-kluster (Kubernetes `1.13` och senare, med RBAC aktiverat) och har upprättat en `kubectl` anslutning till klustret. Om du behöver hjälp med något av dessa objekt kan du se snabb starten för [AKS][aks-quickstart].

Du behöver [Helm][helm] för att följa dessa instruktioner och installera Istio. Vi rekommenderar att du har den senaste säkra versionen som är korrekt installerad och konfigurerad i klustret. Om du behöver hjälp med att installera Helm kan du läsa mer i [installations vägledningen för AKS Helm][helm-install]. Alla Istio-poddar måste också vara schemalagda att köras på Linux-noder.

Se till att du har läst [Istio-prestanda och skalbarhets](https://istio.io/docs/concepts/performance-and-scalability/) dokumentation för att förstå de ytterligare resurs kraven för att köra Istio i ditt AKS-kluster. Kärn-och minnes kraven varierar beroende på din särskilda arbets belastning. Välj ett lämpligt antal noder och storlek för virtuella datorer för din installation.

Den här artikeln separerar installations vägledningen för Istio till flera diskreta steg. Slut resultatet är detsamma i strukturen som den officiella Istio installations [vägledningen][istio-install-helm].

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="add-the-istio-helm-chart-repository"></a>Lägg till Istio Helm Chart-lagringsplatsen

Lägg till Istio Helm Chart-lagringsplatsen för Istio-versionen. Se till att du kör `helm repo update` för att uppdatera din lokala information för diagram lagrings platsen.

```azurecli
helm repo add istio.io https://storage.googleapis.com/istio-release/releases/$ISTIO_VERSION/charts/
helm repo update
```

## <a name="install-the-istio-crds-on-aks"></a>Installera Istio-CRDs på AKS

Istio använder [anpassade resurs definitioner (CRDs)][kubernetes-crd] för att hantera körnings konfigurationen. Vi måste installera Istio-CRDs först eftersom Istio-komponenterna har ett beroende på dem. Använd Helm och `istio-init`-diagrammet för att installera Istio-CRDs i namn området `istio-system` i ditt AKS-kluster:

```azurecli
helm install istio.io/istio-init --name istio-init --namespace istio-system
```

[Jobben][kubernetes-jobs] distribueras som en del av `istio-init` Helm-diagrammet för att installera CRDs. Dessa jobb bör ta mindre än 20s att slutföra, beroende på kluster miljön. Du kan kontrol lera att jobben har slutförts på följande sätt:

```azurecli
kubectl get jobs -n istio-system
```

Följande exempel på utdata visar att jobben har slutförts.

```console
NAME                      COMPLETIONS   DURATION   AGE
istio-init-crd-10-1.3.2   1/1           14s        14s
istio-init-crd-11-1.3.2   1/1           12s        14s
istio-init-crd-12-1.3.2   1/1           14s        14s
```

Nu när vi har bekräftat att jobben har slutförts, ska vi kontrol lera att vi har rätt antal Istio-CRDs installerade. Du kan kontrol lera att alla 23 Istio-CRDs har installerats genom att köra följande kommando. Kommandot ska returnera numret `23`.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - check CRD count](includes/servicemesh/istio/install-check-crd-count-powershell.md)]

::: zone-end

Om du har kommit till den här punkten betyder det att du har installerat Istio-CRDs. Gå vidare till nästa avsnitt för att [Installera Istio-komponenterna på AKS](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>Installera Istio-komponenterna på AKS

Vi kommer att installera [Grafana][grafana] och [Kiali][kiali] som en del av vår Istio-installation. Grafana tillhandahåller instrument paneler för analys och övervakning, och Kiali tillhandahåller en instrument panel för service nät. I vår installation kräver var och en av dessa komponenter autentiseringsuppgifter som måste tillhandahållas som en [hemlighet][kubernetes-secrets].

Innan vi kan installera Istio-komponenterna måste vi skapa hemligheterna för både Grafana och Kiali. 

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

Nu när vi har skapat Grafana-och Kiali-hemligheterna i vårt AKS-kluster är det dags att installera Istio-komponenterna. Använd Helm och `istio`-diagrammet för att installera Istio-komponenterna i namn området `istio-system` i ditt AKS-kluster. 

> [!NOTE]
> **Installations alternativ**
> 
> Vi använder följande alternativ som en del av installationen:
> - `global.controlPlaneSecurityEnabled=true` ömsesidig TLS har Aktiver ATS för kontroll planet
> - `global.mtls.enabled=true` – Kräv att all tjänst-till-tjänst-kommunikation har MTLS
> - `grafana.enabled=true` – aktivera Grafana-distribution för analys-och övervaknings instrument paneler
> - `grafana.security.enabled=true`-aktivera autentisering för Grafana
> - `tracing.enabled=true` – aktivera distributionen av Jaeger för spårning
> - `kiali.enabled=true` – aktivera distributionen av Kiali för en instrument panel för service nät
>
> **Node-väljare**
>
> Istio måste för närvarande vara schemalagda att köras på Linux-noder. Om du har Windows Server-noder i klustret måste du se till att Istio-poddar endast är schemalagda att köras på Linux-noder. Vi använder [Node-väljare][kubernetes-node-selectors] för att kontrol lera att poddar är schemalagda till rätt noder.

> [!CAUTION]
> Funktionerna [SDS (Secret Discovery service)][istio-feature-sds] och [Istio cni][istio-feature-cni] Istio är för närvarande i [alpha][istio-feature-stages], så tänk på detta innan du aktiverar dem. Dessutom är Kubernetes-funktionen för [tjänst kontots token för volym projektion][kubernetes-feature-sa-projected-volume] (ett krav för SDS) inte aktive rad i aktuella AKS-versioner.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/istio/install-components-powershell.md)]

::: zone-end

`istio` Helm-diagrammet distribuerar ett stort antal objekt. Du kan se listan från utdata från kommandot `helm install` ovan. Distributionen av Istio-komponenterna bör ta under 2 minuter att slutföra, beroende på kluster miljön.

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
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.164.244   <none>           3000/TCP                                                                                                                                     53s   app=grafana
istio-citadel            ClusterIP      10.0.49.16     <none>           8060/TCP,15014/TCP                                                                                                                           53s   istio=citadel
istio-galley             ClusterIP      10.0.175.173   <none>           443/TCP,15014/TCP,9901/TCP                                                                                                                   53s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.226.151   20.188.221.111   15020:31128/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:30817/TCP,15030:30436/TCP,15031:32485/TCP,15032:30980/TCP,15443:30124/TCP   53s   app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.102.158   <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       53s   istio=pilot
istio-policy             ClusterIP      10.0.234.53    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                                 53s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.216.8     <none>           443/TCP,15014/TCP                                                                                                                            53s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.154.215   <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       53s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                                   52s   app=jaeger
jaeger-collector         ClusterIP      10.0.26.109    <none>           14267/TCP,14268/TCP                                                                                                                          52s   app=jaeger
jaeger-query             ClusterIP      10.0.70.55     <none>           16686/TCP                                                                                                                                    52s   app=jaeger
kiali                    ClusterIP      10.0.36.206    <none>           20001/TCP                                                                                                                                    53s   app=kiali
prometheus               ClusterIP      10.0.236.99    <none>           9090/TCP                                                                                                                                     53s   app=prometheus
tracing                  ClusterIP      10.0.83.152    <none>           80/TCP                                                                                                                                       52s   app=jaeger
zipkin                   ClusterIP      10.0.25.86     <none>           9411/TCP                                                                                                                                     52s   app=jaeger
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
NAME                                     READY   STATUS      RESTARTS   AGE
grafana-7c48555456-msl7b                 1/1     Running     0          88s
istio-citadel-566fc66db7-m8wgl           1/1     Running     0          87s
istio-galley-5746db8d56-pl5gg            1/1     Running     0          88s
istio-ingressgateway-6c94f7c9bf-f5lt5    1/1     Running     0          88s
istio-init-crd-10-1.3.2-xw9g2            0/1     Completed   0          92m
istio-init-crd-11-1.3.2-54rz8            0/1     Completed   0          92m
istio-init-crd-12-1.3.2-789qj            0/1     Completed   0          92m
istio-pilot-6748968b6d-rvdfx             2/2     Running     0          87s
istio-policy-7576bbbcf7-2stft            2/2     Running     0          87s
istio-sidecar-injector-76d79d494-7jk9n   1/1     Running     0          87s
istio-telemetry-74b7bf676d-tfrcl         2/2     Running     0          88s
istio-tracing-655d9588bc-d2htg           1/1     Running     0          86s
kiali-65d55bcfb8-tqrfk                   1/1     Running     0          88s
prometheus-846f9849bd-br8kp              1/1     Running     0          87s
```

Det bör finnas tre `istio-init-crd-*` poddar med en `Completed` status. Dessa poddar var ansvariga för att köra jobb som skapade CRDs i ett tidigare steg. Alla andra poddar bör visa status för `Running`. Om din poddar inte har dessa status värden väntar du en minut eller två tills de gör det. Om en poddar rapporterar ett problem använder du kommandot [kubectl beskriver Pod][kubectl-describe] för att granska deras utdata och status.

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

Använd följande kommandon för att ta bort Istio från ditt AKS-kluster. `helm delete`-kommandona tar bort `istio` och `istio-init` diagram och `kubectl delete namespace` kommandot tar bort `istio-system`-namnområdet.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete namespace istio-system
```

### <a name="remove-istio-crds-and-secrets"></a>Ta bort Istio CRDs och hemligheter

Kommandona ovan tar bort alla Istio-komponenter och namn området, men vi kommer fortfarande till Istio CRDs och hemligheter. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>Nästa steg

I följande dokumentation beskrivs hur du kan använda Istio för att tillhandahålla intelligent routning för att distribuera en Kanarie-version:

> [!div class="nextstepaction"]
> [AKS Istio intelligent routning-scenario][istio-scenario-routing]

Om du vill utforska fler installations-och konfigurations alternativ för Istio kan du läsa följande officiella Istio-artiklar:

- [Installations guide för Istio-Helm][istio-install-helm]
- [Installations alternativ för Istio-Helm][istio-install-helm-options]

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
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/security/auth-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
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
[helm-install]: ./kubernetes-helm.md
