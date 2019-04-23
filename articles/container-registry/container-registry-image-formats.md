---
title: Azure Container Registry format
description: Läs mer om content de format som stöds i Azure Container Registry.
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 04/18/2019
ms.author: danlep
ms.openlocfilehash: fe129847e685c7151a9b7ad7ea65abbd38530733
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997078"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Innehåll format som stöds i Azure Container Registry

Använda en privat lagringsplats i Azure Container Registry för att hantera något av följande innehåll format. 

## <a name="docker-compatible-container-images"></a>Docker-kompatibla behållaravbildningar

De följande bildformat för Docker-behållaren stöds:

* [Docker Image Manifest V2, Schema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker-avbildning Manifest V2, schemat 2](https://docs.docker.com/registry/spec/manifest-v2-2/) -innehåller Manifest visas som tillåter register att lagra flera plattformar avbildningar i en enda ”image: tag”-referens

## <a name="oci-images"></a>OCI avbildningar

Azure Container Registry har även stöd för avbildningar som uppfyller den [öppna behållare initiativ (OCI) formatspecifikationen](https://github.com/opencontainers/image-spec/blob/master/spec.md). Paketering format är [Singularity bild Format (så)](https://www.sylabs.io/2018/03/sif-containing-your-containers/).

## <a name="helm-charts"></a>Helm-diagram

Azure Container Registry kan vara värd för databaser för [Helm diagram](https://helm.sh/), en förpackningsformat som används för att snabbt hantera och distribuera program för Kubernetes. [Helm klienten](https://docs.helm.sh/using_helm/#installing-helm) version 2.11.0 eller senare stöds.

## <a name="next-steps"></a>Nästa steg

* Se hur du [skicka och hämta](container-registry-get-started-docker-cli.md) avbildningar med Azure Container Registry.

* Använd [ACR uppgifter](container-registry-tasks-overview.md) att skapa och testa behållaravbildningar. 

* Använd den [Moby BuildKit](https://github.com/moby/buildkit) att bygga och paketera behållare i OCI format.

* Konfigurera en [Helm-lagringsplatsen](container-registry-helm-repos.md) i Azure Container Registry. 


