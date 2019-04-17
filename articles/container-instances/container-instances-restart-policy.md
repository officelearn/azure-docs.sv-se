---
title: Använd omstartspolicyer med behållarbaserade uppgifter i Azure Container Instances
description: Lär dig hur du använder Azure Container Instances köra uppgifter som körs kan slutföras, till exempel i build-, test- eller image Renderingsjobb.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 06872eefd0d500a22214109ad5055dd236b5a6ac
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59606845"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Köra behållarbaserade uppgifter med principer för omstart

Enkelt och snabbt distribuera behållare i Azure Container Instances erbjuder en intressant plattform för att köra uppgifter, t.ex. att skapa, testa och bildrendering körs en gång i en behållarinstans.

Du kan ange att dina behållare stoppas när sina processer har slutförts med en omstartsprincip för konfigurerbara. Eftersom container instances faktureras per sekund, debiteras du endast för de beräkningsresurser som används när den behållare som kör uppgiften körs.

I exemplen som visas i den här artikeln används Azure CLI. Du måste ha Azure CLI version 2.0.21 eller senare [installerat lokalt][azure-cli-install], eller använda CLI i den [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Omstartsprincipen för behållaren

När du skapar en [behållargruppen](container-instances-container-groups.md) i Azure Container Instances kan du ange en av tre principinställningar för omstart.

| Starta om princip   | Beskrivning |
| ---------------- | :---------- |
| `Always` | Behållare i behållargruppen alltid startas om. Det här är den **standard** används när inga omstartsprincip anges när du skapar behållaren. |
| `Never` | Behållare i behållargruppen aldrig startas om. Behållarna som kör högst en gång. |
| `OnFailure` | Behållare i behållargruppen startas om endast när processen som körs i behållaren inte (när det avslutas med en slutkod). Behållarna som körs minst en gång. |

## <a name="specify-a-restart-policy"></a>Ange en omstartsprincip

Hur du anger en omstartsprincip beror på hur du skapar dina behållarinstanser som med Azure CLI, Azure PowerShell-cmdlets eller i Azure-portalen. I Azure CLI, anger du den `--restart-policy` parameter när du anropar [az container skapa][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Kör till exempel för slutförande

Om du vill se omstartsprincip i praktiken skapar en behållarinstans från Microsofts [aci wordcount] [ aci-wordcount-image] bild och ange den `OnFailure` omstartsprincip. Den här behållaren exempel körs ett Python-skript som standard, analyserar du texten i Shakespeare's [samhälle](http://shakespeare.mit.edu/hamlet/full.html)skriver de 10 vanligaste ord till STDOUT och sedan avslutas.

Kör exemplet behållaren med följande [az container skapa] [ az-container-create] kommando:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure Container Instances startar behållaren och stoppar den när dess program (eller skript i det här fallet) avslutas. När Azure Container Instances stoppar en behållare som vars omstartsprincip är `Never` eller `OnFailure`, behållarens status anges **Uppsagd**. Du kan kontrollera statusen för en behållare med den [az container show] [ az-container-show] kommando:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Exempel på utdata:

```bash
"Terminated"
```

När statusen för behållaren exempel *Uppsagd*, du kan se dess uppgiftsutdata genom att granska behållarloggarna. Kör den [az behållarloggarna] [ az-container-logs] utdata från kommandot för att visa skript:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Utdata:

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

Det här exemplet visar utdata som skriptet skickas till STDOUT. Dina behållarbaserade uppgifter, men kan i stället skriva sina utdata till beständig lagring för senare hämtning. Till exempel till ett [Azure-filresurs](container-instances-mounting-azure-files-volume.md).

## <a name="next-steps"></a>Nästa steg

Uppgiftsbaserade scenarier, till exempel en stor datauppsättning med flera behållare för batchbearbetning kan dra nytta av anpassade [miljövariabler](container-instances-environment-variables.md) eller [kommandorader](container-instances-start-command.md) vid körning.

Mer information om hur du bevarar utdata för dina behållare att slutföras finns [montera en Azure-filresurs med Azure Container Instances](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
