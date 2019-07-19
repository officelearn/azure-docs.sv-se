---
title: Använda med Kubernetes och Helm
titleSuffix: Azure Cognitive Services
description: Genom att använda Kubernetes och Helm för att definiera behållar avbildningarna tal-till-text och text till tal skapar vi ett Kubernetes-paket. Det här paketet kommer att distribueras till ett Kubernetes-kluster lokalt.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/16/2019
ms.author: dapine
ms.openlocfilehash: ba292a7d3bdf58ff78764bc2095fdf4a8c486070
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326211"
---
# <a name="use-with-kubernetes-and-helm"></a>Använda med Kubernetes och Helm

Ett alternativ för att hantera dina tal behållare lokalt är att använda Kubernetes och Helm. Genom att använda Kubernetes och Helm för att definiera behållar avbildningarna tal-till-text och text till tal skapar vi ett Kubernetes-paket. Det här paketet kommer att distribueras till ett Kubernetes-kluster lokalt. Slutligen ska vi utforska hur du testar de distribuerade tjänsterna och olika konfigurations alternativ. Mer information om att köra Docker-behållare utan Kubernetes-dirigering finns i [Installera och köra tal tjänst behållare](speech-container-howto.md).

## <a name="prerequisites"></a>Förutsättningar

Följande krav gäller innan du använder tal behållare lokalt:

|Krävs|Syfte|
|--|--|
| Azure-konto | Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto][free-azure-account] innan du börjar. |
| Container Registry åtkomst | För att Kubernetes ska kunna hämta Docker-avbildningarna till klustret behöver den åtkomst till behållar registret. Du är tvungen att [begära åtkomst till behållar registret][speech-preview-access] först. |
| Kubernetes CLI | [KUBERNETES CLI][kubernetes-cli] krävs för att hantera delade autentiseringsuppgifter från behållar registret. Kubernetes krävs också innan Helm, som är Kubernetes Package Manager. |
| Helm CLI | Som en del av [Helm CLI][helm-install] install, you'll also need to initialize Helm, which will install [Tiller][tiller-install]. |
|Tal resurs |Du måste ha följande för att kunna använda dessa behållare:<br><br>En _Azure-_ resurs för att hämta den associerade fakturerings nyckeln och URI för fakturerings slut punkt. Båda värdena är tillgängliga på Azure Portals **tal** översikt och nycklar sidor och krävs för att starta behållaren.<br><br>**{Api_key}** : resurs nyckel<br><br>**{ENDPOINT_URI}** : slut punkts-URI-exemplet är:`https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>Den rekommenderade värddator konfigurationen

Se information om [värddatorn för tal tjänst behållare][speech-container-host-computer] som en referens. Det här *Helm-diagrammet* BERÄKNAR automatiskt processor-och minnes krav baserat på hur många avkodningar (samtidiga begär Anden) som användaren anger. Dessutom justeras det baserat på om optimeringar för ljud-och text indata konfigureras `enabled`som. Helm-diagrammet använder som standard två samtidiga begär Anden och inaktiverar optimering.

| Tjänsten | PROCESSOR/container | Minne/container |
|--|--|--|
| **Tal till text** | en avkodare kräver minst 1 150 millicores. `optimizedForAudioFile` Om är aktive rad krävs 1 950 millicores. (standard: två avkodare) | Kunna 2 GB<br>Bara  4 GB |
| **Text till tal** | en samtidig begäran kräver minst 500 millicores. `optimizeForTurboMode` Om är aktive rad krävs 1 000 millicores. (standard: två samtidiga begär Anden) | Kunna 1 GB<br> Bara 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Ansluta till Kubernetes-klustret

Värddatorn förväntas ha ett tillgängligt Kubernetes-kluster. I den här självstudien om hur du [distribuerar ett Kubernetes-kluster](../../aks/tutorial-kubernetes-deploy-cluster.md) kan du få en uppfattning om hur du distribuerar ett Kubernetes-kluster till en värddator.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Dela Docker-autentiseringsuppgifter med Kubernetes-klustret

Om du vill tillåta Kubernetes- `docker pull` klustret till de konfigurerade avbildningarna `containerpreview.azurecr.io` från behållar registret måste du överföra Docker-autentiseringsuppgifterna till klustret. Kör kommandot nedan för att skapa en *Docker-register hemlighet* baserat på de autentiseringsuppgifter som tillhandahålls från behållar registrets åtkomst krav. [`kubectl create`][kubectl-create]

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

Verifiera att hemligheten har skapats genom att köra [`kubectl get`][kubectl-get] `secrets` med-flaggan.

```console
kuberctl get secrets
```

Att `kubectl get secrets` köra skriver ut alla konfigurerade hemligheter.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Konfigurera Helm för distribution

Besök [Microsoft Helm Hub][ms-helm-hub] för alla allmänt tillgängliga Helm-diagram som Microsoft erbjuder. I Microsoft Helm-hubben hittar du det **lokala diagrammet Cognitive Services tal**. **Cognitive Services talet lokalt** är det diagram vi kommer att installera, men vi måste först skapa en `config-values.yaml` fil med explicita konfigurationer. Vi börjar med att lägga till Microsoft-lagringsplatsen till vår Helm-instans.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Nu ska vi konfigurera våra Helm-diagram värden. Kopiera och klistra in följande YAML i en fil med `config-values.yaml`namnet. Mer information om hur Cognitive Services du anpassar det **lokala Helm-diagrammet för tal**finns i [Anpassa Helm-diagram](#customize-helm-charts). Ersätt värdena `apikey` och med dina egna. `billing`

```yaml
# These settings are deployment specific and users can provide customizations

# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-speech-to-text
    tag: latest
    pullSecrets:
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # < Your billing URL >
      apikey: # < Your API Key >

# text-to-speech configurations
textToSpeech:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForTurboMode: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-text-to-speech
    tag: latest
    pullSecrets:
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # < Your billing URL >
      apikey: # < Your API Key >
```

> [!IMPORTANT]
> Om värdena `apikey` och inte har angetts upphör tjänsterna att gälla efter 15 min. `billing` Det går inte heller att verifiera eftersom tjänsterna inte är tillgängliga.

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes-paketet (Helm-diagrammet)

*Helm-diagrammet* innehåller konfigurationen av vilka Docker-avbildningar som ska hämtas från `containerpreview.azurecr.io` behållar registret.

> Ett [Helm-diagram][helm-charts] är en samling filer som beskriver en relaterad uppsättning Kubernetes-resurser. Ett enkelt diagram kan användas för att distribuera något enkelt, t. ex. en memcached POD eller något komplext, t. ex. en fullständig webbapp med HTTP-servrar, databaser, cacheminnen och så vidare.

De tillhandahållna *Helm-diagrammen* hämtar Docker-avbildningarna av röst tjänsten, både text till tal-och tal-till-text-tjänster från `containerpreview.azurecr.io` behållar registret.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Installera Helm-diagrammet på Kubernetes-klustret

Om du vill installera *Helm-diagrammet* måste du köra [`helm install`][helm-install-cmd] `<config-values.yaml>` kommandot och ersätta med rätt sökväg och fil namns argument. Helm-diagrammet som refereras nedan är tillgängligt i [Microsoft Helm Hub här.][ms-helm-hub-speech-chart] `microsoft/cognitive-services-speech-onpremise`

```console
helm install microsoft/cognitive-services-speech-onpremise \
    --version 0.1.0 \
    --values <config-values.yaml> \
    --name onprem-speech
```

Här är ett exempel på utdata som du kan förväntas se från en lyckad installations körning:

```console
NAME:   onprem-speech
LAST DEPLOYED: Tue Jul  2 12:51:42 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                             READY  STATUS             RESTARTS  AGE
speech-to-text-7664f5f465-87w2d  0/1    Pending            0         0s
speech-to-text-7664f5f465-klbr8  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-4jtzh  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-frwxf  0/1    Pending            0         0s

==> v1/Service
NAME            TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
speech-to-text  LoadBalancer  10.0.252.106  <pending>    80:31811/TCP  1s
text-to-speech  LoadBalancer  10.0.125.187  <pending>    80:31247/TCP  0s

