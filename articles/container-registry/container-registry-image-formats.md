---
title: Azure Container Registry format
description: Läs mer om content de format som stöds i Azure Container Registry.
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 10/31/2018
ms.author: danlep
ms.openlocfilehash: e7155604339bc634078fd022e05ede5f902bc0d8
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634729"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Innehåll format som stöds i Azure Container Registry

Använda en privat lagringsplats i Azure Container Registry för att hantera något av följande innehåll format. 

## <a name="docker-compatible-container-images"></a>Docker-kompatibla behållaravbildningar

* [Docker-avbildning Manifest V2, schemat 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker-avbildning Manifest V2, schemat 2](https://docs.docker.com/registry/spec/manifest-v2-2/) -innehåller Manifest visas som tillåter register att lagra flera plattformar avbildningar i en enda ”image: tag”-referens

* [Öppna formatspecifikationen för behållaren initiativ (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md) 


## <a name="helm-charts"></a>Helm-diagram

Azure Container Registry kan också vara värd för databaser för [Helm diagram](https://helm.sh/), en förpackningsformat som används för att snabbt hantera och distribuera program för Kubernetes. [Helm klienten](https://docs.helm.sh/using_helm/#installing-helm) version 2.11.0 eller senare stöds.

## <a name="next-steps"></a>Nästa steg

* Se hur du [skicka och hämta](container-registry-get-started-docker-cli.md) avbildningar med Azure Container Registry.

* Använd [ACR uppgifter](container-registry-tasks-overview.md) att skapa och testa behållaravbildningar. 

* Använd den [Moby BuildKit](https://github.com/moby/buildkit) att bygga och paketera behållare i OCI format.

* Konfigurera en [Helm-lagringsplatsen](container-registry-helm-repos.md) i Azure Container Registry. 


