---
title: Använd talservicecontainrar med Kubernetes och Helm
titleSuffix: Azure Cognitive Services
description: Genom att använda Kubernetes och Helm för att definiera behållaravbildningar mellan tal och text och tal skapar vi ett Kubernetes-paket. Det här paketet kommer att distribueras till ett Kubernetes-kluster lokalt.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
ms.openlocfilehash: 6ad5a843c8cc287834305e09b48cd3fafe09ca51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474772"
---
# <a name="use-speech-service-containers-with-kubernetes-and-helm"></a>Använd talservicecontainrar med Kubernetes och Helm

Ett alternativ för att hantera dina talbehållare lokalt är att använda Kubernetes och Helm. Genom att använda Kubernetes och Helm för att definiera behållaravbildningar mellan tal och text och tal skapar vi ett Kubernetes-paket. Det här paketet kommer att distribueras till ett Kubernetes-kluster lokalt. Slutligen ska vi undersöka hur du testar de distribuerade tjänsterna och olika konfigurationsalternativ. Mer information om hur du kör Docker-behållare utan Kubernetes orkestrering finns i [installera och köra taltjänstbehållare](speech-container-howto.md).

## <a name="prerequisites"></a>Krav

Följande förutsättningar innan du använder Talbehållare lokalt:

| Krävs | Syfte |
|----------|---------|
| Azure-konto | Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt][free-azure-account] konto innan du börjar. |
| Åtkomst till behållarregister | För att Kubernetes ska kunna dra in dockeravbildningarna i klustret måste den åtkomst till behållarregistret. |
| Kubernetes CLI | [Kubernetes CLI][kubernetes-cli] krävs för att hantera delade autentiseringsuppgifter från behållarregistret. Kubernetes behövs också före Helm, som är Kubernetes pakethanterare. |
| Helm CLI | Installera [Helm CLI][helm-install], som används för att installera ett helm-diagram (containerpaketdefinition). |
|Talresurs |För att kunna använda dessa behållare måste du ha:<br><br>En _Speech_ Azure-resurs för att hämta den associerade faktureringsnyckeln och faktureringsslutpunkten URI. Båda värdena är tillgängliga på Azure-portalens **talöversikt** och nycklar och krävs för att starta behållaren.<br><br>**{API_KEY}**: resursnyckel<br><br>**{ENDPOINT_URI}**: uri-exempel för slutpunkt är:`https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>Den rekommenderade värddatorkonfigurationen

Se [värddatorinformation för taltjänstens behållare][speech-container-host-computer] som referens. Det här *rrodret beräknar* automatiskt CPU- och minneskrav baserat på hur många avkodningar (samtidiga begäranden) som användaren anger. Dessutom justeras den baserat på om optimeringar för ljud-/textinmatning har konfigurerats som `enabled`. Rodret diagrammet standard, två samtidiga begäranden och inaktivera optimering.

| Tjänst | CPU / Behållare | Minne / Behållare |
|--|--|--|
| **Tal-till-text** | en dekoder kräver minst 1 150 millicore. Om `optimizedForAudioFile` den är aktiverad krävs 1 950 millicore. (standard: två avkodare) | Obligatoriskt: 2 GB<br>Begränsad: 4 GB |
| **Text-till-tal** | en samtidig begäran kräver minst 500 millicore. Om `optimizeForTurboMode` den är aktiverad krävs 1 000 millicore. (standard: två samtidiga begäranden) | Obligatoriskt: 1 GB<br> Begränsad: 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Ansluta till Kubernetes-klustret

Värddatorn förväntas ha ett tillgängligt Kubernetes-kluster. Se den här självstudien om [hur du distribuerar ett Kubernetes-kluster](../../aks/tutorial-kubernetes-deploy-cluster.md) för en begreppsmässig förståelse av hur du distribuerar ett Kubernetes-kluster till en värddator.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Dela Docker-autentiseringsuppgifter med Kubernetes-klustret

Om du vill tillåta Kubernetes-klustret `docker pull` till `containerpreview.azurecr.io` de konfigurerade avbildningarna från behållarregistret måste du överföra dockerautentiseringsuppgifterna till klustret. Kör [`kubectl create`][kubectl-create] kommandot nedan för att skapa en *dockerregisterhemlighet* baserat på de autentiseringsuppgifter som tillhandahålls från förutsättningen för behållarregisteråtkomst.

Kör följande kommandogränssnitt från kommandoradsgränssnittet. Var noga med `<username>` `<password>`att `<email-address>` ersätta autentiseringsuppgifterna för behållarens register.

```console
kubectl create secret docker-registry mcr \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Om du redan har `containerpreview.azurecr.io` åtkomst till behållarregistret kan du skapa en Kubernetes-hemlighet med den allmänna flaggan i stället. Tänk på följande kommando som körs mot din Docker-konfiguration JSON.
> ```console
>  kubectl create secret generic mcr \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

Följande utdata skrivs ut till konsolen när hemligheten har skapats.

```console
secret "mcr" created
```

Om du vill kontrollera att hemligheten `secrets` har skapats kör du [`kubectl get`][kubectl-get] flaggan med flaggan.

```console
kubectl get secrets
```

Kör utskrifterna `kubectl get secrets` alla konfigurerade hemligheter.

```console
NAME    TYPE                              DATA    AGE
mcr     kubernetes.io/dockerconfigjson    1       30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Konfigurera Helm-diagramvärden för distribution

