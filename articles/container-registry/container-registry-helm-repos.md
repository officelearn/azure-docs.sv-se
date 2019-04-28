---
title: Använd Helm databaser i Azure Container Registry
description: Lär dig hur du använder ett Helm-lagringsplatsen med Azure Container Registry för att lagra diagram för dina program
services: container-registry
author: iainfoulds
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: iainfou
ms.openlocfilehash: ba0e1386d67e920f1805d244f9042044bb462ec9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62109859"
---
# <a name="use-azure-container-registry-as-a-helm-repository-for-your-application-charts"></a>Använda Azure Container Registry som ett Helm-lagringsplatsen för dina program-diagram

Du kan använda för att snabbt hantera och distribuera program för Kubernetes, den [Pakethanteraren för öppen källkod Helm][helm]. Program med Helm, anges som *diagram* som lagras i en lagringsplats för Helm-diagram. Dessa diagram definierar konfigurationer och beroenden och kan versionshanteras under hela programmets livscykel. Azure Container Registry kan användas som värd för databaser för Helm-diagram.

Med Azure Container Registry har du en privat, säker Helm-diagram-lagringsplatsen, som kan integreras med skapandet av pipelines eller andra Azure-tjänster. Helm-diagram-databaser i Azure Container Registry innehåller funktioner för geo-replikering för att hålla dina diagram nära distributioner och för redundans. Du bara betalar för den lagring som används av diagrammen och är tillgängliga för alla nivåer i Azure Container Registry-priset.

Den här artikeln visar hur du använder en lagringsplats för Helm-diagrammet som lagras i Azure Container Registry.

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="before-you-begin"></a>Innan du börjar

Följande krav måste uppfyllas för att slutföra stegen i den här artikeln:

- **Azure Container Registry** -skapa ett behållarregister i Azure-prenumerationen. Till exempel använda den [Azure-portalen](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md).
- **Helm-klientversionen 2.11.0 (inte en RC-versionen) eller senare** – kör `helm version` att hitta din nuvarande version. Du måste också ett Helm-server (Tiller) som initieras i ett Kubernetes-kluster. Om det behövs kan du [skapa ett Azure Kubernetes Service-kluster][aks-quickstart]. Läs mer om hur du installera och uppgradera Helm [installera Helm][helm-install].
- **Azure CLI version 2.0.46 eller senare** – kör `az --version` att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="add-a-repository-to-helm-client"></a>Lägga till en lagringsplats i Helm-klienten

Ett Helm-lagringsplatsen är en HTTP-server som kan lagra Helm-diagram. Azure Container Registry kan ange den här lagringen för Helm-diagram och hantera indexdefinitionen när du lägger till och ta bort diagram i databasen.

Om du vill lägga till ditt Azure-Behållarregister som en lagringsplats för Helm-diagrammet kan du använda Azure CLI. Med den här metoden uppdateras Helm klienten med URI och autentiseringsuppgifter för den lagringsplats som backas upp av Azure Container Registry. Du behöver inte ange informationen databasen manuellt så att autentiseringsuppgifterna som inte visas i kommandohistoriken, till exempel.

Om det behövs, logga in på Azure CLI och följa anvisningarna:

```azurecli
az login
```

Konfigurera standardinställningar för Azure CLI med namnet på din Azure-Behållarregister med hjälp av den [az konfigurera] [ az-configure] kommando. I följande exempel ersätter `<acrName>` med namnet på ditt register:

```azurecli
az configure --defaults acr=<acrName>
```

Lägg nu till din lagringsplats för Azure Container Registry Helm-diagrammet Helm klienten med hjälp av den [az acr helm-lagringsplatsen Lägg till] [ az-acr-helm-repo-add] kommando. Det här kommandot hämtar en autentisering token för Azure container registry som används av Helm-klienten. Autentiseringstoken är giltig för 1 timme. Liknar `docker login`, du kan köra det här kommandot i framtida CLI-sessioner för att autentisera din Helm-klient med din lagringsplats för Azure Container Registry Helm-diagrammet:

```azurecli
az acr helm repo add
```

## <a name="add-a-chart-to-the-repository"></a>Lägga till ett diagram i databasen

I den här artikeln ska vi hämta ett befintligt Helm-diagram från offentliga Helm *stabil* lagringsplatsen. Den *stabil* lagringsplats är en granskad, offentliga lagringsplats som innehåller vanliga program diagram. Paketet sköter underhåll själva kan skicka sina diagram för den *stabil* lagringsplats på samma sätt som Docker-hubb tillhandahåller ett offentligt register för gemensam behållaravbildningar. Diagrammet som hämtats från offentligt *stabil* lagringsplatsen kan sedan skickas till din privata Azure Container Registry-lagringsplatsen. I de flesta fall skulle du skapa och ladda upp egna diagram för de program som du utvecklar. Läs mer om hur du skapar egna Helm-diagram, [utveckla Helm-diagram][develop-helm-charts].

Börja med att skapa en katalog på *~/acr-helm*, ladda ned den befintliga *stable/wordpress* diagram:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm fetch stable/wordpress
```

Lista hämtade diagrammet och Observera Wordpress-version som ingår i filnamnet. Den `helm fetch stable/wordpress` kommando inte angav en viss version, så den *senaste* version hämtades. Alla Helm-diagram innehåller ett versionsnummer i filnamnet som följer den [SemVer 2] [ semver2] standard. I följande Exempelutdata Wordpress-diagrammet är version *2.1.10*:

```
$ ls

