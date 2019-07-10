---
title: Använda Kubernetes on-premises
titleSuffix: Azure Cognitive Services
description: Använda Kubernetes (K8s) och Helm för att definiera tal till text och text till tal behållaravbildningarna kan skapa vi ett Kubernetes-paket. Det här paketet ska distribueras till ett Kubernetes-kluster på plats.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: dapine
ms.openlocfilehash: 1e3afc80abad5f5c1f9b4d57c52ca75449eeb755
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711487"
---
# <a name="use-kubernetes-on-premises"></a>Använda Kubernetes on-premises

Använda Kubernetes (K8s) och Helm för att definiera tal till text och text till tal behållaravbildningarna kan skapa vi ett Kubernetes-paket. Det här paketet ska distribueras till ett Kubernetes-kluster på plats. Slutligen kan vi testa de distribuerade tjänsterna och olika konfigurationsalternativ.

## <a name="prerequisites"></a>Förutsättningar

Den här proceduren kräver flera verktyg som måste installeras och köras lokalt.

* Använda en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto][free-azure-account] innan du börjar.
* Installera den [Azure CLI][azure-cli] (az).
* Installera den [Kubernetes CLI][kubernetes-cli] (kubectl).
* Installera den [Helm][helm-install] klient, Kubernetes pakethanterare.
    * Installera Helm-server [Tiller][tiller-install].
* En Azure-resurs med rätt prisnivån. Inte alla prisnivåer arbetar du med dessa avbildningar:
    * **Tal** resurs med F0 eller standardpriserna nivåerna endast.
    * **Cognitive Services** resurs med S0 prisnivå.

## <a name="the-recommended-host-computer-configuration"></a>Datorkonfiguration rekommenderade värden

Referera till den [Speech Service behållare värddatorn][speech-container-host-computer] information som referens. Detta *helm-diagrammet* beräknar automatiskt processor och minne krav utifrån hur många avkodar (samtidiga begäranden) som användaren anger. Dessutom kan anpassas beroende på om optimeringar för ljud/textinmatning är konfigurerade som `enabled`. Helm-diagram standardvärdena till två samtidiga begäranden och inaktiverar optimering.

| Tjänst | CPU / behållare | Minne / behållare |
|--|--|--|
| **Speech-to-Text** | en avkodaren kräver minst 1,150 millicores. Om den `optimizedForAudioFile` är aktiverat, då 1,950 millicores krävs. (standard: två avkodare) | Krävs: 2 GB<br>Begränsad:  4 GB |
| **Text till tal** | en samtidig begäran kräver minst 500 millicores. Om den `optimizeForTurboMode` är aktiverat, då 1 000 millicores krävs. (standard: två samtidiga begäranden) | Krävs: 1 GB<br> Begränsad: 2 GB |

## <a name="request-access-to-the-container-registry"></a>Begär åtkomst till behållarregistret

Skicka den [Cognitive Services tal behållare formulär][speech-preview-access] att begära åtkomst till behållaren. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Ansluta till Kubernetes-kluster

Värddatorn förväntas ha ett tillgängliga Kubernetes-kluster. Se den här självstudiekursen på [distribuerar ett Kubernetes-kluster](../../aks/tutorial-kubernetes-deploy-cluster.md) en konceptuell förståelse för hur du distribuerar ett Kubernetes-kluster till en värddator.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Dela autentiseringsuppgifter för Docker med Kubernetes-kluster

