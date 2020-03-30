---
title: Importera containeravbildningar
description: Importera behållaravbildningar till ett Azure-behållarregister med hjälp av Azure-API:er, utan att behöva köra Docker-kommandon.
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: caf7a47ac8f7ff0e72d2e049a7013542d274a225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051925"
---
# <a name="import-container-images-to-a-container-registry"></a>Importera behållaravbildningar till ett behållarregister

Du kan enkelt importera (kopiera) behållaravbildningar till ett Azure-behållarregister utan att använda Docker-kommandon. Importera till exempel avbildningar från ett utvecklingsregister till ett produktionsregister eller kopiera basavbildningar från ett offentligt register.

Azure Container Registry hanterar ett antal vanliga scenarier för att kopiera avbildningar från ett befintligt register:

* Importera från ett offentligt register

* Importera från ett annat Azure-behållarregister, i samma eller en annan Azure-prenumeration

* Importera från ett privat behållarregister som inte är Azure

Avbildningsimport till ett Azure-behållarregister har följande fördelar jämfört med att använda Docker CLI-kommandon:

* Eftersom klientmiljön inte behöver en lokal Docker-installation importerar du en behållaravbildning, oavsett vilken os-typ som stöds.

* När du importerar multiarkitekturavbildningar (till exempel officiella Docker-avbildningar) kopieras avbildningar för alla arkitekturer och plattformar som anges i manifestlistan.

Om du vill importera behållaravbildningar kräver den här artikeln att du kör Azure CLI i Azure Cloud Shell eller lokalt (version 2.0.55 eller senare rekommenderas). Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

> [!NOTE]
> Om du behöver distribuera identiska behållaravbildningar över flera Azure-regioner stöder Azure Container Registry även [geo-replikering](container-registry-geo-replication.md). Genom att georepl igenge ett register (Premium-tjänstnivå krävs) kan du betjäna flera regioner med identiska avbildnings- och taggnamn från ett enda register.
>

## <a name="prerequisites"></a>Krav

Om du inte redan har ett Azure-behållarregister skapar du ett register. Stegvis finns i [Snabbstart: Skapa ett privat behållarregister med Azure CLI](container-registry-get-started-azure-cli.md).

Om du vill importera en avbildning till ett Azure-behållarregister måste din identitet ha skrivbehörighet till målregistret (minst deltagarrollen). Se [Azure Container Registry roller och behörigheter](container-registry-roles.md). 

## <a name="import-from-a-public-registry"></a>Importera från ett offentligt register

### <a name="import-from-docker-hub"></a>Importera från Docker Hub

Använd till exempel kommandot [az acr import][az-acr-import] för `hello-world:latest` att importera multiarkitekturavbildningen från Docker Hub till ett register med namnet *Myregistry*. Eftersom `hello-world` är en officiell bild från Docker Hub `library` finns den här bilden i standarddatabasen. Inkludera databasnamnet och eventuellt en tagg i `--source` värdet för bildparametern. (Du kan också identifiera en bild genom dess manifestsammandrag i stället för efter tagg, vilket garanterar en viss version av en bild.)
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

Du kan kontrollera att flera manifest är associerade `az acr repository show-manifests` med den här bilden genom att köra kommandot:

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

I följande exempel importeras `tensorflow` en offentlig bild från databasen i Docker Hub:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Importera från Microsoft Container Registry

Importera till exempel den senaste Windows `windows` Server Core-avbildningen från databasen i Microsoft Container Registry.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:latest \
--image servercore:latest
```

## <a name="import-from-another-azure-container-registry"></a>Importera från ett annat Azure-behållarregister

Du kan importera en avbildning från ett annat Azure-behållarregister med hjälp av integrerade Azure Active Directory-behörigheter.

* Din identitet måste ha Azure Active Directory-behörigheter för att läsa från källregistret (reader-rollen) och skriva till målregistret (deltagarrollen).

* Registret kan finnas i samma eller en annan Azure-prenumeration i samma Active Directory-klientorganisation.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importera från ett register i samma prenumeration

Importera till exempel `aci-helloworld:latest` avbildningen från ett källregister *mittkälla register* till *mitt register* i samma Azure-prenumeration.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

I följande exempel importeras en bild efter manifestsammandrag (SHA-256 hash, representerad som `sha256:...`) i stället för efter tagg:

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importera från ett register i en annan prenumeration

I följande exempel finns *mittkälla register* i en annan prenumeration än *mitt register* i samma Active Directory-klientorganisation. Ange resurs-ID för källregistret `--registry` med parametern. Observera att `--source` parametern bara anger källdatabasen och taggen, inte registrets inloggningsservernamn.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importera från ett register med hjälp av autentiseringsuppgifter för tjänstens huvudnamn

Om du vill importera från ett register som du inte kan komma åt med Active Directory-behörigheter kan du använda autentiseringsuppgifter för tjänstens huvudnamn (om tillgängligt). Ange appID och lösenord för ett [Active Directory-tjänsthuvudnamn](container-registry-auth-service-principal.md) som har ÅTKOMST TILL källregistret. Att använda ett huvudnamn för tjänsten är användbart för att skapa system och andra obevakade system som behöver importera avbildningar till registret.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  –-password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importera från ett privat behållarregister som inte är Azure

Importera en avbildning från ett privat register genom att ange autentiseringsuppgifter som aktiverar pull-åtkomst till registret. Hämta till exempel en avbildning från ett privat Docker-register: 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du lära dig om hur du importerar behållaravbildningar till ett Azure-behållarregister från ett offentligt register eller ett annat privat register. Ytterligare bildimportalternativ finns i kommandoreferensen [för az acr-import.][az-acr-import] 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
