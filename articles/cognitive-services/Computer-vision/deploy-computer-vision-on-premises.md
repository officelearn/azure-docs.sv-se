---
title: Använd Visuellt innehåll container med Kubernetes och Helm
titleSuffix: Azure Cognitive Services
description: Distribuera Visuellt innehåll-behållaren till en Azure Container instance och testa den i en webbläsare.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 9aac374de5af748eafbe4c22e5fc89f64e483c2a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877989"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Använd Visuellt innehåll container med Kubernetes och Helm

Ett alternativ för att hantera dina Visuellt innehåll behållare lokalt är att använda Kubernetes och Helm. Med Kubernetes och Helm för att definiera en Visuellt innehåll behållar avbildning skapar vi ett Kubernetes-paket. Det här paketet kommer att distribueras till ett Kubernetes-kluster lokalt. Slutligen ska vi utforska hur du testar de distribuerade tjänsterna. Mer information om att köra Docker-behållare utan Kubernetes-dirigering finns i [Installera och köra visuellt innehåll behållare](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Krav

Följande krav gäller innan du använder Visuellt innehåll behållare lokalt:

| Krävs | Syfte |
|----------|---------|
| Azure-konto | Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt][free-azure-account] konto innan du börjar. |
| Kubernetes CLI | [KUBERNETES CLI][kubernetes-cli] krävs för att hantera delade autentiseringsuppgifter från behållar registret. Kubernetes krävs också innan Helm, som är Kubernetes Package Manager. |
| Helm CLI | Installera [Helm CLI][helm-install], som används för att installera ett Helm-diagram (definition av container paket). |
| Visuellt innehåll resurs |För att du ska kunna använda behållaren måste du ha:<br><br>En Azure **visuellt innehåll** -resurs och den tillhör ande API-nyckeln slut punkts-URI. Båda värdena är tillgängliga på sidorna översikt och nycklar för resursen och krävs för att starta behållaren.<br><br>**{Api_key}**: en av de två tillgängliga resurs nycklarna på sidan **nycklar**<br><br>**{ENDPOINT_URI}**: slut punkten enligt vad som anges på sidan **Översikt**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Krav och rekommendationer för behållare

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Ansluta till Kubernetes-klustret

Värddatorn förväntas ha ett tillgängligt Kubernetes-kluster. I den här självstudien om hur du [distribuerar ett Kubernetes-kluster](../../aks/tutorial-kubernetes-deploy-cluster.md) kan du få en uppfattning om hur du distribuerar ett Kubernetes-kluster till en värddator.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Dela Docker-autentiseringsuppgifter med Kubernetes-klustret

Om du vill tillåta Kubernetes- `docker pull` klustret till de konfigurerade avbildningarna från `containerpreview.azurecr.io` behållar registret måste du överföra Docker-autentiseringsuppgifterna till klustret. Kör [`kubectl create`][kubectl-create] kommandot nedan för att skapa en *Docker-register hemlighet* baserat på de autentiseringsuppgifter som tillhandahålls från behållar registrets åtkomst krav.

Kör följande kommando från det kommando rads gränssnitt du väljer. Se till att ersätta `<username>`, `<password>`och `<email-address>` med autentiseringsuppgifterna för behållar registret.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Om du redan har åtkomst till `containerpreview.azurecr.io` behållar registret kan du skapa en Kubernetes-hemlighet med hjälp av den generiska flaggan i stället. Överväg följande kommando som körs mot din Docker-konfigurations-JSON.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

Följande utdata skrivs ut till-konsolen när hemligheten har skapats.

```console
secret "containerpreview" created
```

Verifiera att hemligheten har skapats genom att köra [`kubectl get`][kubectl-get] med- `secrets` flaggan.

```console
kubectl get secrets
```

Att `kubectl get secrets` köra skriver ut alla konfigurerade hemligheter.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Konfigurera Helm för distribution

Börja med att skapa en mapp med namnet *Read*och klistra sedan in följande yaml-innehåll i en ny fil med namnet *Chart. yml*.

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

Om du vill konfigurera standardvärdena för Helm-diagrammet kopierar du och klistrar in följande `values.yaml`yaml i en fil med namnet. Ersätt kommentarerna `# {ENDPOINT_URI}` och `# {API_KEY}` med dina egna värden.

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
> Om värdena `billing` och `apikey` inte har angetts upphör tjänsterna att gälla efter 15 min. Det går inte heller att verifiera eftersom tjänsterna inte är tillgängliga.

Skapa en mapp för *mallar* under *Läs* katalogen. Kopiera och klistra in följande YAML i en fil med `deployment.yaml`namnet. `deployment.yaml` Filen fungerar som en Helm-mall.

> Mallar genererar MANIFEST filer, som är YAML-formaterade resurs beskrivningar som Kubernetes kan förstå. [– Helm diagram mal len guide][chart-template-guide]

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

Mallen anger en belastnings Utjämnings tjänst och distributionen av din behållare/avbildning för läsning.

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes-paketet (Helm-diagrammet)

*Helm-diagrammet* innehåller konfigurationen av vilka Docker-avbildningar som ska hämtas från `containerpreview.azurecr.io` behållar registret.

> Ett [Helm-diagram][helm-charts] är en samling filer som beskriver en relaterad uppsättning Kubernetes-resurser. Ett enkelt diagram kan användas för att distribuera något enkelt, t. ex. en memcached POD eller något komplext, t. ex. en fullständig webbapp med HTTP-servrar, databaser, cacheminnen och så vidare.

De tillhandahållna *Helm-diagrammen* hämtar Docker-avbildningarna av den visuellt innehåll tjänsten och motsvarande tjänst från `containerpreview.azurecr.io` behållar registret.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Installera Helm-diagrammet på Kubernetes-klustret

För att kunna installera *Helm-diagrammet*måste vi köra [`helm install`][helm-install-cmd] kommandot. Se till att köra kommandot Install från katalogen ovanför `read` mappen.

```console
helm install read ./read
```

Här är ett exempel på utdata som du kan förväntas se från en lyckad installations körning:

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

Kubernetes-distributionen kan ta flera minuter att slutföra. För att bekräfta att både poddar och tjänsterna är korrekt distribuerade och tillgängliga, kör du följande kommando:

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

Mer information om hur du installerar program med Helm i Azure Kubernetes service (AKS) [finns här][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Cognitive Services behållare][cog-svcs-containers]

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
