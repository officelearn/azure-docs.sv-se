---
title: Innehållsformat som stöds
description: Lär dig mer om innehållsformat som stöds av Azure Container Registry, inklusive Docker-kompatibla behållaravbildningar, Helm-diagram, OCI-avbildningar och OCI-artefakter.
ms.topic: article
ms.date: 08/30/2019
ms.openlocfilehash: ab915385f46f83c7b655acd1a48d66df84b50653
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247012"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Innehållsformat som stöds i Azure Container Registry

Använd en privat databas i Azure Container Registry för att hantera något av följande innehållsformat. 

## <a name="docker-compatible-container-images"></a>Docker-kompatibla behållaravbildningar

Följande avbildningsformat för Docker-behållare stöds:

* [Docker-bildmanifest V2, schema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker Image Manifest V2, Schema 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - innehåller manifestlistor som gör att register för att lagra multiplattformsbilder under en enda "image:tag"-referens

## <a name="oci-images"></a>OCI-bilder

Azure Container Registry stöder avbildningar som uppfyller [OCI-specifikationen (Open Container Initiative).](https://github.com/opencontainers/image-spec/blob/master/spec.md) Förpackningsformat inkluderar [Singularity Image Format (SIF)](https://github.com/sylabs/sif).

## <a name="oci-artifacts"></a>OCI-artefakter

Azure Container Registry stöder [OCI Distributionsspecifikation](https://github.com/opencontainers/distribution-spec), en leverantörsneutral, molnoberoende spec för att lagra, dela, skydda och distribuera behållaravbildningar och andra innehållstyper (artefakter). Specifikationen gör det möjligt för ett register att lagra ett brett spektrum av artefakter utöver behållaravbildningar. Du använder verktyg som är lämpliga för artefakten för att driva och dra artefakter. Ett exempel finns i [Push och hämta en OCI-artefakt med hjälp av ett Azure-behållarregister](container-registry-oci-artifacts.md).

Mer information om OCI-artefakter finns i [OCI Registry as Storage (ORAS)](https://github.com/deislabs/oras) repo och [OCI Artifacts](https://github.com/opencontainers/artifacts) repo på GitHub.

## <a name="helm-charts"></a>Helm-diagram

Azure Container Registry kan vara värd för databaser för [Helm-diagram](https://helm.sh/), ett förpackningsformat som används för att snabbt hantera och distribuera program för Kubernetes. [Helm-klient](https://docs.helm.sh/using_helm/#installing-helm) version 2 (2.11.0 eller senare) stöds.

## <a name="next-steps"></a>Nästa steg

* Se hur du [skickar och hämtar](container-registry-get-started-docker-cli.md) avbildningar med Azure Container Registry.

* Använd [ACR-uppgifter](container-registry-tasks-overview.md) för att skapa och testa behållaravbildningar. 

* Använd [Moby BuildKit](https://github.com/moby/buildkit) för att bygga och paketera behållare i OCI-format.

* Konfigurera en [Helm-databas](container-registry-helm-repos.md) som finns i Azure Container Registry. 


