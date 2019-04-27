---
title: Importera behållaravbildningar till Azure Container Registry
description: 'Importera behållaravbildningar till ett Azure container registry med Azure API: er, utan att behöva köra Docker-kommandon.'
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/06/2019
ms.author: danlep
ms.openlocfilehash: b8a2280fe82e0f4be8e2812f5494150927642692
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60827297"
---
# <a name="import-container-images-to-a-container-registry"></a>Importera behållaravbildningar till ett behållarregister

Du kan enkelt importera (kopiera) behållaravbildningar till ett Azure container registry, utan att använda Docker-kommandon. Till exempel importera bilder från ett register med utveckling till ett register för produktion eller kopiera grundläggande avbildningar från ett offentligt register.

Azure Container Registry hanterar ett antal vanliga scenarier för att kopiera avbildningar från ett befintligt register:

* Importera från ett offentligt register

* Importera från en annan Azure-behållarregister, i samma eller en annan Azure-prenumeration

* Importera från en icke-Azure-privat behållarregister

Bild import till en Azure container registry har följande fördelar jämfört med Docker CLI-kommandon:

* Eftersom miljön klienten inte behöver en lokal Docker-installation kan du importera valfri behållaravbildning, oavsett den OS-typ som stöds.

* När du importerar flera arkitektur bilder (till exempel officiella Docker-avbildningar), kopieras avbildningar för alla plattformar som anges i listan med manifest och arkitekturer.

Om du vill importera behållaravbildningar i den här artikeln kräver att du kör Azure CLI i Azure Cloud Shell eller lokalt (version 2.0.55 eller senare rekommenderas). Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

> [!NOTE]
> Om du vill distribuera identiska behållaravbildningar över flera Azure-regioner stöder Azure Container Registry också [geo-replikering](container-registry-geo-replication.md). Genom geo-replikering ett register (Premium-SKU krävs), kan du hantera flera regioner med identiska namn från ett enda register som bild och tagg.
>

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du inte redan har ett Azure container registry kan du skapa ett register. Anvisningar finns i [snabbstarten: Skapa ett privat behållarregister med Azure CLI](container-registry-get-started-azure-cli.md).

Om du vill importera en avbildning till Azure container registry, din identitet måste ha skrivbehörighet till mål-register (minst deltagarrollen). Se [Azure Container Registry roller och behörigheter](container-registry-roles.md). 

## <a name="import-from-a-public-registry"></a>Importera från ett offentligt register

### <a name="import-from-docker-hub"></a>Importera från Docker Hub

Till exempel använda den [az acr import] [ az-acr-import] kommando för att importera flera arkitekturen `hello-world:latest` avbildning från Docker Hub till ett register med namnet *myregistry*. Eftersom `hello-world` är en officiell avbildning från Docker Hub, den här bilden är i standard `library` lagringsplats. Inkludera namnet på lagringsplatsen och eventuellt en tagg i värdet för den `--source` bild-parametern. (Du kan också identifiera en avbildning genom dess manifest sammanfattad i stället för efter tagg som garanterar en viss version av en avbildning.)
 
```azurecli
az acr import --name myregistry --source docker.io/library/hello-world:latest --image hello-world:latest
```

Du kan kontrollera att flera manifest är associerade med den här avbildningen genom att köra den `az acr repository show-manifests` kommando:

```azurecli
az acr repository show-manifests --name myregistry --repository hello-world
```

I följande exempel importerar en offentlig avbildning från den `tensorflow` lagringsplatsen i Docker Hub:

```azurecli
az acr import --name myregistry --source docker.io/tensorflow/tensorflow:latest-gpu --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Importera från Microsoft Container Registry

Exempelvis kan importera den senaste Windows Server Core-avbildningen från den `windows` lagringsplatsen i Microsoft Container Registry.

```azurecli
az acr import --name myregistry --source mcr.microsoft.com/windows/servercore:latest --image servercore:latest
```

## <a name="import-from-another-azure-container-registry"></a>Importera från en annan Azure-behållarregister

Du kan importera en bild från en annan Azure-behållarregister med hjälp av integrerade Azure Active Directory-behörigheter.

* Din identitet måste ha Azure Active Directory-behörigheter att läsa från registret källa (rollen Läsare) och för att skriva till målet registret (deltagarrollen).

* Registret kan vara i samma eller en annan Azure-prenumeration i samma Active Directory-klient.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importera från ett register i samma prenumeration

Till exempel importera den `aci-helloworld:latest` avbildning från en källa registret *mysourceregistry* till *myregistry* i samma Azure-prenumeration.

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld:latest --image hello-world:latest
```

I följande exempel importerar en bild av manifest sammanfattad (SHA-256-hash som `sha256:...`) i stället för genom att tagga:

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importera från ett register i en annan prenumeration

I följande exempel *mysourceregistry* finns i en annan prenumeration från *myregistry* i samma Active Directory-klient. Ange resurs-ID för käll-registret med den `--registry` parametern. Observera att den `--source` parametern anger endast lagringsplats och bild, inte registret namnet på inloggningsservern.
 
```azurecli
az acr import --name myregistry --source sourcerepo/aci-helloworld:latest --image aci-hello-world:latest --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importera från ett register med hjälp av autentiseringsuppgifter för tjänstens huvudnamn

Om du vill importera från ett register som du inte kan komma åt med hjälp av Active Directory-behörigheter kan du använda autentiseringsuppgifter för tjänstens huvudnamn (om tillgängligt). Ange appID och lösenord för en Active Directory [tjänstens huvudnamn](container-registry-auth-service-principal.md) som har åtkomst till ACRPull i käll-registret. Tjänstens huvudnamn är användbart för build-system och andra obevakad system som måste du importera avbildningar till registret.

```azurecli
az acr import --name myregistry --source sourceregistry.azurecr.io/sourcerepo/sourceimage:tag --image targetimage:tag --username <SP_App_ID> –-password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importera från en icke-Azure-privat behållarregister

Importera en avbildning från ett privat register genom att ange autentiseringsuppgifter som ger pull-åtkomst till registret. Exempel: hämta en avbildning från ett privat Docker-register: 

```azurecli
az acr import --name myregistry --source docker.io/sourcerepo/sourceimage:tag --image sourceimage:tag --username <username> --password <password>
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om att importera behållaravbildningar till ett Azure container registry från ett offentligt register eller en annan privat register. Importera alternativ för ytterligare en avbildning kan du läsa den [az acr import] [ az-acr-import] kommandot referens. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
