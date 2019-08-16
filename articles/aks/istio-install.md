---
title: Installera Istio i Azure Kubernetes service (AKS)
description: Lär dig hur du installerar och använder Istio för att skapa ett service nät i ett Azure Kubernetes service-kluster (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 032a907e45e007cb51357300e4bbf3c7afb40dde
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69542877"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Installera och använda Istio i Azure Kubernetes service (AKS)

[Istio][istio-github] är ett nät med öppen källkod som ger en nyckel uppsättning över mikrotjänster i ett Kubernetes-kluster. Dessa funktioner omfattar trafik hantering, tjänst identitet och säkerhet, princip tillämpning och upprätthållande. Mer information om Istio finns i den officiella [Istio?][istio-docs-concepts] -dokumentationen.

Den här artikeln visar hur du installerar Istio. Istio `istioctl` -klientens binärfil installeras på klient datorn och Istio-komponenterna installeras i ett Kubernetes-kluster på AKS.

> [!NOTE]
> De här anvisningarna hänvisar till `1.1.3`Istio-versionen.
>
> Istio `1.1.x` -versionerna har testats av Istio-teamet mot Kubernetes- `1.11`versioner `1.12`, `1.13`,. Du hittar fler Istio-versioner på [GitHub-Istio-][istio-github-releases] versioner och information om var och en av versionerna på [Istio-viktig][istio-release-notes]information.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Ladda ned Istio
> * Installera Istio istioctl-klientens binärfil
> * Installera Istio-CRDs på AKS
> * Installera Istio-komponenterna på AKS
> * Verifiera Istio-installationen
> * Åtkomst till tilläggen
> * Avinstallera Istio från AKS

## <a name="before-you-begin"></a>Innan du börjar

De steg som beskrivs i den här artikeln förutsätter att du har skapat ett AKS `1.11` -kluster (Kubernetes och senare, med RBAC aktiverat) `kubectl` och har upprättat en anslutning till klustret. Om du behöver hjälp med något av dessa objekt kan du se snabb starten för [AKS][aks-quickstart].

Du behöver [Helm][helm] för att följa dessa instruktioner och installera Istio. Vi rekommenderar att du har version `2.12.2` eller senare installerat och konfigurerat i klustret på rätt sätt. Om du behöver hjälp med att installera Helm kan du läsa mer i [installations vägledningen för AKS Helm][helm-install]. Alla Istio-poddar måste också vara schemalagda att köras på Linux-noder.

Se till att du har läst [Istio-prestanda och skalbarhets](https://istio.io/docs/concepts/performance-and-scalability/) dokumentation för att förstå de ytterligare resurs kraven för att köra Istio i ditt AKS-kluster. Kärn-och minnes kraven varierar beroende på din särskilda arbets belastning. Välj ett lämpligt antal noder och storlek för virtuella datorer för din installation.

Den här artikeln separerar installations vägledningen för Istio till flera diskreta steg. Slut resultatet är detsamma i strukturen som den officiella Istio installations vägledningen [][istio-install-helm].

## <a name="download-istio"></a>Ladda ned Istio

Börja med att hämta och extrahera den senaste versionen av Istio. Stegen är lite olika för ett bash-gränssnitt i MacOS, Linux eller Windows-undersystemet för Linux och för ett PowerShell-gränssnitt. Välj något av följande installations steg som matchar din önskade miljö:

* [Bash i MacOS, Linux eller Windows-undersystemet för Linux](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

I MacOS använder `curl` du för att ladda ned den senaste Istio-versionen och `tar` extrahera sedan med enligt följande:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

I Linux-eller Windows-undersystem för Linux `curl` använder du för att hämta den senaste Istio-versionen `tar` och extrahera sedan med enligt följande:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

Gå vidare till avsnittet för att [Installera Istio istioctl-klientens binärfil](#install-the-istio-istioctl-client-binary).

### <a name="powershell"></a>PowerShell

I PowerShell använder `Invoke-WebRequest` du för att ladda ned den senaste Istio-versionen och `Expand-Archive` extrahera sedan med enligt följande:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
# Use TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Gå vidare till avsnittet för att [Installera Istio istioctl-klientens binärfil](#install-the-istio-istioctl-client-binary).

## <a name="install-the-istio-istioctl-client-binary"></a>Installera Istio istioctl-klientens binärfil

> [!IMPORTANT]
> Se till att du kör stegen i det här avsnittet från mappen på den översta nivån i Istio-versionen som du laddade ned och extraherade.

`istioctl` Klientens binärfil körs på klient datorn och gör att du kan interagera med Istio-tjänstens nät. Installations stegen är lite olika mellan klient operativ system. Välj något av följande installations steg som matchar din önskade miljö:

* [MacOS](#macos)
* [Linux-eller Windows-undersystem för Linux](#linux-or-windows-subsystem-for-linux)
* [Windows](#windows)

### <a name="macos"></a>MacOS

Om du vill installera `istioctl` Istio-klientens binärfil i ett bash-baserat gränssnitt på MacOS, använder du följande kommandon. Dessa kommandon kopierar `istioctl` klientens binärfil till standard platsen för användar program `PATH`i.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Om du vill att kommando rads komplettering ska slutföras `istioctl` för Istio-klientens binärfil, så gör du det på följande sätt:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Gå vidare till nästa avsnitt för att [Installera Istio-CRDs på AKS](#install-the-istio-crds-on-aks).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux-eller Windows-undersystem för Linux

Använd följande kommandon för att installera Istio `istioctl` -klientens binärfil i ett bash-baserat gränssnitt på Linux-eller Windows-undersystem [för Linux][install-wsl]. Dessa kommandon kopierar `istioctl` klientens binärfil till standard platsen för användar program `PATH`i.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Om du vill att kommando rads komplettering ska slutföras `istioctl` för Istio-klientens binärfil, så gör du det på följande sätt:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Gå vidare till nästa avsnitt för att [Installera Istio-CRDs på AKS](#install-the-istio-crds-on-aks).

### <a name="windows"></a>Windows

Om du vill installera `istioctl` Istio-klientens binärfil i ett **PowerShell**-baserat gränssnitt i Windows, använder du följande kommandon. Dessa kommandon kopierar `istioctl` klientens binärfil till en Istio-mapp och gör den tillgänglig både direkt (i aktuellt gränssnitt) och permanent (mellan omstarter av gränssnittet) `PATH`via. Du behöver inte utökade privilegier (admin) för att köra dessa kommandon och du behöver inte starta om gränssnittet.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```

Gå vidare till nästa avsnitt för att [Installera Istio-CRDs på AKS](#install-the-istio-crds-on-aks).

## <a name="install-the-istio-crds-on-aks"></a>Installera Istio-CRDs på AKS

> [!IMPORTANT]
> Se till att du kör stegen i det här avsnittet från mappen på den översta nivån i Istio-versionen som du laddade ned och extraherade.

Istio använder [anpassade resurs definitioner (CRDs)][kubernetes-crd] för att hantera körnings konfigurationen. Vi måste installera Istio-CRDs först eftersom Istio-komponenterna har ett beroende på dem. Använd Helm och `istio-init` diagrammet för att installera Istio-CRDs `istio-system` i namn området i ditt AKS-kluster:

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

[Jobben][kubernetes-jobs] distribueras som en del av `istio-init` Helm-diagrammet för att installera CRDs. Dessa jobb bör ta mellan 1 och 2 minuter att slutföra, beroende på kluster miljön. Du kan kontrol lera att jobben har slutförts på följande sätt:

```azurecli
kubectl get jobs -n istio-system
```

Följande exempel på utdata visar att jobben har slutförts.

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

Nu när vi har bekräftat att jobben har slutförts, ska vi kontrol lera att vi har rätt antal Istio-CRDs installerade. Du kan kontrol lera att alla 53 Istio-CRDs har installerats genom att köra lämpligt kommando för din miljö. Kommandot ska returnera talet `53`.

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

Powershell

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

Om du har kommit till den här punkten betyder det att du har installerat Istio-CRDs. Gå vidare till nästa avsnitt för att [Installera Istio-komponenterna på AKS](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>Installera Istio-komponenterna på AKS

> [!IMPORTANT]
> Se till att du kör stegen i det här avsnittet från mappen på den översta nivån i Istio-versionen som du laddade ned och extraherade.

Vi kommer att installera [Grafana][grafana] och [Kiali][kiali] som en del av vår Istio-installation. Grafana tillhandahåller instrument paneler för analys och övervakning, och Kiali tillhandahåller en instrument panel för service nät. I vår installation kräver var och en av dessa komponenter autentiseringsuppgifter som måste tillhandahållas som en [hemlighet][kubernetes-secrets].

Innan vi kan installera Istio-komponenterna måste vi skapa hemligheterna för både Grafana och Kiali. Skapa dessa hemligheter genom att köra lämpliga kommandon för din miljö.

### <a name="add-grafana-secret"></a>Lägg till Grafana-hemlighet

`REPLACE_WITH_YOUR_SECURE_PASSWORD` Ersätt token med ditt lösen ord och kör följande kommandon:

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

### <a name="add-kiali-secret"></a>Lägg till Kiali-hemlighet

`REPLACE_WITH_YOUR_SECURE_PASSWORD` Ersätt token med ditt lösen ord och kör följande kommandon:

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

### <a name="install-istio-components"></a>Installera Istio-komponenter

Nu när vi har skapat Grafana-och Kiali-hemligheterna i vårt AKS-kluster är det dags att installera Istio-komponenterna. Använd Helm och `istio` diagrammet för att installera Istio-komponenterna `istio-system` i namn området i ditt AKS-kluster. Använd lämpliga kommandon för din miljö.

> [!NOTE]
> Vi använder följande alternativ som en del av installationen:
> - `global.controlPlaneSecurityEnabled=true`– ömsesidig TLS har Aktiver ATS för kontroll planet
> - `mixer.adapters.useAdapterCRDs=false`-ta bort bevakningar om mixer-CRDs så att de blir inaktuella och förbättrar prestandan
> - `grafana.enabled=true`-Aktivera Grafana-distribution för analys-och övervaknings instrument paneler
> - `grafana.security.enabled=true`-aktivera autentisering för Grafana
> - `tracing.enabled=true`-Aktivera Jaeger-distributionen för spårning
> - `kiali.enabled=true`– Aktivera Kiali-distributionen för en instrument panel för service nät

Bash

```bash
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set mixer.adapters.useAdapterCRDs=false \
  --set grafana.enabled=true --set grafana.security.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

Powershell

```powershell
helm install install/kubernetes/helm/istio --name istio --namespace istio-system `
  --set global.controlPlaneSecurityEnabled=true `
  --set mixer.adapters.useAdapterCRDs=false `
  --set grafana.enabled=true --set grafana.security.enabled=true `
  --set tracing.enabled=true `
  --set kiali.enabled=true
```

`istio` Helm-diagrammet distribuerar ett stort antal objekt. Du kan se listan från utdata `helm install` från kommandot ovan. Det kan ta 4 till fem minuter att slutföra distributionen av Istio-komponenterna, beroende på kluster miljön.

> [!NOTE]
> Alla Istio-poddar måste vara schemalagda att köras på Linux-noder. Om du har Windows Server-adresspooler utöver Linux-noder i klustret, kontrollerar du att alla Istio-poddar har schemalagts att köras på Linux-noder.

Nu har du distribuerat Istio till ditt AKS-kluster. För att säkerställa att vi har en lyckad distribution av Istio kan vi gå vidare till nästa avsnitt för att [validera Istio-installationen](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Verifiera Istio-installationen

Bekräfta först att de förväntade tjänsterna har skapats. Använd kommandot [kubectl get SVC][kubectl-get] för att visa de tjänster som körs. Fråga namn området där Istio och tilläggs komponenter har installerats `istio` av Helm-diagrammet: `istio-system`

```console
kubectl get svc --namespace istio-system --output wide
```

Följande exempel på utdata visar de tjänster som nu ska köras:

- `istio-*`Terminal
- `jaeger-*`, `tracing` och`zipkin` tillägg spårnings tjänster
- `prometheus`tilläggs mått tjänst
- `grafana`instrument panels tjänsten för analys och övervakning av tillägg
- `kiali`tjänst för nät instrument panel för tillägg till service

Om visar `istio-ingressgateway` en extern `<pending>`IP-adress väntar du några minuter tills en IP-adress har tilldelats av Azure-nätverk.

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

Bekräfta sedan att nödvändiga poddar har skapats. Använd kommandot [kubectl get poddar][kubectl-get] och fråga `istio-system` namn området igen:

```console
kubectl get pods --namespace istio-system
```

Följande exempel på utdata visar de poddar som kör:

- `istio-*` poddar
- `prometheus-*` måtten Pod
- instrument `grafana-*` panelens Pod för analys och övervakning av tillägg
- Pod `kiali` för tillägg till tjänstens nät instrument panel

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

Det måste finnas två `istio-init-crd-*` poddar med en `Completed` status. Dessa poddar var ansvariga för att köra jobb som skapade CRDs i ett tidigare steg. Alla andra poddar bör visa statusen `Running`. Om din poddar inte har dessa status värden väntar du en minut eller två tills de gör det. Om en poddar rapporterar ett problem använder du kommandot [kubectl beskriver Pod][kubectl-describe] för att granska deras utdata och status.

## <a name="accessing-the-add-ons"></a>Åtkomst till tilläggen

Ett antal tillägg installerades Istio i vår installation ovan som tillhandahåller ytterligare funktioner. Användar gränssnitten för tilläggen visas inte offentligt via en extern IP-adress. Använd kommandot [kubectl Port-Forward][kubectl-port-forward] för att få åtkomst till användar gränssnitten för tillägg. Det här kommandot skapar en säker anslutning mellan klient datorn och relevanta Pod i ditt AKS-kluster.

Vi har lagt till ett extra säkerhets lager för Grafana och Kiali genom att ange autentiseringsuppgifter för dem tidigare i den här artikeln.

### <a name="grafana"></a>Grafana

Instrument paneler för analys och övervakning av Istio tillhandahålls av [Grafana][grafana]. Vidarebefordra den lokala porten `3000` på klient datorn till porten `3000` på den Pod som kör Grafana i ditt AKS-kluster:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

Följande exempel på utdata visar de Port-forwards som kon figurer ATS för Grafana:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Nu kan du komma åt Grafana på följande URL på klient datorn – [http://localhost:3000](http://localhost:3000). Kom ihåg att använda de autentiseringsuppgifter som du skapade via Grafana-hemligheten tidigare när du uppmanas till det.

### <a name="prometheus"></a>Prometheus

Mått för Istio tillhandahålls av [Prometheus][prometheus]. Vidarebefordra den lokala porten `9090` på klient datorn till porten `9090` på den Pod som kör Prometheus i ditt AKS-kluster:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090
```

Följande exempel på utdata visar de Port-forwards som kon figurer ATS för Prometheus:

```console
Forwarding from 127.0.0.1:9090 -> 9090
Forwarding from [::1]:9090 -> 9090
```

Nu kan du komma åt Prometheus Expression browser på följande URL på klient datorn – [http://localhost:9090](http://localhost:9090).

### <a name="jaeger"></a>Jaeger

Spårning i Istio tillhandahålls av [Jaeger][jaeger]. Vidarebefordra den lokala porten `16686` på klient datorn till porten `16686` på den Pod som kör Jaeger i ditt AKS-kluster:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686
```

Följande exempel på utdata visar de Port-forwards som kon figurer ATS för Jaeger:

```console
Forwarding from 127.0.0.1:16686 -> 16686
Forwarding from [::1]:16686 -> 16686
```

Nu kan du komma åt användar gränssnittet för Jaeger-spårning på följande URL på klient datorn – [http://localhost:16686](http://localhost:16686).

### <a name="kiali"></a>Kiali

En instrument panel för service nätets instrument panel tillhandahålls av [Kiali][kiali]. Vidarebefordra den lokala porten `20001` på klient datorn till porten `20001` på den Pod som kör Kiali i ditt AKS-kluster:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

Följande exempel på utdata visar de Port-forwards som kon figurer ATS för Kiali:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Nu kan du gå till instrument panelen för Kiali service nät på följande URL på klient datorn – [http://localhost:20001/kiali/console/](http://localhost:20001/kiali/console/). Kom ihåg att använda de autentiseringsuppgifter som du skapade via Kiali-hemligheten tidigare när du uppmanas till det.

## <a name="uninstall-istio-from-aks"></a>Avinstallera Istio från AKS

> [!WARNING]
> Om du tar bort Istio från ett system som körs kan det leda till trafik problem mellan dina tjänster. Se till att du har gjort att systemet fortfarande fungerar korrekt utan Istio innan du fortsätter.

### <a name="remove-istio-components-and-namespace"></a>Ta bort Istio-komponenter och namn område

Använd följande kommandon för att ta bort Istio från ditt AKS-kluster. `helm delete` Kommandona `istio-init`tarbort - `istio` och-`istio-system` diagrammen och kommandottarbortnamnområdet.`kubectl delete ns`

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>Ta bort Istio CRDs

Kommandona ovan tar bort alla Istio-komponenter och namn området, men vi kommer fortfarande till Istio-CRDs. Om du vill ta bort CRDs kan du använda någon av följande metoder.

Metoden #1 – det här kommandot förutsätter att du kör det här steget från mappen på den översta nivån i den nedladdade och extraherade versionen av Istio som du använde för att installera Istio med.

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

Metoden #2 – Använd ett av dessa kommandon om du inte längre har åtkomst till den nedladdade och extraherade versionen av Istio som du använde för att installera Istio med. Det här kommandot tar lite längre tid eftersom det tar några minuter att slutföra.

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

Powershell
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>Nästa steg

I följande dokumentation beskrivs hur du kan använda Istio för att tillhandahålla intelligent routning för att distribuera en Kanarie-version:

> [!div class="nextstepaction"]
> [AKS Istio intelligent routning-scenario][istio-scenario-routing]

Om du vill utforska fler installations-och konfigurations alternativ för Istio kan du läsa följande officiella Istio-artiklar:

- [Installations guide för Istio-Helm][istio-install-helm]
- [Installations alternativ för Istio-Helm][istio-install-helm-options]

Du kan också följa ytterligare scenarier med hjälp av [Istio Bookinfo][istio-bookinfo-example]-programexempelet.

Information om hur du övervakar ditt AKS-program med hjälp av Application Insights och Istio finns i följande Azure Monitor-dokumentation:
- [Program övervakning utan Instrumentation för Kubernetes-värdbaserade program][app-insights]

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

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
