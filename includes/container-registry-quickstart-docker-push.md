---
title: inkludera fil
description: inkludera fil
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 01/23/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: b10bf18fde850223bda80a597f448747558113f1
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84752158"
---
## <a name="push-image-to-registry"></a>Push-överför avbildningen till registret

Innan du kan push-överföra en avbildning till Azure Container Registry måste du ha en avbildning. Om du inte har några lokala containeravbildningar ännu kan du köra följande [docker pull][docker-pull]-kommando för att hämta en befintlig avbildning från Docker Hub. I det här exemplet hämtar du `hello-world`-avbildningen.

```
docker pull hello-world
```

Innan du kan push-överföra en avbildning till registret måste du tagga den med det fullständigt kvalificerade namnet på inloggnings servern för registret. Inloggnings serverns namn har formatet * \<registry-name\> . azurecr.io* (alla gemener), till exempel *mycontainerregistry007.azurecr.io*.

Tagga avbildningen med hjälp av kommandot [docker tag][docker-tag]. Ersätt `<login-server>` med namnet på inloggningsservern för ACR-instansen.

```
docker tag hello-world <login-server>/hello-world:v1
```

Slutligen använder du [Docker push][docker-push] för att push-överföra avbildningen till register instansen. Ersätt `<login-server>` med inloggnings Server namnet för din register instans. Det här exemplet skapar lagringsplatsen **hello-world**, som innehåller `hello-world:v1`-avbildningen.

```
docker push <login-server>/hello-world:v1
```

När du har push-överfört avbildningen till containerregistret tar du bort `hello-world:v1`-avbildningen från den lokala Docker-miljön. (Observera att detta [docker rmi][docker-rmi]-kommando inte tar bort avbildningen från **hello-world**-lagringsplatsen i Azure-containerregistret.)

```
docker rmi <login-server>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

