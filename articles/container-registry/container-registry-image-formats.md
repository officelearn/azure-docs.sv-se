---
title: Azure Container Registry innehålls format
description: Lär dig mer om innehålls format som stöds i Azure Container Registry.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 04/18/2019
ms.author: danlep
ms.openlocfilehash: c804dd7b73ea4a51c02c8b342a4ac60d992ec7c5
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310681"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Innehålls format som stöds i Azure Container Registry

Använd ett privat lager i Azure Container Registry för att hantera något av följande innehålls format. 

## <a name="docker-compatible-container-images"></a>Docker-kompatibla behållar avbildningar

Följande versioner av Docker-behållar avbildningar stöds:

* [Docker-avbildnings manifest v2, schema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker-avbildnings manifest v2, schema 2](https://docs.docker.com/registry/spec/manifest-v2-2/) – innehåller manifest listor som tillåter register att lagra avbildningar av flera plattformar under en enda "image: tag"-referens

## <a name="oci-images"></a>OCI-avbildningar

Azure Container Registry stöder också bilder som uppfyller specifikationen för [avbildnings formatet Open container Initiative (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md). Paket formaten innehåller ett [bild format för bilder (SIF)](https://www.sylabs.io/2018/03/sif-containing-your-containers/).

## <a name="helm-charts"></a>Helm-diagram

Azure Container Registry kan vara värd för databaser för [Helm-diagram](https://helm.sh/), ett paket format som används för att snabbt hantera och distribuera program för Kubernetes. [Helm](https://docs.helm.sh/using_helm/#installing-helm) -2.11.0 eller senare stöds.

## <a name="next-steps"></a>Nästa steg

* Se hur du [push-överför och hämtar](container-registry-get-started-docker-cli.md) bilder med Azure Container Registry.

* Använd [ACR-uppgifter](container-registry-tasks-overview.md) för att bygga och testa behållar avbildningar. 

* Använd [Moby-BuildKit](https://github.com/moby/buildkit) för att bygga och paketera behållare i OCI-format.

* Konfigurera en [Helm-lagringsplats](container-registry-helm-repos.md) som finns i Azure Container Registry. 