==> v1beta1/PodDisruptionBudget
NAME                                MIN AVAILABLE  MAX UNAVAILABLE  ALLOWED DISRUPTIONS  AGE
speech-to-text-poddisruptionbudget  N/A            20%              0                    1s
text-to-speech-poddisruptionbudget  N/A            20%              0                    1s

==> v1beta2/Deployment
NAME            READY  UP-TO-DATE  AVAILABLE  AGE
speech-to-text  0/2    2           0          0s
text-to-speech  0/2    2           0          0s

==> v2beta2/HorizontalPodAutoscaler
NAME                       REFERENCE                  TARGETS        MINPODS  MAXPODS  REPLICAS  AGE
speech-to-text-autoscaler  Deployment/speech-to-text  <unknown>/50%  2        10       0         0s
text-to-speech-autoscaler  Deployment/text-to-speech  <unknown>/50%  2        10       0         0s


NOTES:
cognitive-services-speech-onpremise has been installed!
Release is named onprem-speech
```

Kubernetes-distributionen kan ta flera minuter att slutföra. För att bekräfta att både poddar och tjänsterna är korrekt distribuerade och tillgängliga, kör du följande kommando:

```console
kubectl get all
```

Du bör förvänta dig att se något som liknar följande utdata:

```console
NAME                                  READY     STATUS    RESTARTS   AGE
pod/speech-to-text-7664f5f465-87w2d   1/1       Running   0          34m
pod/speech-to-text-7664f5f465-klbr8   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-4jtzh   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-frwxf   1/1       Running   0          34m

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
service/kubernetes       ClusterIP      10.0.0.1       <none>           443/TCP        3h
service/speech-to-text   LoadBalancer   10.0.252.106   52.162.123.151   80:31811/TCP   34m
service/text-to-speech   LoadBalancer   10.0.125.187   65.52.233.162    80:31247/TCP   34m

NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/speech-to-text   2         2         2            2           34m
deployment.apps/text-to-speech   2         2         2            2           34m

NAME                                        DESIRED   CURRENT   READY     AGE
replicaset.apps/speech-to-text-7664f5f465   2         2         2         34m
replicaset.apps/text-to-speech-56f8fb685b   2         2         2         34m

NAME                                                            REFERENCE                   TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/speech-to-text-autoscaler   Deployment/speech-to-text   1%/50%    2         10        2          34m
horizontalpodautoscaler.autoscaling/text-to-speech-autoscaler   Deployment/text-to-speech   0%/50%    2         10        2          34m
```

### <a name="verify-helm-deployment-with-helm-tests"></a>Verifiera Helm-distribution med Helm-tester

De installerade Helm-diagrammen definierar *Helm-tester*, vilket ger en bekvämlighet för verifiering. De här testerna verifierar tjänst beredskap. För att verifiera både **tal-till-text** -och **text till tal** -tjänster kör vi kommandot [Helm test][helm-test] .

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Dessa tester Miss känner om Pod status inte `Running` är eller om distributionen inte finns `AVAILABLE` med i kolumnen. Vara patient eftersom det kan ta mer än tio minuter att slutföra.

De här testerna kommer att resultera i olika status resultat:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

Som ett alternativ till att köra *Helm*-testerna kan du samla in de *externa IP-* adresserna och motsvarande `kubectl get all` portar från kommandot. Med hjälp av IP och port öppnar du en webbläsare och navigerar `http://<external-ip>:<port>:/swagger/index.html` till för att Visa API-Swagger sida (er).

## <a name="customize-helm-charts"></a>Anpassa Helm-diagram

Helm-diagram är hierarkiska. Om du är hierarkiskt tillåter det att diagrammet ärvs, och det omfattar även begreppet specificitet, där inställningar som är mer detaljerade åsidosätter ärvda regler.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

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
[helm-install-cmd]: https://helm.sh/docs/helm/#helm-install
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/developing_charts
[speech-preview-access]: https://aka.ms/speechcontainerspreview
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
