---
title: Lagra Helm-diagram
description: Lär dig hur du lagrar Helm-diagram för dina Kubernetes-program med databaser i Azure Container Registry
ms.topic: article
ms.date: 03/20/2020
ms.openlocfilehash: 6304486ac493e235ed74f26ab4be4f843ef52513
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131485"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Skicka och hämta Helm-diagram till ett Azure-behållarregister

Om du snabbt vill hantera och distribuera program för Kubernetes kan du använda [Helm-pakethanteraren med öppen källkod][helm]. Med Helm definieras programpaket som [diagram](https://helm.sh/docs/topics/charts/), som samlas in och lagras i en [Helm-diagramdatabas](https://helm.sh/docs/topics/chart_repository/).

Den här artikeln visar hur du är värd för Helm-diagramdatabaser i ett Azure-behållarregister med antingen en Helm 3- eller Helm 2-installation. I många fall skulle du skapa och ladda upp dina egna diagram för de program du utvecklar. Mer information om hur du skapar egna Helm-diagram finns i guiden för utvecklare av [diagrammallar][develop-helm-charts]. Du kan också lagra ett befintligt Helm-diagram från en annan Helm-repo.

> [!IMPORTANT]
> Stöd för Helm-diagram i Azure Container Registry är för närvarande i förhandsversion. Förhandsvisningar görs tillgängliga för dig på villkor att du godkänner de kompletterande [användarvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="helm-3-or-helm-2"></a>Helm 3 eller Helm 2?

Om du vill lagra, hantera och installera Helm-diagram använder du en Helm-klient och Helm CLI. Större utgåvor av Helm-klienten inkluderar Helm 3 och Helm 2. Helm 3 stöder ett nytt diagramformat och installerar inte längre Tiller-komponenten på Serversidan. Mer information om versionsskillnader finns i [vanliga frågor och svar om versionen](https://helm.sh/docs/faq/). Om du tidigare har distribuerat Helm 2-diagram läser du [Migrera Helm v2 till v3](https://helm.sh/docs/topics/v2_v3_migration/).

Du kan använda antingen Helm 3 eller Helm 2 som värd för Helm-diagram i Azure Container Registry, med arbetsflöden som är specifika för varje version:

* [Helm 3-klient](#use-the-helm-3-client) - använd `helm chart` kommandon i Helm CLI för att hantera diagram i registret som [OCI-artefakter](container-registry-image-formats.md#oci-artifacts)
* [Helm 2-klient](#use-the-helm-2-client) – använd [az acr helm-kommandon][az-acr-helm] i Azure CLI för att lägga till och hantera behållarregistret som en Helm-diagramdatabas

### <a name="additional-information"></a>Ytterligare information

* I de flesta scenarier rekommenderar vi att `helm chart` du använder Helm 3-arbetsflödet med inbyggda kommandon för att hantera diagram som OCI-artefakter.
* Du kan använda äldre [azure CLI-kommandon][az-acr-helm] och arbetsflöde med Helm 3-klienten och diagrammen. Vissa kommandon, till `az acr helm list` exempel, är dock inte kompatibla med Helm 3-diagram.
* Från och med Helm 3 stöds [az acr helm-kommandon][az-acr-helm] främst för kompatibilitet med Helm 2-klienten och diagramformatet. Framtida utveckling av dessa kommandon är för närvarande inte planerad.

## <a name="use-the-helm-3-client"></a>Använd Helm 3-klienten

### <a name="prerequisites"></a>Krav

- **Ett Azure-behållarregister** i din Azure-prenumeration. Om det behövs skapar du ett register med [Azure-portalen](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md).
- **Helm-klient version 3.1.0** `helm version` eller senare - Kör för att hitta din nuvarande version. Mer information om hur du installerar och uppgraderar Helm finns i [Installera Helm][helm-install].
- **Ett Kubernetes-kluster** där du installerar ett Helm-diagram. Om det behövs skapar du ett [Azure Kubernetes Service-kluster][aks-quickstart]. 
- **Azure CLI version 2.0.71** `az --version` eller senare - Kör för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

### <a name="high-level-workflow"></a>Arbetsflöde på hög nivå

Med **Helm 3** du:

* Kan skapa en eller flera Helm-databaser i ett Azure-behållarregister
* Lagra Helm 3-diagram i ett register som [OCI-artefakter](container-registry-image-formats.md#oci-artifacts). För närvarande är Helm 3 stöd för OCI *experimentell*.
* Autentisera med registret `helm registry login` med kommandot.
* Använd `helm chart` kommandon i Helm CLI för att driva, dra och hantera Helm-diagram i ett register
* Används `helm install` för att installera diagram i ett Kubernetes-kluster från en lokal databascache.

Mer om följande avsnitt finns i följande avsnitt.

### <a name="enable-oci-support"></a>Aktivera OCI-stöd

Ange följande miljövariabel för att aktivera OCI-stöd i Helm 3-klienten. För närvarande är detta stöd experimentellt. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="create-a-sample-chart"></a>Skapa ett exempeldiagram

Skapa ett testdiagram med följande kommandon:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Som ett grundläggande exempel, `templates` ändra katalogen till mappen och först ta bort innehållet där:

```console
rm -rf *
```

Skapa `templates` en fil som `configmap.yaml` anropas med följande innehåll i mappen:

```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
```

Mer information om hur du skapar och kör det här exemplet finns i [Komma igång](https://helm.sh/docs/chart_template_guide/getting_started/) i Helm Docs.

### <a name="save-chart-to-local-registry-cache"></a>Spara diagram i lokal registercache

Ändra katalogen `hello-world` till underkatalogen. Kör `helm chart save` sedan för att spara en kopia av diagrammet lokalt och även skapa ett alias med registrets fullständiga namn (alla gemener) och måldatabasen och taggen. 

I följande exempel är registernamnet *mycontainerregistry*, målrepo är *hello-world*och måldiagramtaggen är *v1*, men ersättningsvärden för din miljö:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Kör `helm chart list` för att bekräfta att du har sparat diagrammen i den lokala registercachen. Utdata liknar följande:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

### <a name="authenticate-with-the-registry"></a>Autentisera med registret

Kör `helm registry login` kommandot i Helm 3 CLI för att [autentisera med registret](container-registry-authentication.md) med autentiseringsuppgifter som är lämpliga för ditt scenario.

Skapa till exempel ett Azure Active [Directory-tjänsthuvudnamn med pull- och pushbehörigheter](container-registry-auth-service-principal.md#create-a-service-principal) (AcrPush-roll) till registret. Ange sedan tjänstens `helm registry login`huvudreferenser till . I följande exempel levereras lösenordet med hjälp av en miljövariabel:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

### <a name="push-chart-to-azure-container-registry"></a>Push-diagram till Azure Container Registry

Kör `helm chart push` kommandot i Helm 3 CLI för att skicka diagrammet till den fullständigt kvalificerade målarkivet:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Efter en lyckad push liknar utdata:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

### <a name="list-charts-in-the-repository"></a>Lista diagram i databasen

Precis som med avbildningar som lagras i ett Azure-behållarregister kan du använda [az acr-databaskommandon][az-acr-repository] för att visa databaserna som är värdar för dina diagram och diagramtaggar och manifest. 

Kör till exempel [az acr-databasvisning][az-acr-repository-show] för att se egenskaperna för den repo som du skapade i föregående steg:

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/hello-world
```

Utdata liknar följande:

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-03-20T18:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2020-03-20T18:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

Kör kommandot [az acr-databas visa manifest][az-acr-repository-show-manifests] för att se information om diagrammet som lagras i databasen. Ett exempel:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

Utdata, förkortat i det `configMediaType` `application/vnd.cncf.helm.config.v1+json`här exemplet, visar en av :

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-03-20T18:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2020-03-20T18:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "v1"
    ]
```

### <a name="pull-chart-to-local-cache"></a>Hämta diagram till lokal cache

Om du vill installera ett Helm-diagram på Kubernetes måste diagrammet finnas i den lokala cachen. I det här `helm chart remove` exemplet körs du först `mycontainerregistry.azurecr.io/helm/hello-world:v1`för att ta bort det befintliga lokala diagrammet med namnet:

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Kör `helm chart pull` för att hämta diagrammet från Azure-behållarregistret till din lokala cache:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

### <a name="export-helm-chart"></a>Exportera Helm-diagram

Om du vill arbeta vidare med diagrammet `helm chart export`exporterar du det till en lokal katalog med . Exportera diagrammet som du hämtade `install` till katalogen:

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Om du vill visa information för det exporterade diagrammet i osiningen kör du `helm show chart` kommandot i katalogen där du exporterade diagrammet.

```console
cd install
helm show chart hello-world
```

Helm returnerar detaljerad information om den senaste versionen av diagrammet, vilket visas i följande exempelutdata:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

### <a name="install-helm-chart"></a>Installera Helm-diagram

Kör `helm install` för att installera Helm-diagrammet som du hämtade till den lokala cachen och exporterade. Ange ett versionsnamn som *myhelmtest* `--generate-name` eller skicka parametern. Ett exempel:

```console
helm install myhelmtest ./hello-world
```

Utdata efter lyckad diagraminstallation liknar:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Om du vill verifiera `helm get manifest` installationen kör du kommandot. Kommandot returnerar YAML-data `configmap.yaml` i mallfilen.

Kör `helm uninstall` för att avinstallera diagramutgåvan i klustret:

```console
helm uninstall myhelmtest
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Ta bort ett Helm-diagram från databasen

Om du vill ta bort ett diagram från databasen använder du kommandot [az acr-databasborttagning.][az-acr-repository-delete] Kör följande kommando och bekräfta åtgärden när du uppmanas att göra det:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="use-the-helm-2-client"></a>Använd Helm 2-klienten

### <a name="prerequisites"></a>Krav

- **Ett Azure-behållarregister** i din Azure-prenumeration. Om det behövs skapar du ett register med [Azure-portalen](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md).
- **Helm-klient version 2.11.0 (inte en RC-version) eller senare** - Kör `helm version` för att hitta din nuvarande version. Du behöver också en Helm-server (Tiller) som initierats i ett Kubernetes-kluster. Om det behövs skapar du ett [Azure Kubernetes Service-kluster][aks-quickstart]. Mer information om hur du installerar och uppgraderar Helm finns i [Installera Helm][helm-install-v2].
- **Azure CLI version 2.0.46** `az --version` eller senare - Kör för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

### <a name="high-level-workflow"></a>Arbetsflöde på hög nivå

Med **Helm 2** du:

* Konfigurera ditt Azure-behållarregister som en *enda* Helm-diagramdatabas. Azure Container Registry hanterar indexdefinitionen när du lägger till och tar bort diagram till databasen.
* Autentisera med ditt Azure-behållarregister via Azure CLI, som sedan uppdaterar din Helm-klient automatiskt med register-URI och autentiseringsuppgifter. Du behöver inte ange den här registerinformationen manuellt, så autentiseringsuppgifterna visas inte i kommandohistoriken.
* Använd az [acr][az-acr-helm] helm-kommandona i Azure CLI för att lägga till ditt Azure-behållarregister som en Helm-diagramlagringsplats och för att skicka och hantera diagram. Dessa Azure CLI-kommandon kantaslut Helm 2-klientkommandon.
* Lägg till diagramdatabasen i ditt Azure-behållarregister i ditt lokala Helm-repoindex, med stöd för diagramsökning.
* Används `helm install` för att installera diagram i ett Kubernetes-kluster från en lokal databascache.

Mer om följande avsnitt finns i följande avsnitt.

### <a name="add-repository-to-helm-client"></a>Lägga till databas i Helm-klienten

Lägg till din Azure Container Registry Helm-diagramdatabas i Helm-klienten med kommandot [az acr helm repo add.][az-acr-helm-repo-add] Det här kommandot hämtar en autentiseringstoken för ditt Azure-behållarregister som används av Helm-klienten. Autentiseringstoken är giltig i 3 timmar. På `docker login`samma sätt som kan du köra det här kommandot i framtida CLI-sessioner för att autentisera Din Helm-klient med din Azure Container Registry Helm-diagramdatabas:

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-sample-chart-to-the-repository"></a>Lägga till ett exempeldiagram i databasen

Först skapa en lokal katalog på *~ / acr-helm*, sedan ladda ner den befintliga *stabil / wordpress* diagram:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

Skriv `ls` för att lista det nedladdade diagrammet och notera Wordpress-versionen som ingår i filnamnet. Kommandot `helm fetch stable/wordpress` angav inte en viss version, så den *senaste* versionen hämtades. I följande exempelutdata är Wordpress-diagrammet version *8.1.0:*

```output
wordpress-8.1.0.tgz
```

Skicka diagrammet till Helm-diagramdatabasen i Azure Container Registry med kommandot [az acr helm push][az-acr-helm-push] i Azure CLI. Ange namnet på ditt Helm-diagram som hämtats i föregående steg, till exempel *wordpress-8.1.0.tgz:*

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

Efter en stund rapporterar Azure CLI att diagrammet sparas, vilket visas i följande exempelutdata:

```output
{
  "saved": true
}
```

### <a name="list-charts-in-the-repository"></a>Lista diagram i databasen

Om du vill använda diagrammet som laddades upp i föregående steg måste det lokala Helm-databasindexet uppdateras. Du kan indexera om databaserna i Helm-klienten eller använda Azure CLI för att uppdatera databasindexet. Varje gång du lägger till ett diagram i databasen måste det här steget slutföras:

```azurecli
az acr helm repo add --name mycontainerregistry
```

Med ett diagram lagrat i databasen och det uppdaterade indexet lokalt kan du använda de vanliga Helm-klientkommandona för att söka eller installera. Om du vill visa alla diagram `helm search` i databasen använder du kommandot och tillhandahåller ditt eget Azure-behållarregisternamn:

```console
helm search mycontainerregistry
```

Wordpress-diagrammet som skjuts i föregående steg visas, vilket visas i följande exempelutdata:

```output
NAME                  CHART VERSION    APP VERSION    DESCRIPTION
helmdocs/wordpress    8.1.0           5.3.2          Web publishing platform for building blogs and websites.
```

Du kan också lista diagrammen med Azure CLI med az [acr helm-listan:][az-acr-helm-list]

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>Visa information för ett Helm-diagram

Om du vill visa information för ett visst `helm inspect` diagram i osiningen kan du använda kommandot.

```console
helm inspect mycontainerregistry/wordpress
```

När inget versionsnummer anges används den *senaste* versionen. Helm returnerar detaljerad information om diagrammet, som visas i följande komprimerade exempelutdata:

```output
apiVersion: v1
appVersion: 5.3.2
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: http://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: Bitnami
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 8.1.0
[...]
```

Du kan också visa informationen för ett diagram med kommandot Azure CLI [az acr helm show.][az-acr-helm-show] Återigen returneras den *senaste* versionen av ett diagram som standard. Du kan `--version` lägga till om du vill visa en viss version av ett diagram, till exempel *8.1.0:*

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>Installera ett Helm-diagram från databasen

Helm-diagrammet i databasen installeras genom att databasnamnet och diagramnamnet anges. Använd Helm-klienten för att installera Wordpress-diagrammet:

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> Om du skickar till din Azure Container Registry Helm-diagramdatabas och senare återvänder i en ny CLI-session behöver din lokala Helm-klient en uppdaterad autentiseringstoken. Om du vill hämta en ny autentiseringstoken använder du kommandot [az acr helm repo add.][az-acr-helm-repo-add]

Följande steg slutförs under installationsprocessen:

- Helm-klienten söker i det lokala databasindexet.
- Motsvarande diagram hämtas från Azure Container Registry-databasen.
- Diagrammet distribueras med hjälp av Tiller i kubernetes-klustret.

När installationen fortsätter följer du instruktionerna i kommandoutdata för att se WorPress-url:erna och autentiseringsuppgifterna. Du kan också `kubectl get pods` köra kommandot för att se Kubernetes-resurserna som distribueras via Helm-diagrammet:

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Ta bort ett Helm-diagram från databasen

Om du vill ta bort ett diagram från databasen använder du kommandot [az acr helm delete.][az-acr-helm-delete] Ange namnet på diagrammet, till exempel *wordpress*, och den version som ska tas bort, till exempel *8.1.0*.

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

Om du vill ta bort alla versioner av `--version` det namngivna diagrammet utelämnar du parametern.

Diagrammet fortsätter att returneras när `helm search`du kör . Helm-klienten uppdaterar inte automatiskt listan över tillgängliga diagram i en databas. Om du vill uppdatera Helm-klientrepoindexet använder du kommandot [az acr helm repo add][az-acr-helm-repo-add] igen:

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du skapar och distribuerar Helm-diagram finns i [Utveckla Helm-diagram][develop-helm-charts].
* Läs mer om hur du installerar program med Helm i [Azure Kubernetes Service (AKS)](../aks/kubernetes-helm.md).
* Helm-diagram kan användas som en del av processen för att bygga behållare. Mer information finns i [Använda Azure Container Registry Tasks][acr-tasks].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-v2]: https://v2.helm.sh/docs/using_helm/#installing-helm
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
