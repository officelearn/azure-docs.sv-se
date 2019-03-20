---
title: Installera Istio i Azure Kubernetes Service (AKS)
description: Lär dig hur du installerar och använder Istio för att skapa en tjänst-nät i ett kluster i Azure Kubernetes Service (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 12/3/2018
ms.author: pabouwer
ms.openlocfilehash: d85b830b63e2d52f3eeb5df8645edccfccf43c76
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58138158"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Installera och använda Istio i Azure Kubernetes Service (AKS)

[Istio] [ istio-github] är en öppen källkod service-nät som ger en nyckeluppsättning av funktioner i mikrotjänster i ett Kubernetes-kluster. Dessa funktioner innefattar trafikhantering, identitet och säkerhet, genomförande av principer och observability. Mer information om Istio finns i officiellt [vad är Istio?] [ istio-docs-concepts] dokumentation.

Den här artikeln visar hur du installerar Istio. Istio `istioctl` binär-klienten installeras på klientdatorn och sedan Istio-komponenter är installerade i en Kubernetes-kluster i AKS. Dessa instruktioner referera Istio version *1.0.4*. Du hittar ytterligare Istio versioner på [GitHub - Istio versioner][istio-github-releases].

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Ladda ned Istio
> * Installera klienten Istio binära
> * Installera komponenterna för Istio Kubernetes
> * Verifiera installationen

## <a name="before-you-begin"></a>Innan du börjar

Stegen som beskrivs i den här artikeln förutsätter att du har skapat ett AKS-kluster (Kubernetes 1.10 och ovan, med RBAC aktiverad) och har upprättat en `kubectl` anslutning med klustret. Om du behöver hjälp med något av dessa objekt läser den [AKS-Snabbstart][aks-quickstart].

Du behöver för att installera Istio [Helm] [ helm] version *2.10.0* eller senare korrekt installerat och konfigurerat i klustret. Om du behöver hjälp med att installera Helm kan se den [vägledning för installation av AKS Helm][helm-install]. Om du inte har minst version *2.10.0* Helm installerade, uppgradera eller se den [Istio - Installation med Helm handboken] [ istio-install-helm] för andra installationsalternativ.

Den här artikeln separerar Istio installation vägledning i flera separata steg. Var och en av de här stegen beskrivs så att du lär dig hur du installerar Istio och lär dig hur Istio fungerar med Kubernetes. Slutresultatet är samma i struktur som den officiella Istio installationen [vägledning][istio-install-k8s-quickstart].

## <a name="download-istio"></a>Ladda ned Istio

Först ladda ned och extrahera den senaste versionen av Istio. Stegen är lite annorlunda för ett bash-gränssnitt på MacOS, Linux eller Windows-undersystem för Linux och ett PowerShell-gränssnitt. Välj ett av följande installationen steg för din miljö:

* [Bash på MacOS, Linux eller Windows-undersystem för Linux](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

På MacOS, använder du `curl` ladda ned den senaste versionen av Istio och sedan extrahera med `tar` på följande sätt:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

På Linux eller Windows-undersystem for Linux kan använda `curl` ladda ned den senaste versionen av Istio och sedan extrahera med `tar` på följande sätt:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

### <a name="powershell"></a>PowerShell

I PowerShell använder [Invoke-WebRequest] [ Invoke-WebRequest] ladda ned den senaste versionen av Istio och sedan extrahera med [Expandera Arkivera] [ Expand-Archive] som följande:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.0.4"

# Windows
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

## <a name="install-the-istio-client-binary"></a>Installera klienten Istio binära

Den `istioctl` binära klienten körs på klientdatorn och låter dig hantera Istio routningsregler och principer. Igen, installera stegen skiljer sig lite mellan klientoperativsystem. Välj ett av följande installationen steg för din miljö.

> [!IMPORTANT]
> Kontrollera att du kört stegen i det här avsnittet från den översta mappen av Istio-versionen som du har hämtat och extraherat.

### <a name="macos"></a>MacOS

Installera Istio `istioctl` klientens binärfil i ett bash-baserat gränssnitt på MacOS, Använd följande kommandon. De här kommandona kopiera den `istioctl` klienten binär till platsen för standardanvändare installationsprogrammet i din `PATH`.

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

Om du vill ha för Istio kommandoradsverktyget genomförd `istioctl` klienten binärt, sedan konfigurera den på följande sätt:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Nu gå vidare till avsnittet [installera komponenterna för Istio Kubernetes](#install-the-istio-kubernetes-components).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux eller Windows-undersystem for Linux

Använd följande kommandon för att installera Istio `istioctl` klienten binära i ett bash-baserat gränssnitt på Linux eller [Windows-undersystem for Linux][install-wsl]. De här kommandona kopiera den `istioctl` klienten binär till platsen för standardanvändare installationsprogrammet i din `PATH`.

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

Om du vill ha för Istio kommandoradsverktyget genomförd `istioctl` klienten binärt, sedan konfigurera den på följande sätt:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Nu gå vidare till avsnittet [installera komponenterna för Istio Kubernetes](#install-the-istio-kubernetes-components).

### <a name="windows"></a>Windows

Installera Istio `istioctl` klientens binärfil i ett Powershell-baserat gränssnitt på Windows, använder du följande kommandon. De här kommandona kopiera den `istioctl` klienten binär till en ny användare programmet plats och gör den tillgänglig via din `PATH`.

```powershell
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:/Program Files/Istio"
mv ./bin/istioctl.exe "C:/Program Files/Istio/"
$PATH = [environment]::GetEnvironmentVariable("PATH", "User")
[environment]::SetEnvironmentVariable("PATH", $PATH + "; C:\Program Files\Istio\", "User")
```

## <a name="install-the-istio-kubernetes-components"></a>Installera komponenterna för Istio Kubernetes

> [!IMPORTANT]
> Kontrollera att du kört stegen i det här avsnittet från den översta mappen av Istio-versionen som du har hämtat och extraherat.

> [!NOTE]
> Version `1.0.6` och nyare av Istio Helm-diagrammet har större ändringar. Om du väljer för att installera den här versionen, måste du nu att manuellt skapa en hemlighet för Kiali. Du måste också manuellt skapa en hemlighet för Grafana om du har angett `grafana.security.enabled=true`. Se Helm-diagrammet Istio [README.md](https://github.com/istio/istio/tree/master/install/kubernetes/helm/istio#installing-the-chart) finns mer information om hur du skapar dessa hemligheter.

Använd Helm för att installera komponenter för Istio i AKS-klustret. Installera Istio resurser i den `istio-system` namnområdet, och aktivera ytterligare alternativ för säkerhet och övervakning på följande sätt:

```azurecli
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set grafana.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

Helm-diagrammet distribuerar ett stort antal objekt. Distributionen kan ta 2 och 3 minuter att slutföra beroende på klustermiljön.

## <a name="validate-the-installation"></a>Verifiera installationen

För att säkerställa att du har en lyckad distribution av Istio, kan vi Kontrollera installationen.

Först bekräfta att de förväntade tjänsterna har skapats. Använd den [kubectl hämta svc] [ kubectl-get] kommando för att visa tjänsterna som körs. Fråga den *istio system* namnområdet, där komponenterna för Istio och tillägg har installerats av Helm-diagrammet:

```console
kubectl get svc --namespace istio-system --output wide
```

Följande Exempelutdata visar de tjänster som bör nu köras:

- *istio -** tjänster
- * jaeger-**, *spårning*, och *zipkin* spårning tilläggstjänster
- *prometheus* måtttjänsten för tillägg
- *grafana* tillägg analyser och instrumentpanel för övervakningstjänsten
- *kiali* tillägg nät instrumentpanel-tjänsten

Om den `istio-ingressgateway` visas ett externa IP-adressen för `<pending>`, Vänta några minuter tills en IP-adress har tilldelats genom Azure-nätverk.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                   AGE       SELECTOR
grafana                  ClusterIP      10.0.26.60     <none>           3000/TCP                                                                                                                  3m        app=grafana
istio-citadel            ClusterIP      10.0.88.87     <none>           8060/TCP,9093/TCP                                                                                                         3m        istio=citadel
istio-egressgateway      ClusterIP      10.0.115.115   <none>           80/TCP,443/TCP                                                                                                            3m        app=istio-egressgateway,istio=egressgateway
istio-galley             ClusterIP      10.0.104.183   <none>           443/TCP,9093/TCP                                                                                                          3m        istio=galley
istio-ingressgateway     LoadBalancer   10.0.12.216    52.187.250.239   80:31380/TCP,443:31390/TCP,31400:31400/TCP,15011:30469/TCP,8060:31999/TCP,853:31235/TCP,15030:32000/TCP,15031:30293/TCP   3m        app=istio-ingressgateway,istio=ingressgateway
istio-pilot              ClusterIP      10.0.38.134    <none>           15010/TCP,15011/TCP,8080/TCP,9093/TCP                                                                                     3m        istio=pilot
istio-policy             ClusterIP      10.0.253.81    <none>           9091/TCP,15004/TCP,9093/TCP                                                                                               3m        istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.181.186   <none>           443/TCP                                                                                                                   3m        istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.177.113   <none>           9091/TCP,15004/TCP,9093/TCP,42422/TCP                                                                                     3m        istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                3m        app=jaeger
jaeger-collector         ClusterIP      10.0.112.81    <none>           14267/TCP,14268/TCP                                                                                                       3m        app=jaeger
jaeger-query             ClusterIP      10.0.179.193   <none>           16686/TCP                                                                                                                 3m        app=jaeger
kiali                    ClusterIP      10.0.211.63    <none>           20001/TCP                                                                                                                 3m        app=kiali
prometheus               ClusterIP      10.0.70.113    <none>           9090/TCP                                                                                                                  3m        app=prometheus
tracing                  ClusterIP      10.0.139.121   <none>           80/TCP                                                                                                                    3m        app=jaeger
zipkin                   ClusterIP      10.0.60.155    <none>           9411/TCP                                                                                                                  3m        app=jaeger
```

Bekräfta sedan att nödvändiga poddarna har skapats. Använd den [kubectl hämta poddar] [ kubectl-get] kommando och fråga igen den *istio system* namnområde:

```console
kubectl get pods --namespace istio-system
```

Följande Exempelutdata visar poddarna som kör:

- den *istio -** poddar
- den *prometheus -** tillägg mått pod
- den *grafana -** tillägg analys och övervakning instrumentpanelen pod
- den *kiali* tillägg service nät instrumentpanelen pod

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-59b787b9b-cr6d7                  1/1       Running     0          6m
istio-citadel-78df8c67d9-9lfpf           1/1       Running     0          6m
istio-egressgateway-6b96cd7f5-k848h      1/1       Running     0          6m
istio-galley-58f566cb66-8mhbv            1/1       Running     0          6m
istio-ingressgateway-6cbbf596f6-6jz8g    1/1       Running     0          6m
istio-pilot-8449d555fc-sl6kp             2/2       Running     0          6m
istio-policy-6b99d88bc5-55s52            2/2       Running     0          6m
istio-sidecar-injector-b88dfb954-8m86s   1/1       Running     0          6m
istio-telemetry-675cb4cb9d-8s7wd         2/2       Running     0          6m
istio-tracing-7596597bd7-sbnt9           1/1       Running     0          6m
kiali-5fbd6ffb-4qcxw                     1/1       Running     0          6m
prometheus-76db5fddd5-2tkxs              1/1       Running     0          6m
```

Alla poddarna visa status för `Running`. Om dina poddar inte har dessa statusar, vänta en minut eller två tills de gör. Om alla poddar rapportera ett problem, använder du den [kubectl beskriver pod] [ kubectl-describe] att granska sina utdata och status.

## <a name="accessing-the-add-ons"></a>Åtkomst till tillägg

Ett antal tillägg har installerats Istio i vår installationsprogrammet som ger ytterligare funktioner. Användargränssnitt för tilläggen exponeras inte offentligt via en extern ip-adress. För att komma åt användargränssnitt tillägg, använda den [kubectl port och tydlig] [ kubectl-port-forward] kommando. Det här kommandot skapar en säker anslutning mellan en lokal port på klientdatorn och relevanta pod AKS-klustret.

### <a name="grafana"></a>Grafana

Analys och övervakning instrumentpaneler för Istio tillhandahålls av [Grafana][grafana]. Vidarebefordra den lokala porten *3000* på klientdatorn till port *3000* på en pod med Grafana AKS-klustret:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

Följande Exempelutdata visar port och tydlig som håller på att konfigureras för Grafana:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Du kan nu nå Grafana på följande URL på klientdatorn - [ http://localhost:3000 ](http://localhost:3000).

### <a name="prometheus"></a>Prometheus

Uttryck för webbläsaren för mätvärden som tillhandahålls av [Prometheus][prometheus]. Vidarebefordra den lokala porten *9090* på klientdatorn till port *9090* på en pod med Prometheus AKS-klustret:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090
```

Följande Exempelutdata visar port och tydlig som håller på att konfigureras för Prometheus:

```console
Forwarding from 127.0.0.1:9090 -> 9090
Forwarding from [::1]:9090 -> 9090
```

Du kan nu nå Prometheus uttryck webbläsaren på följande URL på klientdatorn - [ http://localhost:9090 ](http://localhost:9090).

### <a name="jaeger"></a>Jaeger

Spårning av användargränssnittet tillhandahålls av [Jaeger][jaeger]. Vidarebefordra den lokala porten *16686* på klientdatorn till port *16686* på en pod med Jaeger AKS-klustret:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686
```

Följande Exempelutdata visar port och tydlig som håller på att konfigureras för Jaeger:

```console
Forwarding from 127.0.0.1:16686 -> 16686
Forwarding from [::1]:16686 -> 16686
```

Du kan nu nå Jaeger spårning användargränssnittet på följande URL på klientdatorn - [ http://localhost:16686 ](http://localhost:16686).

### <a name="kiali"></a>Kiali

En nät observability instrumentpanel kommer från [Kiali][kiali]. Vidarebefordra den lokala porten *20001* på klientdatorn till port *20001* på en pod med Kiali AKS-klustret:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

Följande Exempelutdata visar port och tydlig som håller på att konfigureras för Kiali:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Du kan nu nå Kiali nät observability instrumentpanelen för tjänsten på följande URL på klientdatorn - [ http://localhost:20001 ](http://localhost:20001).

Standardanvändarnamnet och lösenordet för Kiali instrumentpanelen är *username:admin / lösenord: admin*. Dessa autentiseringsuppgifter kan konfigureras via den *kiali.dashboard.username* och *kiali.dashboard.passphrase* Helm värden.

## <a name="next-steps"></a>Nästa steg

Om du vill se hur du kan använda Istio intelligent routning mellan flera versioner av ditt program och distribuera en canary release, finns i följande dokumentation:

> [!div class="nextstepaction"]
> [AKS Istio intelligent routningsscenario][istio-scenario-routing]

Se följande officiella Istio artiklar om du vill utforska fler alternativ för Istio som installation och konfiguration:

- [Istio - snabbstarten för Kubernetes-installation][istio-install-k8s-quickstart]
- [Istio - Helm installationsguide][istio-install-helm]
- [Istio - Helm-installationsalternativ][istio-install-helm-options]

Du kan även följa ytterligare scenarier med hjälp av den [Istio Bookinfo program exempel][istio-bookinfo-example].

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-k8s-quickstart]: https://istio.io/docs/setup/kubernetes/quick-start/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/helm-install/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10
[kubernetes-crd]: https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
[Invoke-WebRequest]: /powershell/module/microsoft.powershell.utility/invoke-webrequest
[Expand-Archive]: /powershell/module/Microsoft.PowerShell.Archive/Expand-Archive
