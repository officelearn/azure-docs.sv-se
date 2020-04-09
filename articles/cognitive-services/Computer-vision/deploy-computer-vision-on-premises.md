---
title: Använd Computer Vision-behållaren med Kubernetes och Helm
titleSuffix: Azure Cognitive Services
description: Distribuera behållaren för datorseende till en Azure Container-instans och testa den i en webbläsare.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 9aac374de5af748eafbe4c22e5fc89f64e483c2a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877989"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Använd Computer Vision-behållaren med Kubernetes och Helm

Ett alternativ för att hantera dina Computer Vision-behållare lokalt är att använda Kubernetes och Helm. Med Kubernetes och Helm för att definiera en datorseende behållarevbild, skapar vi ett Kubernetes-paket. Det här paketet kommer att distribueras till ett Kubernetes-kluster lokalt. Slutligen ska vi undersöka hur du testar de distribuerade tjänsterna. Mer information om hur du kör Docker-behållare utan Kubernetes orkestrering finns i [installera och köra Datorseende-behållare](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Krav

Följande förutsättningar innan du använder Datorseende-behållare lokalt:

| Krävs | Syfte |
|----------|---------|
| Azure-konto | Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt][free-azure-account] konto innan du börjar. |
| Kubernetes CLI | [Kubernetes CLI][kubernetes-cli] krävs för att hantera delade autentiseringsuppgifter från behållarregistret. Kubernetes behövs också före Helm, som är Kubernetes pakethanterare. |
| Helm CLI | Installera [Helm CLI][helm-install], som används för att installera ett helm-diagram (containerpaketdefinition). |
| Resurs för datorseende |För att kunna använda behållaren måste du ha:<br><br>En Azure **Computer Vision-resurs** och den associerade API-nyckeln slutpunkten URI. Båda värdena är tillgängliga på sidorna Översikt och Nycklar för resursen och krävs för att starta behållaren.<br><br>**{API_KEY}**: En av de två tillgängliga resursnycklarna på sidan **Nycklar**<br><br>**{ENDPOINT_URI}**: Slutpunkten som anges på **sidan Översikt**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Krav och rekommendationer för behållare

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Ansluta till Kubernetes-klustret

Värddatorn förväntas ha ett tillgängligt Kubernetes-kluster. Se den här självstudien om [hur du distribuerar ett Kubernetes-kluster](../../aks/tutorial-kubernetes-deploy-cluster.md) för en begreppsmässig förståelse av hur du distribuerar ett Kubernetes-kluster till en värddator.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Dela Docker-autentiseringsuppgifter med Kubernetes-klustret

Om du vill tillåta Kubernetes-klustret `docker pull` till `containerpreview.azurecr.io` de konfigurerade avbildningarna från behållarregistret måste du överföra dockerautentiseringsuppgifterna till klustret. Kör [`kubectl create`][kubectl-create] kommandot nedan för att skapa en *dockerregisterhemlighet* baserat på de autentiseringsuppgifter som tillhandahålls från förutsättningen för behållarregisteråtkomst.

Kör följande kommandogränssnitt från kommandoradsgränssnittet. Var noga med `<username>` `<password>`att `<email-address>` ersätta autentiseringsuppgifterna för behållarens register.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Om du redan har `containerpreview.azurecr.io` åtkomst till behållarregistret kan du skapa en Kubernetes-hemlighet med den allmänna flaggan i stället. Tänk på följande kommando som körs mot din Docker-konfiguration JSON.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

Följande utdata skrivs ut till konsolen när hemligheten har skapats.

```console
secret "containerpreview" created
```

Om du vill kontrollera att hemligheten `secrets` har skapats kör du [`kubectl get`][kubectl-get] flaggan med flaggan.

```console
kubectl get secrets
```

Kör utskrifterna `kubectl get secrets` alla konfigurerade hemligheter.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Konfigurera Helm-diagramvärden för distribution

Börja med att skapa en mapp med namnet *read*och klistra sedan in följande YAML-innehåll i en ny fil med namnet *Chart.yml*.

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

Om du vill konfigurera standardvärdena för Helm-diagrammet kopierar och klistrar du in följande YAML i en fil med namnet `values.yaml`. Ersätt `# {ENDPOINT_URI}` kommentarerna `# {API_KEY}` och kommentarerna med dina egna värderingar.

```yaml
# These settings are deployment specific and users can provide customizations

read:
  enabled: true
  image:
    name: cognitive-services-read
    registry: containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-read
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Om `billing` värdena och `apikey` inte tillhandahålls upphör tjänsterna att gälla efter 15 min. På samma sätt kommer verifieringen att misslyckas eftersom tjänsterna inte kommer att vara tillgängliga.

Skapa en *mallmapp* under *läskatalogen.* Kopiera och klistra in följande YAML i en fil med namnet `deployment.yaml`. Filen `deployment.yaml` fungerar som en Helm-mall.

> Mallar genererar manifestfiler, som är YAML-formaterade resursbeskrivningar som Kubernetes kan förstå. [- Mallguide för Helm-diagram][chart-template-guide]

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: read
spec:
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: {{.Values.read.image.name}}
        image: {{.Values.read.image.registry}}{{.Values.read.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.read.image.args.eula}}
        - name: billing
          value: {{.Values.read.image.args.billing}}
        - name: apikey
          value: {{.Values.read.image.args.apikey}}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}

--- 
apiVersion: v1
kind: Service
metadata:
  name: read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

Mallen anger en belastningsutjämnad tjänst och distributionen av din behållare/avbildning för Läsning.

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes-paketet (Helm-sjökortet)

*Helm-diagrammet* innehåller konfigurationen för vilken dockeravbildning(er) som ska hämtas från `containerpreview.azurecr.io` behållarregistret.

> Ett [Helm-diagram][helm-charts] är en samling filer som beskriver en relaterad uppsättning Kubernetes-resurser. Ett enda diagram kan användas för att distribuera något enkelt, till exempel en memcached pod, eller något komplext, som en fullständig webbappstack med HTTP-servrar, databaser, cacheminnen och så vidare.

Medföljande *Helm-diagram* hämtar dockeravbildningarna för dataseendetjänsten `containerpreview.azurecr.io` och motsvarande tjänst från behållarregistret.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Installera Helm-diagrammet i Kubernetes-klustret

Om du vill installera *rodret måste* [`helm install`][helm-install-cmd] vi köra kommandot. Kontrollera att installationskommandot körs från `read` katalogen ovanför mappen.

```console
helm install read ./read
```

Här är ett exempel på utdata som du kan förvänta dig att se från en lyckad installation körning:

```console
NAME: read
LAST DEPLOYED: Thu Sep 04 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                    READY  STATUS             RESTARTS  AGE
read-57cb76bcf7-45sdh   0/1    ContainerCreating  0         0s

==> v1/Service
NAME     TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
read     LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME    READY  UP-TO-DATE  AVAILABLE  AGE
read    0/1    1           0          0s
```

Kubernetes-distributionen kan ta över flera minuter att slutföra. Om du vill bekräfta att både poddar och tjänster har distribuerats och är tillgängliga på rätt sätt kör du följande kommando:

```console
kubectl get all
```

Du bör förvänta dig att se något som liknar följande utdata:

```console
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/read-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/read           LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   1/1     1            1           17s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/read-57cb76bcf7   1         1         1       17s
```
<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om hur du installerar program med Helm i Azure Kubernetes Service (AKS) [finns här][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Behållare för kognitiva tjänster][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