Att tillåta Kubernetes-klustret till `docker pull` konfigurerade avbildningar från den `containerpreview.azurecr.io` behållarregistret måste du överföra docker-autentiseringsuppgifter i klustret. Kör den [ `kubectl create` ][kubectl-create] kommandot nedan för att skapa en *docker-registry hemlighet* baserat på autentiseringsuppgifterna från behållaren [registeråtkomst](#request-access-to-the-container-registry) avsnittet.

Från ditt kommandoradsgränssnitt föredrar, kör du följande kommando. Se till att ersätta den `<username>`, `<password>`, och `<email-address>` med autentiseringsuppgifter för container-registret.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Om du redan har åtkomst till den `containerpreview.azurecr.io` behållarregistret som du kan skapa en Kubernetes-hemlighet i stället använda den allmänna flaggan. Överväg följande kommando som körs mot din Docker-konfiguration JSON.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

Följande utdata skrivs ut på konsolen när hemligheten har skapats.

```console
secret "containerpreview" created
```

Kontrollera att hemligheten har skapats genom att köra den [ `kubectl get` ][kubectl-get] med den `secrets` flaggan.

```console
kuberctl get secrets
```

Kör den `kubectl get secrets` skriver ut alla konfigurerade hemligheter.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Konfigurera värden i Helm-diagram för distribution

Gå till den [Microsoft Helm Hub][ms-helm-hub] för alla allmänt tillgängliga helm-diagram som erbjuds av Microsoft. Från Microsoft Helm hubben, hittar du den **Cognitive Services tal lokala diagram över**. Den **Cognitive Services tal lokalt** är ett diagram som installerar vi, men vi måste först skapa en `config-values.yaml` fil med explicita konfigurationer. Låt oss börja med att lägga till Microsoft-databasen till vår Helm-instans.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Nu ska konfigurerar vi vår värden i Helm-diagram. Kopiera och klistra in följande YAML i en fil med namnet `config-values.yaml`. Mer information om hur du anpassar den **Cognitive Services tal lokala Helm-diagrammet**, se [anpassa helm-diagram](#customize-helm-charts). Ersätt den `billing` och `apikey` värdena med dina egna.

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
> Om den `billing` och `apikey` värden inte anges, tjänsterna går ut efter 15 minuter. På samma sätt kan misslyckas verifieringen som tjänsterna inte är tillgänglig.

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes-paket (Helm-diagram)

Den *Helm-diagrammet* innehåller konfiguration som docker-avbildningar för att hämta från den `containerpreview.azurecr.io` behållarregister.

> En [Helm-diagrammet][helm-charts] är en uppsättning filer som beskriver en uppsättning Kubernetes-resurser. Ett enda diagram kan användas för att distribuera något enkelt, som en memcached pod eller ett komplext, som en fullständig web app-stack med HTTP-servrar, databaser, cacheminnen och så vidare.

Den angivna *Helm diagram* hämta docker-avbildningar av Speech-tjänsten, både text och tal till text-tjänster från den `containerpreview.azurecr.io` behållarregister.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Installera Helm-diagrammet på Kubernetes-kluster

Installera den *helm-diagrammet* måste du köra den [ `helm install` ][helm-install-cmd] kommando, ersätter den `<config-values.yaml>` med rätt sökväg och filnamn namnargumentet. Den `microsoft/cognitive-services-speech-onpremise` Helm-diagrammet som anges nedan, är tillgänglig på den [Microsoft Helm här Hub][ms-helm-hub-speech-chart].

```console
helm install microsoft/cognitive-services-speech-onpremise \
    --version 0.1.0 \
    --values <config-values.yaml> \
    --name onprem-speech
```

Här är ett exempel på utdata du kan förvänta dig att se från körning av en lyckad installation:

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

Kubernetes-distribution kan ta över flera minuter att slutföra. Kontrollera att både poddar och tjänster är korrekt distribuerats och är tillgänglig genom att köra följande kommando:

```console
kubectl get all
```

Du kan förvänta att se något som liknar följande utdata:

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

### <a name="verify-helm-deployment-with-helm-tests"></a>Kontrollera Helm-distributionen med Helm-test

De installerade Helm-diagram definiera *Helm tester*, som används som för att underlätta för verifiering. De här testerna verifiera beredskap för tjänsten. Kontrollera att båda **tal till text** och **text till tal** tjänster, som vi ska köra den [Helm test][helm-test] kommando.

```console
helm test onprem-speech
```

> [!IMPORTANT]
> De här testerna misslyckas om POD-status inte `Running` eller om distributionen inte visas den `AVAILABLE` kolumn. Ha tålamod eftersom det kan ta över tio minuter för att slutföra.

De här testerna kommer utdataresultat olika status:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

Som ett alternativ till att köra den *helm tester*, du kan samla in den *externa IP-Adressen* adresser och motsvarande portar från den `kubectl get all` kommando. Med hjälp av IP-adress och port, öppna en webbläsare och gå till `http://<external-ip>:<port>:/swagger/index.html` till vyn API swagger-sidor.

## <a name="customize-helm-charts"></a>Anpassa Helm-diagram

Helm-diagram är hierarkiska. Möjliggör vara hierarkiska diagrammet arv det också caters begreppet dessutom, där inställningar som är mer specifika åsidosätter ärvda regler.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om hur du installerar program med Helm i Azure Kubernetes Service (AKS) [här][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Cognitive Services-behållare][cog-svcs-containers]

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
