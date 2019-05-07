---
title: Installera Istio i Azure Kubernetes Service (AKS)
description: Lär dig hur du installerar och använder Istio för att skapa en tjänst-nät i ett kluster i Azure Kubernetes Service (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: fc95ce4aad4e8597b02b9c862be33bfcf6185541
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073809"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Installera och använda Istio i Azure Kubernetes Service (AKS)

[Istio] [ istio-github] är en öppen källkod service-nät som ger en nyckeluppsättning av funktioner i mikrotjänster i ett Kubernetes-kluster. Dessa funktioner innefattar trafikhantering, identitet och säkerhet, genomförande av principer och observability. Mer information om Istio finns i officiellt [vad är Istio?] [ istio-docs-concepts] dokumentation.

Den här artikeln visar hur du installerar Istio. Istio `istioctl` binära klienten har installerats på klientdatorn och Istio-komponenter är installerade i en Kubernetes-kluster i AKS.

> [!NOTE]
> Dessa instruktioner referera Istio version `1.1.3`.
>
> Istio `1.1.x` versioner har testats av teamet Istio mot Kubernetes-versioner `1.11`, `1.12`, `1.13`. Du hittar ytterligare Istio versioner på [GitHub - Istio släpper] [ istio-github-releases] och information om alla versioner på [Istio – viktig information] [ istio-release-notes].

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Ladda ned Istio
> * Installera binär Istio istioctl klienten
> * Installera Istio CRD på AKS
> * Installera komponenterna för Istio på AKS
> * Verifiera installationen Istio
> * Åtkomst till tillägg
> * Avinstallera Istio från AKS

## <a name="before-you-begin"></a>Innan du börjar

Stegen som beskrivs i den här artikeln förutsätter att du har skapat ett AKS-kluster (Kubernetes `1.11` och ovan, med RBAC aktiverad) och har upprättat en `kubectl` anslutning med klustret. Om du behöver hjälp med något av dessa objekt läser den [AKS-Snabbstart][aks-quickstart].

Du behöver [Helm] [ helm] att följa dessa anvisningar och installera Istio. Vi rekommenderar att du har version `2.12.2` eller senare korrekt installerat och konfigurerat i klustret. Om du behöver hjälp med att installera Helm läser den [vägledning för installation av AKS Helm][helm-install]. Alla Istio poddar måste också schemaläggas att köras på Linux-noder.

Den här artikeln separerar Istio installation vägledning i flera separata steg. Slutresultatet är samma i struktur som den officiella Istio installationen [vägledning][istio-install-helm].

## <a name="download-istio"></a>Ladda ned Istio

Först ladda ned och extrahera den senaste versionen av Istio. Stegen är lite annorlunda för ett bash-gränssnitt på MacOS, Linux eller Windows-undersystem för Linux och ett PowerShell-gränssnitt. Välj något av följande steg i installationen, som matchar din miljö:

* [Bash på MacOS, Linux eller Windows-undersystem för Linux](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

På MacOS, använder du `curl` ladda ned den senaste versionen av Istio och sedan extrahera med `tar` på följande sätt:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

På Linux eller Windows-undersystem for Linux kan använda `curl` ladda ned den senaste versionen av Istio och sedan extrahera med `tar` på följande sätt:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

Nu gå vidare till avsnittet [installera Istio istioctl klienten binära](#install-the-istio-istioctl-client-binary).

### <a name="powershell"></a>PowerShell

I PowerShell använder `Invoke-WebRequest` ladda ned den senaste versionen av Istio och sedan extrahera med `Expand-Archive` på följande sätt:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Nu gå vidare till avsnittet [installera Istio istioctl klienten binära](#install-the-istio-istioctl-client-binary).

## <a name="install-the-istio-istioctl-client-binary"></a>Installera binär Istio istioctl klienten

> [!IMPORTANT]
> Kontrollera att du kört stegen i det här avsnittet från den översta mappen av Istio-versionen som du har hämtat och extraherat.

Den `istioctl` klienten binära körs på klientdatorn och gör att du kan interagera med Istio service nät. Installera stegen skiljer sig lite mellan klientoperativsystem. Välj något av följande steg i installationen, som matchar din miljö:

* [MacOS](#macos)
* [Linux eller Windows-undersystem for Linux](#linux-or-windows-subsystem-for-linux)
* [Windows](#windows)

### <a name="macos"></a>MacOS

Installera Istio `istioctl` klientens binärfil i ett bash-baserat gränssnitt på MacOS, Använd följande kommandon. De här kommandona kopiera den `istioctl` klienten binär till platsen för standardanvändare installationsprogrammet i din `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
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

Nu gå vidare till nästa avsnitt för att [installera Istio CRD på AKS](#install-the-istio-crds-on-aks).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux eller Windows-undersystem for Linux

Använd följande kommandon för att installera Istio `istioctl` klienten binära i ett bash-baserat gränssnitt på Linux eller [Windows-undersystem for Linux][install-wsl]. De här kommandona kopiera den `istioctl` klienten binär till platsen för standardanvändare installationsprogrammet i din `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
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

Nu gå vidare till nästa avsnitt för att [installera Istio CRD på AKS](#install-the-istio-crds-on-aks).

### <a name="windows"></a>Windows

Att installera Istio `istioctl` klienten binära i en **Powershell**-baserat gränssnitt på Windows, använder du följande kommandon. De här kommandona kopiera den `istioctl` klienten binär till en mapp för Istio är permanent tillgänglig via din `PATH`. Du behöver inte förhöjd behörighet för (Admin) att köra dessa kommandon.

```powershell
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"
$PATH = [environment]::GetEnvironmentVariable("PATH", "User")
[environment]::SetEnvironmentVariable("PATH", $PATH + "; C:\Istio\", "User")
```

Nu gå vidare till nästa avsnitt för att [installera Istio CRD på AKS](#install-the-istio-crds-on-aks).

## <a name="install-the-istio-crds-on-aks"></a>Installera Istio CRD på AKS

> [!IMPORTANT]
> Kontrollera att du kört stegen i det här avsnittet från den översta mappen av Istio-versionen som du har hämtat och extraherat.

Istio använder [anpassade Resursdefinitionerna (CRD)] [ kubernetes-crd] att hantera dess runtime-konfigurationen. Vi måste du installera Istio CRD först, eftersom komponenten Istio har ett beroende på dem. Använd Helm och `istio-init` diagram om du vill installera Istio CRD till den `istio-system` namnområde i AKS-klustret:

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

[Jobb] [ kubernetes-jobs] distribueras som en del av den `istio-init` Helm-diagrammet för att installera CRD. Dessa jobb bör ta mellan 1 till 2 minuter att slutföra beroende på klustermiljön. Du kan verifiera att jobben har slutförts på följande sätt:

```azurecli
kubectl get jobs -n istio-system
```

Följande Exempelutdata visar jobben har slutförts.

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

Nu när vi har bekräftat slutförts och lyckats jobb, ska vi Kontrollera att vi har rätt antal Istio CRD installerad. Du kan kontrollera att alla 53 Istio CRD har installerats genom att köra kommandot lämplig för din miljö. Kommandot ska returnera antalet `53`.

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

PowerShell

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

Om du har hittills sedan det innebär att du har installerat Istio CRD. Nu gå vidare till nästa avsnitt för att [installera komponenterna för Istio på AKS](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>Installera komponenterna för Istio på AKS

> [!IMPORTANT]
> Kontrollera att du kört stegen i det här avsnittet från den översta mappen av Istio-versionen som du har hämtat och extraherat.

Vi kommer att installera [Grafana] [ grafana] och [Kiali] [ kiali] som en del av vår Istio-installationen. Grafana tillhandahåller analys och övervakning av instrumentpaneler och Kiali tillhandahåller en nät observability instrumentpanel. I vår installationsprogrammet, var och en av komponenterna kräver autentiseringsuppgifter som måste anges som en [hemlighet][kubernetes-secrets].

Innan vi kan installera Istio-komponenter, måste vi skapa hemligheterna för både Grafana och Kiali. Skapa dessa hemligheter genom att köra kommandona lämplig för din miljö.

### <a name="add-grafana-secret"></a>Lägga till Grafana hemlighet

Ersätt den `REPLACE_WITH_YOUR_SECURE_PASSWORD` token med ditt lösenord och kör följande kommandon:

#### <a name="macos-linux"></a>MacOS, Linux

```bash
GRAFANA_USERNAME=$(echo -n "grafana" | base64)
GRAFANA_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$GRAFANA_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("grafana"))
$GRAFANA_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE" | kubectl apply -f -
```

### <a name="add-kiali-secret"></a>Lägga till Kiali hemlighet

Ersätt den `REPLACE_WITH_YOUR_SECURE_PASSWORD` token med ditt lösenord och kör följande kommandon:

#### <a name="macos-linux"></a>MacOS, Linux

```bash
KIALI_USERNAME=$(echo -n "kiali" | base64)
KIALI_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$KIALI_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("kiali"))
$KIALI_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE" | kubectl apply -f -
```

### <a name="install-istio-components"></a>Installera komponenter för Istio

Nu när vi har nu skapat Grafana och Kiali hemligheterna i vår AKS-kluster, är det dags att installera Istio-komponenter. Använd Helm och `istio` diagram om du vill installera Istio komponenterna i den `istio-system` namnområde i AKS-klustret. Använd lämpliga kommandon för din miljö.

> [!NOTE]
> Vi använder följande alternativ som en del av vår installation:
> - `global.controlPlaneSecurityEnabled=true` -ömsesidig TLS som aktiverats för kontrollplanet
> - `mixer.adapters.useAdapterCRDs=false` -ta bort ur mixer-kort CRD eftersom de till att bli inaktuella och detta förbättrar prestanda
> - `grafana.enabled=true` -Aktivera Grafana distribution för analys och övervakning instrumentpaneler
> - `grafana.security.enabled=true` -aktivera autentisering för Grafana
> - `tracing.enabled=true` -Aktivera Jaeger distribution för spårning
> - `kiali.enabled=true` -Aktivera Kiali distributionen för en nät observability instrumentpanel

Bash

```bash
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set mixer.adapters.useAdapterCRDs=false \
  --set grafana.enabled=true --set grafana.security.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

PowerShell

```powershell
helm install install/kubernetes/helm/istio --name istio --namespace istio-system `
  --set global.controlPlaneSecurityEnabled=true `
  --set mixer.adapters.useAdapterCRDs=false `
  --set grafana.enabled=true --set grafana.security.enabled=true `
  --set tracing.enabled=true `
  --set kiali.enabled=true
```

Den `istio` Helm-diagrammet distribuerar ett stort antal objekt. Du kan se en lista från utdata från din `helm install` kommandot ovan. Distributionen av Istio-komponenter kan ta 4 till 5 minuter att slutföra beroende på klustermiljön.

> [!NOTE]
> Alla Istio poddar måste schemaläggas att köras på Linux-noder. Om du har Windows Server nodpooler förutom Linux nodpooler i ditt kluster, kontrollerar du att alla Istio poddar har schemalagts att köras på Linux-noder.

Nu har du distribuerat Istio till AKS-klustret. För att säkerställa att vi har en lyckad distribution av Istio, vi går vidare till nästa avsnitt för att [verifierar installationen Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Verifiera installationen Istio

Först bekräfta att de förväntade tjänsterna har skapats. Använd den [kubectl hämta svc] [ kubectl-get] kommando för att visa tjänsterna som körs. Fråga den `istio-system` namnområdet, där komponenterna för Istio och tillägg har installerats av den `istio` Helm-diagrammet:

```console
kubectl get svc --namespace istio-system --output wide
```

Följande Exempelutdata visar de tjänster som bör nu köras:

- `istio-*` Tjänster
- `jaeger-*`, `tracing`, och `zipkin` spårning tilläggstjänster
- `prometheus` måtttjänsten för tillägg
- `grafana` tillägg analyser och instrumentpanel för övervakningstjänsten
- `kiali` tillägg nät instrumentpanel-tjänsten

Om den `istio-ingressgateway` visas ett externa IP-adressen för `<pending>`, Vänta några minuter tills en IP-adress har tilldelats genom Azure-nätverk.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                                                                                                                                      AGE       SELECTOR
grafana                  ClusterIP      10.0.81.182    <none>          3000/TCP                                                                                                                                     119s      app=grafana
istio-citadel            ClusterIP      10.0.96.33     <none>          8060/TCP,15014/TCP                                                                                                                           119s      istio=citadel
istio-galley             ClusterIP      10.0.237.158   <none>          443/TCP,15014/TCP,9901/TCP                                                                                                                   119s      istio=galley
istio-ingressgateway     LoadBalancer   10.0.154.12    20.188.211.19   15020:30603/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:31198/TCP,15030:30610/TCP,15031:30937/TCP,15032:31344/TCP,15443:31499/TCP   119s      app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.178.56    <none>          15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       119s      istio=pilot
istio-policy             ClusterIP      10.0.116.118   <none>          9091/TCP,15004/TCP,15014/TCP                                                                                                                 119s      istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.31.160    <none>          443/TCP                                                                                                                                      119s      istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.187.246   <none>          9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       119s      istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>          5775/UDP,6831/UDP,6832/UDP                                                                                                                   119s      app=jaeger
jaeger-collector         ClusterIP      10.0.116.63    <none>          14267/TCP,14268/TCP                                                                                                                          119s      app=jaeger
jaeger-query             ClusterIP      10.0.22.108    <none>          16686/TCP                                                                                                                                    119s      app=jaeger
kiali                    ClusterIP      10.0.142.50    <none>          20001/TCP                                                                                                                                    119s      app=kiali
prometheus               ClusterIP      10.0.138.134   <none>          9090/TCP                                                                                                                                     119s      app=prometheus
tracing                  ClusterIP      10.0.165.210   <none>          80/TCP                                                                                                                                       118s      app=jaeger
zipkin                   ClusterIP      10.0.126.211   <none>          9411/TCP                                                                                                                                     118s      app=jaeger
```

Bekräfta sedan att nödvändiga poddarna har skapats. Använd den [kubectl hämta poddar] [ kubectl-get] kommando och fråga igen den `istio-system` namnområde:

```console
kubectl get pods --namespace istio-system
```

Följande Exempelutdata visar poddarna som kör:

- den `istio-*` poddar
- den `prometheus-*` tillägg mått pod
- den `grafana-*` tillägg analys och övervakning instrumentpanelen pod
- den `kiali` tillägg service nät instrumentpanelen pod

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-88779954d-nzpm7                  1/1       Running     0          6m26s
istio-citadel-7f699dc8c8-n7q8g           1/1       Running     0          6m26s
istio-galley-649bc8cd97-wfjzm            1/1       Running     0          6m26s
istio-ingressgateway-65dfbd566-42wkn     1/1       Running     0          6m26s
istio-init-crd-10-tmtw5                  0/1       Completed   0          20m38s
istio-init-crd-11-ql25l                  0/1       Completed   0          20m38s
istio-pilot-958dd8cc4-4ckf9              2/2       Running     0          6m26s
istio-policy-86b4b7cf9-zf7v7             2/2       Running     4          6m26s
istio-sidecar-injector-d48786c5c-pmrj9   1/1       Running     0          6m26s
istio-telemetry-7f6996fdcc-84w94         2/2       Running     3          6m26s
istio-tracing-79db5954f-h7hmz            1/1       Running     0          6m26s
kiali-5c4cdbb869-s28dv                   1/1       Running     0          6m26s
prometheus-67599bf55b-pgxd8              1/1       Running     0          6m26s
```

Det bör finnas två `istio-init-crd-*` poddar med en `Completed` status. Dessa poddar var ansvarig för att köra de jobb som skapats i CRD i ett tidigare steg. Alla andra poddarna bör ha statusen `Running`. Om dina poddar inte har dessa statusar, vänta en minut eller två tills de gör. Om alla poddar rapportera ett problem, använder du den [kubectl beskriver pod] [ kubectl-describe] att granska sina utdata och status.

## <a name="accessing-the-add-ons"></a>Åtkomst till tillägg

Ett antal tillägg har installerats Istio i vår installationsprogrammet som ger ytterligare funktioner. Användargränssnitt för tilläggen exponeras inte offentligt via en extern ip-adress. För att komma åt användargränssnitt tillägg, använda den [kubectl port och tydlig] [ kubectl-port-forward] kommando. Det här kommandot skapar en säker anslutning mellan klientdatorn och relevanta pod AKS-klustret.

Vi lagt till ett extra lager av säkerhet för Grafana och Kiali genom att ange autentiseringsuppgifter för dem tidigare i den här artikeln.

### <a name="grafana"></a>Grafana

Analyser och övervakning instrumentpaneler för Istio tillhandahålls av [Grafana][grafana]. Vidarebefordra den lokala porten `3000` på klientdatorn till port `3000` på en pod med Grafana AKS-klustret:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

Följande Exempelutdata visar port och tydlig som håller på att konfigureras för Grafana:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Du kan nu nå Grafana på följande URL på klientdatorn - [ http://localhost:3000 ](http://localhost:3000). Kom ihåg att använda de autentiseringsuppgifter som du skapade via Grafana hemliga tidigare när du uppmanas till detta.

### <a name="prometheus"></a>Prometheus

Mått för Istio tillhandahålls av [Prometheus][prometheus]. Vidarebefordra den lokala porten `9090` på klientdatorn till port `9090` på en pod med Prometheus AKS-klustret:

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

Spårning i Istio tillhandahålls av [Jaeger][jaeger]. Vidarebefordra den lokala porten `16686` på klientdatorn till port `16686` på en pod med Jaeger AKS-klustret:

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

En nät observability instrumentpanel kommer från [Kiali][kiali]. Vidarebefordra den lokala porten `20001` på klientdatorn till port `20001` på en pod med Kiali AKS-klustret:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

Följande Exempelutdata visar port och tydlig som håller på att konfigureras för Kiali:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Du kan nu nå Kiali nät observability instrumentpanelen för tjänsten på följande URL på klientdatorn - [ http://localhost:20001/kiali/console/ ](http://localhost:20001/kiali/console/). Kom ihåg att använda de autentiseringsuppgifter som du skapade via Kiali hemliga tidigare när du uppmanas till detta.

## <a name="uninstall-istio-from-aks"></a>Avinstallera Istio från AKS

> [!WARNING]
> Tar bort Istio från ett system kan resultera i trafik-relaterade problem mellan dina tjänster. Se till att du har gjort etablerar felkod fortfarande fungera utan Istio innan du fortsätter.

### <a name="remove-istio-components-and-namespace"></a>Ta bort Istio komponenter och namnområde

Använd följande kommandon för att ta bort Istio från ditt AKS-kluster. Den `helm delete` -kommandon tar bort den `istio` och `istio-init` diagram, och `kubectl delete ns` kommandot tar bort den `istio-system` namnområde.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>Ta bort Istio CRD

Kommandona ovan kan du ta bort alla komponenter för Istio och namnrymd, men vi fortfarande kvar med Istio CRD. Om du vill ta bort CRD, kan du använda en följande metoder.

Metoden #1 – det här kommandot förutsätter att du kör det här steget från den översta mappen av den hämta och extraherade versionen av Istio som användes för att installera Istio med.

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

Metoden #2 – Använd ett av följande kommandon om du inte längre har åtkomst till den hämta och extraherade versionen av Istio som användes för att installera Istio med. Det här kommandot tar lite längre tid - kommer att ta ett par minuter att slutföra.

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

PowerShell
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>Nästa steg

Följande dokumentation beskrivs hur du kan använda Istio för att tillhandahålla intelligent routning för att lansera en kontrollvärde version:

> [!div class="nextstepaction"]
> [AKS Istio intelligent routningsscenario][istio-scenario-routing]

Se följande officiella Istio artiklar om du vill utforska fler alternativ för Istio som installation och konfiguration:

- [Istio - Helm installationsguide][istio-install-helm]
- [Istio - Helm-installationsalternativ][istio-install-helm-options]

Du kan även följa ytterligare scenarier med hjälp av den [Istio Bookinfo program exempel][istio-bookinfo-example].

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/about/notes/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md