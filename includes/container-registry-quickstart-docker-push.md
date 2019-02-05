---
title: ta med fil
description: ta med fil
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 01/23/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 7ed8b96a528d56b28262936c4b200762b3e93b8e
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302261"
---
## <a name="push-image-to-registry"></a>Push-överför avbildningen till registret

Innan du kan push-överföra en avbildning till Azure Container Registry måste du ha en avbildning. Om du inte har några lokala containeravbildningar ännu kan du köra följande [docker pull][docker-pull]-kommando för att hämta en befintlig avbildning från Docker Hub. I det här exemplet hämtar du `hello-world`-avbildningen.

```Docker
docker pull hello-world
```

Innan du kan push-överföra en avbildning till ditt register måste du tagga den med det fullständiga namnet på din ACR-inloggningsserver. Namnet på inloggningsservern har formatet *\<registry-name\>.azurecr.io* (endast gemener), till exempel *mycontainerregistry007.azurecr.io*.

Tagga avbildningen med hjälp av kommandot [docker tag][docker-tag]. Ersätt `<acrLoginServer>` med namnet på inloggningsservern för ACR-instansen.

```Docker
docker tag hello-world <acrLoginServer>/hello-world:v1
```

Använd slutligen [docker push][docker-push] för att överföra avbildningen till ACR-instansen. Ersätt `<acrLoginServer>` med namnet på inloggningsservern för ACR-instansen. Det här exemplet skapar lagringsplatsen **hello-world**, som innehåller `hello-world:v1`-avbildningen.

```Docker
docker push <acrLoginServer>/hello-world:v1
```

När du har push-överfört avbildningen till containerregistret tar du bort `hello-world:v1`-avbildningen från den lokala Docker-miljön. (Observera att detta [docker rmi][docker-rmi]-kommando inte tar bort avbildningen från **hello-world**-lagringsplatsen i Azure-containerregistret.)

```Docker
docker rmi <acrLoginServer>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

