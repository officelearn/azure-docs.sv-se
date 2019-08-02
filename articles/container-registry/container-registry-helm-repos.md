---
title: Använda Helm-databaser i Azure Container Registry
description: Lär dig hur du använder en Helm-lagringsplats med Azure Container Registry för att lagra diagram för dina program
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: iainfou
ms.openlocfilehash: 2135a3a5a8f14cf6c2e7fd2984d9b221e2445c1d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68309513"
---
# <a name="use-azure-container-registry-as-a-helm-repository-for-your-application-charts"></a>Använda Azure Container Registry som Helm-lagringsplats för dina program diagram

Om du snabbt vill hantera och distribuera program för Kubernetes kan du använda [paket hanteraren med öppen källkod för Helm][helm]. Med Helm definieras program som *diagram* som lagras i en Helm-diagram lagrings plats. De här diagrammen definierar konfigurationer och beroenden och kan vara versioner i hela programmets livs cykel. Azure Container Registry kan användas som värd för Helm-diagram-databaser.

Med Azure Container Registry har du en privat, säker lagrings plats för Helm diagram som kan integreras med build-pipeliner eller andra Azure-tjänster. Helm-diagram Arkiv i Azure Container Registry innehåller funktioner för geo-replikering för att hålla dina diagram nära distributioner och för redundans. Du betalar bara för det lagrings utrymme som används av diagrammen och är tillgängligt för alla Azure Container Registry pris nivåer.

