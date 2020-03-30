---
title: Kontrollera registrets hälsotillstånd
description: Lär dig hur du kör ett snabbt diagnostiskt kommando för att identifiera vanliga problem när du använder ett Azure-behållarregister, inklusive lokal Docker-konfiguration och anslutning till registret
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: ea4432c9e92c4a0380517e39678814e2d1cb3bfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456405"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Kontrollera hälsotillståndet för ett Azure-behållarregister

När du använder ett Azure-behållarregister kan du ibland stöta på problem. Du kanske till exempel inte kan hämta en behållaravbildning på grund av ett problem med Docker i den lokala miljön. Ett nätverksproblem kan också hindra dig från att ansluta till registret. 

Som ett första diagnostiskt steg kör du kommandot [az acr check-health][az-acr-check-health] för att få information om miljöns hälsa och eventuellt åtkomst till ett målregister. Det här kommandot är tillgängligt i Azure CLI version 2.0.67 eller senare. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

## <a name="run-az-acr-check-health"></a>Kör az acr check-hälsa

Följande exempel visar olika sätt `az acr check-health` att köra kommandot.

> [!NOTE]
> Om du kör kommandot i Azure Cloud Shell kontrolleras inte den lokala miljön. Du kan dock kontrollera åtkomsten till ett målregister.

### <a name="check-the-environment-only"></a>Kontrollera endast miljön

Om du vill kontrollera den lokala Docker-demonen, CLI-versionen och Helm-klientkonfigurationen kör du kommandot utan ytterligare parametrar:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Kontrollera miljön och ett målregister

Om du vill kontrollera åtkomsten till ett register och utföra lokala miljökontroller skickar du namnet på ett målregister. Ett exempel:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Felrapportering

Kommandot loggar information till standardutdata. Om ett problem upptäcks innehåller den en felkod och beskrivning. Mer information om koderna och möjliga lösningar finns i [felreferensen](container-registry-health-error-reference.md).

Som standard stoppas kommandot när ett fel hittas. Du kan också köra kommandot så att det ger utdata för alla hälsokontroller, även om fel hittas. Lägg `--ignore-errors` till parametern, som visas i följande exempel:

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry
az acr check-health --name myregistry --ignore-errors
```      

Exempel på utdata:

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  



## <a name="next-steps"></a>Nästa steg

Mer information om felkoder som returneras av kommandot [az acr check-health][az-acr-check-health] finns i [felreferensen för hälsokontroll](container-registry-health-error-reference.md).

Se [vanliga frågor](container-registry-faq.md) och svar om vanliga frågor och andra kända problem om Azure Container Registry.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
