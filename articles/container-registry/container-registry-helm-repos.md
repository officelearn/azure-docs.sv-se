---
title: Lagra Helm-diagram
description: Lär dig hur du lagrar Helm-diagram för dina Kubernetes-program med hjälp av databaser i Azure Container Registry
ms.topic: article
ms.date: 01/28/2020
ms.openlocfilehash: 7969efe37558fffb26b983131c56ae11f3ef9368
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78398966"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Push-och pull-Helm diagram till ett Azure Container Registry

Om du snabbt vill hantera och distribuera program för Kubernetes kan du använda [paket hanteraren med öppen källkod för Helm][helm]. Med Helm definieras programpaket som [diagram](https://helm.sh/docs/topics/charts/), som samlas in och lagras i en Helm- [diagram lagrings plats](https://helm.sh/docs/topics/chart_repository/).

Den här artikeln visar hur du kan vara värd för Helm-diagram i databaser i ett Azure Container Registry med hjälp av antingen en Helm 3-eller Helm 2-installation. I det här exemplet lagrar du ett befintligt Helm-diagram från den offentliga Helm- *stabila* lagrings platsen. I många fall skulle du skapa och ladda upp dina egna diagram för de program som du utvecklar. Mer information om hur du skapar egna Helm-diagram finns i [Developer-Guide för diagram mal len][develop-helm-charts].

> [!IMPORTANT]
> Stöd för Helm-diagram i Azure Container Registry är för närvarande en för hands version. För hands versionerna görs tillgängliga för dig på villkor att du godkänner kompletterande [användnings villkor][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="helm-3-or-helm-2"></a>Helm 3 eller Helm 2?

Om du vill lagra, hantera och installera Helm-diagram använder du en Helm-klient och Helm CLI. Större versioner av Helm-klienten är Helm 3 och Helm 2. Helm 3 har stöd för ett nytt diagram format och installerar inte längre komponenten på Server sidan. Mer information om versions skillnaderna finns i [vanliga frågor och svar](https://helm.sh/docs/faq/)om versioner. Om du tidigare har distribuerat Helm 2-diagram, se [migrera Helm v2 till v3](https://helm.sh/docs/topics/v2_v3_migration/).

Du kan använda antingen Helm 3 eller Helm 2 för att vara värd för Helm-diagram i Azure Container Registry, med arbets flöden som är speciella för varje version:

* [Helm 3-klient](#use-the-helm-3-client) -Använd `helm chart`-kommandon för att hantera diagram i registret som [OCI-artefakter](container-registry-image-formats.md#oci-artifacts)
* [Helm 2-klient](#use-the-helm-2-client) -Använd [AZ ACR Helm][az-acr-helm] -kommandon i Azure CLI för att lägga till och hantera behållar registret som en Helm diagram lagrings plats

### <a name="additional-information"></a>Ytterligare information

* Vi rekommenderar att du använder arbets flödet Helm 3 med inbyggda `helm chart` kommandon för att hantera diagram som OCI-artefakter.
* Du kan använda äldre [AZ-ACR Helm][az-acr-helm] Azure CLI-kommandon och arbets flöde med Helm 3-klienten och-diagrammen. Vissa kommandon som `az acr helm list` är dock inte kompatibla med Helm 3-diagram.
* Från och med Helm 3 stöds [AZ ACR Helm][az-acr-helm] -kommandon huvudsakligen för kompatibilitet med Helm 2-klienten och diagram formatet. Framtida utveckling av dessa kommandon är för närvarande inte planerat.

## <a name="use-the-helm-3-client"></a>Använda Helm 3-klienten

### <a name="prerequisites"></a>Förutsättningar

- **Ett Azure Container Registry** i din Azure-prenumeration. Om det behövs skapar du ett register med hjälp av [Azure Portal](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md).
- **Helm klient version 3.0.0 eller senare** – kör `helm version` för att hitta din aktuella version. Mer information om hur du installerar och uppgraderar Helm finns i [Installera Helm][helm-install].
- **Ett Kubernetes-kluster** där du ska installera ett Helm-diagram. Om det behövs skapar du ett [Azure Kubernetes service-kluster][aks-quickstart]. 
- **Azure CLI version 2.0.71 eller senare** – kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

### <a name="high-level-workflow"></a>Arbets flöde på hög nivå

Med **Helm 3** :

* Kan skapa en eller flera Helm-databaser i ett Azure Container Registry
* Lagra Helm 3-diagram i ett register som [OCI-artefakter](container-registry-image-formats.md#oci-artifacts). För närvarande betraktas Helm 3-stöd för OCI som *experimentellt*.
* Använd `helm chart`-kommandon direkt från Helm CLI för att skicka, hämta och hantera Helm-diagram i ett register
* Autentisera med ditt register via Azure CLI, som sedan uppdaterar Helm-klienten automatiskt med registrerings-URI och autentiseringsuppgifter. Du behöver inte ange register informationen manuellt, så autentiseringsuppgifterna visas inte i kommando historiken.
* Använd `helm install` för att installera diagram till ett Kubernetes-kluster från en lokal cache för lagring.

Se följande avsnitt för exempel.

### <a name="enable-oci-support"></a>Aktivera OCI-stöd

Ange följande miljö variabel för att aktivera OCI-stöd i Helm 3-klienten. Den här supporten är för närvarande experimentell. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="pull-an-existing-helm-package"></a>Hämta ett befintligt Helm-paket

Om du inte redan har lagt till `stable` Helm Chart lagrings platsen kör du kommandot `helm repo add`:

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com
```

Hämta ett diagram paket från `stable` lagrings platsen lokalt. Du kan till exempel skapa en lokal katalog som *~/ACR-Helm*och sedan ladda ned det befintliga *stabila/WordPress-* diagrammet. (Det här exemplet och andra kommandon i den här artikeln är formaterade för bash-gränssnittet.)

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm pull stable/wordpress --untar
```

`helm pull stable/wordpress` kommandot angav inte någon särskild version, så den *senaste* versionen hämtades och expanderades i `wordpress`-underkatalogen.

### <a name="save-chart-to-local-registry-cache"></a>Spara diagrammet i det lokala registrets cacheminne

Ändra katalogen till `wordpress`-underkatalogen, som innehåller Helm. Kör sedan `helm chart save` för att spara en kopia av diagrammet lokalt och skapa ett alias med det fullständigt kvalificerade namnet på registret och mål databasen och taggen. 

I följande exempel är register namnet *mycontainerregistry*, mål lagrings platsen är *WordPress*och mål diagram tag gen är den *senaste*, men ersätter värden för din miljö:

```console
cd wordpress
helm chart save . wordpress:latest
helm chart save . mycontainerregistry.azurecr.io/helm/wordpress:latest
```

Kör `helm chart list` för att bekräfta att du har sparat diagrammen i det lokala cacheminnet för registret. Utdata liknar följande:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
wordpress:latest                                         wordpress       8.1.0   5899db0 29.1 KiB        1 day 
mycontainerregistry.azurecr.io/helm/wordpress:latest     wordpress       8.1.0   5899db0 29.1 KiB        1 day 
```

### <a name="push-chart-to-azure-container-registry"></a>Push-diagram till Azure Container Registry

Kör kommandot `helm chart push` i Helm 3 CLI för att skicka Helm-diagrammet till en lagrings plats i Azure Container Registry. Om den inte finns skapas databasen.

Använd först Azure CLI-kommandot [AZ ACR login][az-acr-login] för att autentisera till ditt register:

```azurecli
az acr login --name mycontainerregistry
```

Skicka diagrammet till det fullständigt kvalificerade mål lagrings platsen:

```console
helm chart push mycontainerregistry.azurecr.io/helm/wordpress:latest
```

Efter en lyckad push liknar utdata följande:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/wordpress]
ref:     mycontainerregistry.azurecr.io/helm/wordpress:latest
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    29.1 KiB
name:    wordpress
version: 8.1.0
```

### <a name="list-charts-in-the-repository"></a>Visa lista över diagram i databasen

Precis som med bilder som lagras i ett Azure Container Registry kan du använda [AZ ACR-lagringsplatsen][az-acr-repository] för att visa de databaser som är värdar för dina diagram och diagram etiketter och-manifest. 

Du kan till exempel köra [AZ ACR-lagringsplatsen][az-acr-repository-show] för att se egenskaperna för lagrings platsen som du skapade i föregående steg:

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/wordpress
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
  "createdTime": "2020-01-29T16:54:30.1514833Z",
  "imageName": "helm/wordpress",
  "lastUpdateTime": "2020-01-29T16:54:30.4992247Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

Kör kommandot [AZ ACR-lagringsplatsen show-Manifests][az-acr-repository-show-manifests] för att se information om diagrammet som lagras i lagrings platsen. Exempel:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/wordpress --detail
```

Utdata, förkortat i det här exemplet, visar en `configMediaType` av `application/vnd.cncf.helm.config.v1+json`:

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-01-29T16:54:30.2382436Z",
    "digest": "sha256:xxxxxxxx51bc0807bfa97cb647e493ac381b96c1f18749b7388c24bbxxxxxxxxx",
    "imageSize": 29995,
    "lastUpdateTime": "2020-01-29T16:54:30.3492436Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "latest"
    ]
  }
]
```

### <a name="pull-chart-to-local-cache"></a>Hämta diagram till lokalt cacheminne

Om du vill installera ett Helm-diagram på Kubernetes måste diagrammet vara i det lokala cacheminnet. I det här exemplet ska du först köra `helm chart remove` för att ta bort det befintliga lokala diagrammet med namnet `mycontainerregistry.azurecr.io/helm/wordpress:latest`:

```console
helm chart remove mycontainerregistry.azurecr.io/helm/wordpress:latest
```

Kör `helm chart pull` för att ladda ned diagrammet från Azure Container Registry till ditt lokala cacheminne:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/wordpress:latest
```

### <a name="export-helm-chart"></a>Exportera Helm-diagram

Om du vill arbeta mer med diagrammet exporterar du det till en lokal katalog med hjälp av `helm chart export`. Exportera till exempel diagrammet som du hämtade till `install`-katalogen:

```console
helm chart export mycontainerregistry.azurecr.io/helm/wordpress:latest --destination ./install
```

Om du vill visa information om det exporterade diagrammet i lagrings platsen kör du kommandot `helm inspect chart` i den katalog där du exporterade diagrammet.

```console
cd install
helm inspect chart wordpress
```

När inget versions nummer anges används den *senaste* versionen. Helm returnerar detaljerad information om diagrammet, som du ser i följande komprimerade utdata:

```output
apiVersion: v1
appVersion: 5.3.2
dependencies:
- condition: mariadb.enabled
  name: mariadb
  repository: https://kubernetes-charts.storage.googleapis.com/
  tags:
  - wordpress-database
  version: 7.x.x
description: Web publishing platform for building blogs and websites.
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
```

### <a name="install-helm-chart"></a>Installera Helm-diagram

Kör `helm install` för att installera Helm-diagrammet som du hämtade till den lokala cachen och exporterade. Ange ett versions namn eller skicka parametern `--generate-name`. Exempel:

```console
helm install wordpress --generate-name
```

När installationen fortsätter följer du anvisningarna i kommandots utdata för att se WorPress-URL: er och autentiseringsuppgifter. Du kan också köra kommandot `kubectl get pods` för att se de Kubernetes-resurser som distribueras via Helm-diagrammet:

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Ta bort ett Helm-diagram från lagrings platsen

Om du vill ta bort ett diagram från databasen använder du kommandot [AZ ACR databas Delete][az-acr-repository-delete] . Kör följande kommando och bekräfta åtgärden när du uppmanas till det:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/wordpress:latest
```

## <a name="use-the-helm-2-client"></a>Använda Helm 2-klienten

### <a name="prerequisites"></a>Förutsättningar

- **Ett Azure Container Registry** i din Azure-prenumeration. Om det behövs skapar du ett register med hjälp av [Azure Portal](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md).
- **Helm klient version 2.11.0 (inte en RC-version) eller senare** – kör `helm version` för att hitta din aktuella version. Du behöver också en Helm-Server (till gång) som initierats i ett Kubernetes-kluster. Om det behövs skapar du ett [Azure Kubernetes service-kluster][aks-quickstart]. Mer information om hur du installerar och uppgraderar Helm finns i [Installera Helm][helm-install-v2].
- **Azure CLI version 2.0.46 eller senare** – kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

### <a name="high-level-workflow"></a>Arbets flöde på hög nivå

Med **Helm 2** :

* Konfigurera ditt Azure Container Registry som en *enda* Helm-diagram lagrings plats. Azure Container Registry hanterar index definitionen när du lägger till och tar bort diagram i lagrings platsen.
* Använd kommandona [AZ ACR Helm][az-acr-helm] i Azure CLI för att lägga till ditt Azure Container Registry som en Helm-lagringsplats och för att skicka och hantera diagram. Dessa Azure CLI-kommandon radbryter Helm 2-klient kommandon.
* Lägg till diagrammets lagrings plats i ditt Azure Container Registry till ditt lokala Helm lagrings platsen-index, stöd för diagram sökning
* Autentisera med Azure Container Registry via Azure CLI, som sedan uppdaterar Helm-klienten automatiskt med registrerings-URI och autentiseringsuppgifter. Du behöver inte ange register informationen manuellt, så autentiseringsuppgifterna visas inte i kommando historiken.
* Använd `helm install` för att installera diagram till ett Kubernetes-kluster från en lokal cache för lagring.

Se följande avsnitt för exempel.

### <a name="add-repository-to-helm-client"></a>Lägg till lagrings plats i Helm-klienten

Lägg till din Azure Container Registry Helm Chart-lagringsplats till Helm-klienten med kommandot [AZ ACR Helm lagrings platsen Add][az-acr-helm-repo-add] . Det här kommandot hämtar en autentiseringstoken för ditt Azure Container Registry som används av Helm-klienten. Autentiseringstoken är giltig i 3 timmar. Precis som med `docker login`kan du köra det här kommandot i framtida CLI-sessioner för att autentisera din Helm-klient med ditt Azure Container Registry Helm-diagram lager:

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-chart-to-the-repository"></a>Lägg till ett diagram i lagrings platsen

Skapa först en lokal katalog på *~/ACR-Helm*och hämta sedan det befintliga *stabila/WordPress-* diagrammet:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

Skriv `ls` för att lista det hämtade diagrammet och anteckna WordPress-versionen som ingår i fil namnet. Det gick inte att ange en viss version av `helm fetch stable/wordpress` kommandot, så den *senaste* versionen hämtades. I följande exempel utdata är WordPress-diagrammet version *8.1.0*:

```output
wordpress-8.1.0.tgz
```

Skicka diagrammet till Helm-diagrammets lagrings plats i Azure Container Registry med kommandot [AZ ACR Helm push][az-acr-helm-push] i Azure CLI. Ange namnet på ditt Helm-diagram som hämtades i föregående steg, till exempel *WordPress-8.1.0. tgz*:

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

Efter en liten stund rapporterar Azure CLI att diagrammet sparas, vilket visas i följande exempel på utdata:

```output
{
  "saved": true
}
```

### <a name="list-charts-in-the-repository"></a>Visa lista över diagram i databasen

Om du vill använda diagrammet som laddades upp i föregående steg måste det lokala Helm-lagringsplatsens index uppdateras. Du kan indexera om databaserna i Helm-klienten eller använda Azure CLI för att uppdatera lagrings platsens index. Varje gång du lägger till ett diagram i din lagrings plats måste det här steget utföras:

```azurecli
az acr helm repo add --name mycontainerregistry
```

Med ett diagram som är lagrat i din lagrings plats och det uppdaterade indexet tillgängligt lokalt kan du söka efter eller installera de vanliga Helm-klient kommandona. Om du vill se alla diagram i din lagrings plats använder du kommandot `helm search`, med ett eget Azure Container Registry namn:

```console
helm search mycontainerregistry
```

WordPress-diagrammet som du publicerade i föregående steg visas i listan, som du ser i följande exempel:

```output
NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  8.1.0           5.3.2       Web publishing platform for building blogs and websites.
```

Du kan också visa diagram med Azure CLI med hjälp av [AZ ACR Helm List][az-acr-helm-list]:

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>Visa information om ett Helm-diagram

Om du vill visa information om ett särskilt diagram i lagrings platsen kan du använda kommandot `helm inspect`.

```console
helm inspect mycontainerregistry/wordpress
```

När inget versions nummer anges används den *senaste* versionen. Helm returnerar detaljerad information om diagrammet, som du ser i följande komprimerade exempel utdata:

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

Du kan också visa informationen för ett diagram med kommandot Azure CLI [AZ ACR Helm show][az-acr-helm-show] . Den *senaste* versionen av ett diagram returneras som standard. Du kan lägga till `--version` för att visa en lista med en speciell version av ett diagram, till exempel *8.1.0*:

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>Installera ett Helm-diagram från lagrings platsen

Helm-diagrammet i din lagrings plats installeras genom att ange databas namnet och diagram namnet. Använd Helm-klienten för att installera WordPress-diagrammet:

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> Om du push-överför till din Azure Container Registry Helm Chart-lagringsplats och senare återgår till en ny CLI-session, behöver din lokala Helm-klient en uppdaterad autentiseringstoken. Om du vill hämta en ny autentiseringstoken använder du kommandot [AZ ACR Helm lagrings platsen Add][az-acr-helm-repo-add] .

Följande steg slutförs under installations processen:

- Helm-klienten söker igenom det lokala lagrings plats indexet.
- Motsvarande diagram hämtas från Azure Container Registry-lagringsplatsen.
- Diagrammet distribueras med hjälp av till-modulen i Kubernetes-klustret.

När installationen fortsätter följer du anvisningarna i kommandots utdata för att se WorPress-URL: er och autentiseringsuppgifter. Du kan också köra kommandot `kubectl get pods` för att se de Kubernetes-resurser som distribueras via Helm-diagrammet:

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Ta bort ett Helm-diagram från lagrings platsen

Om du vill ta bort ett diagram från databasen använder du kommandot [AZ ACR Helm Delete][az-acr-helm-delete] . Ange namnet på diagrammet, till exempel *WordPress*, och den version som ska tas bort, till exempel *8.1.0*.

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

Lämna `--version`-parametern om du vill ta bort alla versioner av det namngivna diagrammet.

Diagrammet fortsätter att returneras när du kör `helm search`. Helm-klienten uppdaterar inte automatiskt listan över tillgängliga diagram i en lagrings plats. Om du vill uppdatera Helm-klientens lagrings platsen-index använder du kommandot [AZ ACR Helm lagrings platsen Lägg till][az-acr-helm-repo-add] kommando igen:

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln användes ett befintligt Helm-diagram från den offentliga *säkra* databasen. Mer information om hur du skapar och distribuerar Helm-diagram finns i [utveckla Helm-diagram][develop-helm-charts].

Helm-diagram kan användas som en del av behållar Bygg processen. Mer information finns i [använda Azure Container Registry uppgifter][acr-tasks].

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