wordpress-2.1.10.tgz
```

Skicka nu diagrammet till Helm-diagram-lagringsplatsen i Azure Container Registry med Azure CLI [az acr helm push] [ az-acr-helm-push] kommando. Ange namnet på din Helm-diagrammet hämtade i föregående steg, till exempel *wordpress-2.1.10.tgz*:

```azurecli
az acr helm push wordpress-2.1.10.tgz
```

Efter en liten stund rapporterar Azure CLI att diagrammet har sparats, som visas i följande Exempelutdata:

```
$ az acr helm push wordpress-2.1.10.tgz

{
  "saved": true
}
```

## <a name="list-charts-in-the-repository"></a>Lista diagram i databasen

Helm-klienten har en lokal cachelagrad kopia av innehållet i fjärranslutna databaser. Ändringar till en fjärrlagringsplats uppdateras inte automatiskt en lista över tillgängliga diagram lokalt är känt av Helm-klienten. När du söker efter diagram över lagringsplatser använder Helm sin lokala cachelagrade index. För att använda diagrammet som laddades upp i föregående steg, måste du uppdatera det lokala Helm-lagringsplatsen indexet. Du kan indexera om databaserna i Helm-klienten eller använda Azure CLI för att uppdatera indexet för databasen. Varje gång du lägger till ett diagram till databasen, måste det här steget utföras:

```azurecli
az acr helm repo add
```

Du kan använda vanliga Helm-klientkommandon att söka efter och installera med ett diagram som lagras i databasen och det uppdaterade indexet som är tillgängliga lokalt. Om du vill se alla diagram i databasen, använda `helm search <acrName>`. Ange din egen Azure Container Registry-namn:

```console
helm search <acrName>
```

Wordpress-diagrammet som överfördes i föregående steg visas som visas i följande Exempelutdata:

```
$ helm search myacrhelm

NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  2.1.10          4.9.8       Web publishing platform for building blogs and websites.
```

Du kan också ange diagram med Azure CLI, med hjälp av [az acr helm lista][az-acr-helm-list]:

```azurecli
az acr helm list
```

## <a name="show-information-for-a-helm-chart"></a>Visa information för ett Helm-diagram

Om du vill visa information för ett specifikt diagram i lagringsplatsen, kan du igen använda reguljära Helm-klienten. Att visa information för diagrammet med namnet *wordpress*, använda `helm inspect`.

```console
helm inspect <acrName>/wordpress
```

När inga versionsnumret tillhandahålls den *senaste* version som används. Helm returnerar detaljerad information om schemat, som visas i följande komprimerade exempel på utdata:

```
$ helm inspect myacrhelm/wordpress

appVersion: 4.9.8
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: https://www.wordpress.com/
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
  name: bitnami-bot
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 2.1.10
[...]
```

Du kan också visa information för ett diagram med Azure CLI [az acr helm show] [ az-acr-helm-show] kommando. Igen, den *senaste* version av ett diagram returneras som standard. Du kan lägga till `--version` vill visa en specifik version av ett diagram, till exempel *2.1.10*:

```azurecli
az acr helm show wordpress
```

## <a name="install-a-helm-chart-from-the-repository"></a>Installera ett Helm-diagram från databasen

Helm-diagrammet i databasen är installerad genom att ange namnet på lagringsplatsen och diagramområdets namn. Använd Helm-klienten för att installera Wordpress-diagrammet:

```console
helm install <acrName>/wordpress
```

> [!TIP]
> Om du push-överföra till din lagringsplats för Azure Container Registry Helm-diagrammet och senare gå tillbaka i en ny CLI-session, måste en uppdaterad autentiseringstoken i din lokala Helm-klient. Så här skaffar du en ny autentiseringstoken i [az acr helm-lagringsplatsen Lägg till] [ az-acr-helm-repo-add] kommando.

Följande steg måste utföras under installationen.

- Helm-klienten söker lagringsplats indexet.
- Motsvarande diagrammet hämtas från Azure Container Registry-databasen.
- Diagrammet med hjälp av Tiller i Kubernetes-klustret.

Följande komprimerade exempel på utdata visar Kubernetes-resurser som distribueras via Helm-diagrammet:

```
$ helm install myacrhelm/wordpress

NAME:   irreverent-jaguar
LAST DEPLOYED: Thu Sep 13 21:44:20 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                          READY  STATUS   RESTARTS  AGE
irreverent-jaguar-wordpress-7ff46d9b8c-b7v6m  0/1    Pending  0         1s
irreverent-jaguar-mariadb-0                   0/1    Pending  0         1s
[...]
```

## <a name="delete-a-helm-chart-from-the-repository"></a>Ta bort ett Helm-diagram från databasen

Ta bort ett diagram från databasen genom att använda den [az acr helm delete] [ az-acr-helm-delete] kommando. Ange namnet på diagrammet, till exempel *wordpress*, och versionen för att ta bort, till exempel *2.1.10*.

```azurecli
az acr helm delete wordpress --version 2.1.10
```

Om du vill ta bort alla versioner av namngivna diagrammet hoppar över den `--version` parametern.

Fortsätter att returneras i `helm search <acrName>`. Helm-klienten uppdatera inte igen och automatiskt listan över tillgängliga diagrammen i en databas. Uppdatera Helm klienten lagringsplatsen indexet med den [az acr helm-lagringsplatsen Lägg till] [ az-acr-helm-repo-add] -kommandot på nytt:

```azurecli
az acr helm repo add
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln används ett befintligt Helm-diagram från offentligt *stabil* lagringsplats. Läs mer om hur du skapar och distribuerar Helm-diagram, [utveckla Helm-diagram][develop-helm-charts].

Helm-diagram kan användas som en del av behållaren skapandeprocessen. Mer information finns i [använder Azure Container Registry uppgifter][acr-tasks].

Mer information om hur du använder och hanterar Azure Container Registry finns i den [bästa praxis][acr-bestpractices].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[develop-helm-charts]: https://docs.helm.sh/developing_charts/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