Besök [Microsoft Helm Hub][ms-helm-hub] för alla offentligt tillgängliga helm-diagram som erbjuds av Microsoft. Från Microsoft Helm Hub hittar du **det lokala schemat för cognitive services-tal**. **Cognitive Services Speech On-Premises** är det diagram vi ska installera, men vi måste först skapa en `config-values.yaml` fil med explicita konfigurationer. Låt oss börja med att lägga till Microsoft-databasen i vår Helm-instans.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Därefter konfigurerar vi våra Helm-diagramvärden. Kopiera och klistra in följande YAML i en fil med namnet `config-values.yaml`. Mer information om hur du anpassar **Cognitive Services Speech On-Premises Helm Chart**finns i Anpassa [helm-diagram](#customize-helm-charts). Ersätt `# {ENDPOINT_URI}` kommentarerna `# {API_KEY}` och kommentarerna med dina egna värderingar.

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
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}

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
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Om `billing` värdena och `apikey` inte tillhandahålls upphör tjänsterna att gälla efter 15 min. På samma sätt kommer verifieringen att misslyckas eftersom tjänsterna inte kommer att vara tillgängliga.

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes-paketet (Helm-sjökortet)

*Helm-diagrammet* innehåller konfigurationen för vilken dockeravbildning(er) som ska hämtas från `containerpreview.azurecr.io` behållarregistret.

> Ett [Helm-diagram][helm-charts] är en samling filer som beskriver en relaterad uppsättning Kubernetes-resurser. Ett enda diagram kan användas för att distribuera något enkelt, till exempel en memcached pod, eller något komplext, som en fullständig webbappstack med HTTP-servrar, databaser, cacheminnen och så vidare.

De angivna *Helm-diagrammen* hämtar dockeravbildningarna för taltjänsten, både text-till-tal- och tal-till-text-tjänsterna från `containerpreview.azurecr.io` behållarregistret.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Installera Helm-diagrammet i Kubernetes-klustret

Om du vill installera *rodret* måste [`helm install`][helm-install-cmd] vi köra `<config-values.yaml>` kommandot och ersätta argumentet med rätt sökväg och filnamn. Helm-diagrammet som refereras nedan finns på [Microsoft Helm Hub här][ms-helm-hub-speech-chart]. `microsoft/cognitive-services-speech-onpremise`

```console
helm install onprem-speech microsoft/cognitive-services-speech-onpremise \
    --version 0.1.1 \
    --values <config-values.yaml> 
```

Här är ett exempel på utdata som du kan förvänta dig att se från en lyckad installation körning:

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

Kubernetes-distributionen kan ta över flera minuter att slutföra. Om du vill bekräfta att både poddar och tjänster har distribuerats och är tillgängliga på rätt sätt kör du följande kommando:

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

De installerade Helm-diagrammen definierar *Helm-tester*, som fungerar som en bekvämlighet för verifiering. Dessa tester validerar tjänstens beredskap. Om du vill verifiera både **tal-till-text-** och **text-till-tal-tjänster** kör vi [Helm-testkommandot.][helm-test]

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Dessa tester misslyckas om POD-status inte `Running` är eller om `AVAILABLE` distributionen inte visas under kolumnen. Ha tålamod eftersom detta kan ta över tio minuter att slutföra.

Dessa tester kommer att ge ut olika statusresultat:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

Som ett alternativ till att köra *helm-testerna*kan du samla `kubectl get all` in *externa IP-adresser* och motsvarande portar från kommandot. Öppna en webbläsare med IP- och `http://<external-ip>:<port>:/swagger/index.html` portporten och navigera till api-swaggersidan.Using the IP and port, open a web browser and navigate to to view the API swagger page(s).

## <a name="customize-helm-charts"></a>Anpassa Helm-diagram

Helm-diagram är hierarkiska. Att vara hierarkisk möjliggör diagram arv, det vänder sig också till begreppet specificitet, där inställningar som är mer specifika åsidosätta ärvda regler.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

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
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://v2.helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
