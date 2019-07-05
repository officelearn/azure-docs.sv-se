---
title: Hälsostatus för registret i Azure Container Registry
description: Lär dig hur du kör en snabb diagnostiska kommando för att identifiera vanliga problem när du använder en Azure container registry, inklusive lokala Docker-konfiguration och anslutning till registret
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: 3e5b5467f9fa25e23f6661c6630d346aa85e2205
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555103"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Kontrollera hälsotillståndet för ett Azure container registry

När du använder en Azure container registry, kan det ibland uppstå problem. Du kan till exempel inte att hämta en behållaravbildning på grund av ett problem med Docker i din lokala miljö. Eller ett nätverksproblem hindrar dig från att ansluta till registret. 

Som ett första steg för diagnostik, kör den [az acr-hälsostatus][az-acr-check-health] command to get information about the health of the environment and optionally access to a target registry. This command is available in Azure CLI version 2.0.67 or later. If you need to install or upgrade, see [Install Azure CLI][azure-cli].

## <a name="run-az-acr-check-health"></a>Kör az acr check-health

I följande exempel visar olika sätt att köra den `az acr check-health` kommando.

> [!NOTE]
> Om du kör kommandot i Azure Cloud Shell, kontrolleras inte den lokala miljön. Du kan dock kontrollera åtkomst till ett mål-register.

### <a name="check-the-environment-only"></a>Kontrollera miljön endast

Om du vill kontrollera den lokala Docker kör-daemon och CLI-version Helm-klientkonfiguration, du kommandot utan ytterligare parametrar:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Kontrollera miljön och ett mål-register

Skicka namnet på ett mål-register för att kontrollera åtkomst till ett register samt utföra lokal Miljökontroller. Exempel:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Felrapportering

Kommandot loggar information till standardutdata. Om ett problem upptäcks, ger det en felkod och felbeskrivning. Mer information om alla koder och möjliga lösningar finns i den [felreferens](container-registry-health-error-reference.md).

Som standard avbryts kommandot snart det finns ett fel. Du kan också köra kommandot så att den ger utdata alla hälsokontroller, även om fel hittas. Lägg till den `--ignore-errors` parametern, som visas i följande exempel:

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

Mer information om felkoder som returneras av den [az acr-hälsostatus][az-acr-check-health] kommandot, se den [hälsotillstånd Kontrollera felreferens](container-registry-health-error-reference.md).

Se den [vanliga frågor och svar](container-registry-faq.md) för vanliga frågor och svar och andra kända problem med Azure Container Registry.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