Den här artikeln visar hur du använder en Helm-databas lagring som lagras i Azure Container Registry.

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Förhandsversioner görs tillgängliga för dig under förutsättning att du godkänner [kompletterande användningsvillkor][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra stegen i den här artikeln måste följande krav vara uppfyllda:

- **Azure Container Registry** – skapa ett behållar register i din Azure-prenumeration. Använd till exempel [Azure Portal](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md).
- **Helm klient version 2.11.0 (inte en RC-version) eller senare** – `helm version` kör för att hitta din aktuella version. Du behöver också en Helm-Server (till gång) som initierats i ett Kubernetes-kluster. Om det behövs kan du [skapa ett Azure Kubernetes service-kluster][aks-quickstart]. Mer information om hur du installerar och uppgraderar Helm finns i [Installera Helm][helm-install].
- **Azure CLI version 2.0.46 eller senare** – kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="add-a-repository-to-helm-client"></a>Lägga till en lagrings plats i Helm-klienten

En Helm-lagringsplats är en HTTP-server som kan lagra Helm-diagram. Azure Container Registry kan tillhandahålla den här lagringen för Helm-diagram och hantera index definitionen när du lägger till och tar bort diagram i lagrings platsen.

Om du vill lägga till din Azure Container Registry som en Helm diagram-lagringsplats använder du Azure CLI. Med den här metoden uppdateras din Helm-klient med URI: n och autentiseringsuppgifterna för databasen som stöds av Azure Container Registry. Du behöver inte ange den här lagrings informationen manuellt, så autentiseringsuppgifterna visas inte i kommando historiken, till exempel.

Om det behövs loggar du in på Azure CLI och följer anvisningarna:

```azurecli
az login
```

Konfigurera Azure CLI-standardinställningarna med namnet på din Azure Container Registry med hjälp av kommandot [AZ Configure][az-configure] . I följande exempel ersätter `<acrName>` du med namnet på ditt register:

```azurecli
az configure --defaults acr=<acrName>
```

Lägg nu till din Azure Container Registry Helm Chart-lagringsplats till Helm-klienten med kommandot [AZ ACR Helm lagrings platsen Add][az-acr-helm-repo-add] . Det här kommandot hämtar en autentiseringstoken för ditt Azure Container Registry som används av Helm-klienten. Autentiseringstoken är giltig i 1 timme. Precis som `docker login`kan du köra det här kommandot i framtida CLI-sessioner för att autentisera din Helm-klient med din Azure Container Registry Helm-diagram lagrings plats:

```azurecli
az acr helm repo add
```

## <a name="add-a-chart-to-the-repository"></a>Lägg till ett diagram i lagrings platsen

I den här artikeln ska vi hämta ett befintligt Helm-diagram från den offentliga Helm *stabila* lagrings platsen. Den *stabila* lagrings platsen är en granskad, offentlig lagrings platsen som innehåller vanliga program diagram. Paket underhållare kan skicka sina diagram till den *stabila* lagrings platsen, på samma sätt som Docker Hub tillhandahåller ett offentligt register för vanliga behållar avbildningar. Diagrammet som hämtas från den offentliga *stabila* lagrings platsen kan sedan skickas till din privata Azure Container Registry-lagringsplats. I de flesta fall skulle du skapa och ladda upp dina egna diagram för de program som du utvecklar. Mer information om hur du skapar egna Helm-diagram finns i [utveckla Helm-diagram][develop-helm-charts].

Skapa först en katalog på *~/ACR-Helm*och hämta sedan det befintliga *stabila/WordPress-* diagrammet:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm fetch stable/wordpress
```

Ange det hämtade diagrammet och anteckna WordPress-versionen som ingår i fil namnet. Kommandot angav inte någon särskild version, så den senaste versionen hämtades. `helm fetch stable/wordpress` Alla Helm-diagram innehåller ett versions nummer i fil namnet som följer [SemVer 2][semver2] -standarden. I följande exempel utdata är WordPress-diagrammet version *2.1.10*:

```
$ ls

wordpress-2.1.10.tgz
```

Skicka nu diagrammet till Helm-diagrammets lagrings plats i Azure Container Registry använda kommandot Azure CLI [AZ ACR Helm push][az-acr-helm-push] . Ange namnet på ditt Helm-diagram som hämtades i föregående steg, till exempel *WordPress-2.1.10. tgz*:

```azurecli
az acr helm push wordpress-2.1.10.tgz
```

Efter en liten stund rapporterar Azure CLI att diagrammet har sparats, vilket visas i följande exempel på utdata:

```
$ az acr helm push wordpress-2.1.10.tgz

{
  "saved": true
}
```

## <a name="list-charts-in-the-repository"></a>Visa lista över diagram i databasen

Helm-klienten har en lokal cachelagrad kopia av innehållet i fjärrdatabaser. Ändringar i en fjärrlagringsplats uppdaterar inte automatiskt listan över tillgängliga diagram som är kända lokalt av Helm-klienten. När du söker efter diagram över flera databaser använder Helm det lokala, cachelagrade indexet. Om du vill använda diagrammet som laddades upp i föregående steg måste det lokala Helm-lagringsplatsens index uppdateras. Du kan indexera om databaserna i Helm-klienten eller använda Azure CLI för att uppdatera lagrings platsens index. Varje gång du lägger till ett diagram i din lagrings plats måste det här steget utföras:

```azurecli
az acr helm repo add
```

Med ett diagram som är lagrat i din lagrings plats och det uppdaterade indexet tillgängligt lokalt kan du söka efter eller installera de vanliga Helm-klient kommandona. Använd `helm search <acrName>`om du vill se alla diagram i din lagrings plats. Ange ett eget Azure Container Registry namn:

```console
helm search <acrName>
```

WordPress-diagrammet som du publicerade i föregående steg visas i listan, som du ser i följande exempel:

```
$ helm search myacrhelm

NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  2.1.10          4.9.8       Web publishing platform for building blogs and websites.
```

Du kan också visa diagram med Azure CLI med hjälp av [AZ ACR Helm List][az-acr-helm-list]:

```azurecli
az acr helm list
```

## <a name="show-information-for-a-helm-chart"></a>Visa information om ett Helm-diagram

Om du vill visa information om ett särskilt diagram i lagrings platsen kan du använda den vanliga Helm-klienten igen. Använd`helm inspect`om du vill se information om diagrammet med namnet *WordPress*.

```console
helm inspect <acrName>/wordpress
```

När inget versions nummer anges används den *senaste* versionen. Helm returnerar detaljerad information om diagrammet, som du ser i följande komprimerade exempel utdata:

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

Du kan också visa informationen för ett diagram med kommandot Azure CLI [AZ ACR Helm show][az-acr-helm-show] . Den *senaste* versionen av ett diagram returneras som standard. Du kan lägga `--version` till en lista med en angiven version av ett diagram, till exempel *2.1.10*:

```azurecli
az acr helm show wordpress
```

## <a name="install-a-helm-chart-from-the-repository"></a>Installera ett Helm-diagram från lagrings platsen

Helm-diagrammet i din lagrings plats installeras genom att ange databas namnet och sedan diagram namnet. Använd Helm-klienten för att installera WordPress-diagrammet:

```console
helm install <acrName>/wordpress
```

> [!TIP]
> Om du push-överför till din Azure Container Registry Helm Chart-lagringsplats och senare återgår till en ny CLI-session, behöver din lokala Helm-klient en uppdaterad autentiseringstoken. Om du vill hämta en ny autentiseringstoken använder du kommandot [AZ ACR Helm lagrings platsen Add][az-acr-helm-repo-add] .

Följande steg slutförs under installations processen:

- Helm-klienten söker igenom det lokala lagrings plats indexet.
- Motsvarande diagram hämtas från Azure Container Registry-lagringsplatsen.
- Diagrammet distribueras med hjälp av till-modulen i Kubernetes-klustret.

I följande komprimerade exempel utdata visas de Kubernetes-resurser som distribueras via Helm-diagrammet:

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

## <a name="delete-a-helm-chart-from-the-repository"></a>Ta bort ett Helm-diagram från lagrings platsen

Om du vill ta bort ett diagram från databasen använder du kommandot [AZ ACR Helm Delete][az-acr-helm-delete] . Ange namnet på diagrammet, till exempel *WordPress*, och den version som ska tas bort, till exempel *2.1.10*.

```azurecli
az acr helm delete wordpress --version 2.1.10
```

Lämna `--version` parametern om du vill ta bort alla versioner av det namngivna diagrammet.

Diagrammet fortsätter att returneras i `helm search <acrName>`. Helm-klienten uppdaterar inte automatiskt listan över tillgängliga diagram i en lagrings plats. Om du vill uppdatera Helm-klientens lagrings platsen-index använder du kommandot [AZ ACR Helm lagrings platsen Lägg till][az-acr-helm-repo-add] kommando igen:

```azurecli
az acr helm repo add
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln användes ett befintligt Helm-diagram från den offentliga *säkra* databasen. Mer information om hur du skapar och distribuerar Helm-diagram finns i [utveckla Helm-diagram][develop-helm-charts].

Helm-diagram kan användas som en del av behållar Bygg processen. Mer information finns i [använda Azure Container Registry uppgifter][acr-tasks].

Mer information om hur du använder och hanterar Azure Container Registry finns i [metod tips][acr-bestpractices].

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
