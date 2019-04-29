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
ms.openlocfilehash: 09eaf9465ec3912dea6e1f3ee1693f6bfed50abc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60787418"
---
## <a name="push-image-to-registry"></a>Push-överför avbildningen till registret

Innan du kan push-överföra en avbildning till Azure Container Registry måste du ha en avbildning. Om du inte har några lokala containeravbildningar ännu kan du köra följande [docker pull][docker-pull]-kommando för att hämta en befintlig avbildning från Docker Hub. I det här exemplet hämtar du `hello-world`-avbildningen.

```
docker pull hello-world
```

Innan du kan push-överföra en avbildning till ditt register måste du tagga den med det fullständiga namnet på din ACR-inloggningsserver. Namnet på inloggningsservern har formatet *\<registry-name\>.azurecr.io* (endast gemener), till exempel *mycontainerregistry007.azurecr.io*.

Tagga avbildningen med hjälp av kommandot [docker tag][docker-tag]. Ersätt `<acrLoginServer>` med namnet på inloggningsservern för ACR-instansen.

```
docker tag hello-world <acrLoginServer>/hello-world:v1
```

Använd slutligen [docker push][docker-push] för att överföra avbildningen till ACR-instansen. Ersätt `<acrLoginServer>` med namnet på inloggningsservern för ACR-instansen. Det här exemplet skapar lagringsplatsen **hello-world**, som innehåller `hello-world:v1`-avbildningen.

```
docker push <acrLoginServer>/hello-world:v1
```

När du har push-överfört avbildningen till containerregistret tar du bort `hello-world:v1`-avbildningen från den lokala Docker-miljön. (Observera att detta [docker rmi][docker-rmi]-kommando inte tar bort avbildningen från **hello-world**-lagringsplatsen i Azure-containerregistret.)

```
docker rmi <acrLoginServer>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

