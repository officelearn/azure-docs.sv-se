---
title: Starta om princip för körnings aktiviteter
description: Lär dig hur du använder Azure Container Instances för att köra aktiviteter som körs till slut för ande, som i bygg-, test-eller bild åter givnings jobb.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: f814b1c99827c07f8dadfb0cfd80c87a93377cdc
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533463"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Köra uppgifter i behållare med principer för omstart

Att distribuera behållare i Azure Container Instances är en övertygande plattform för att köra kör en gång-aktiviteter som build, test och bild åter givning i en behållar instans.

Med en konfigurerbar restart-princip kan du ange att dina behållare stoppas när deras processer har slutförts. Eftersom behållar instanser debiteras per sekund debiteras du bara för de beräknings resurser som används när behållaren som kör din aktivitet körs.

I exemplen som presenteras i den här artikeln används Azure CLI. Du måste ha Azure CLI-version 2.0.21 eller senare [installerad][azure-cli-install]eller använda CLI i [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Princip för omstart av behållare

När du skapar en [behållar grupp](container-instances-container-groups.md) i Azure Container instances kan du ange en av tre princip inställningar för omstart.

| Starta om princip   | Beskrivning |
| ---------------- | :---------- |
| `Always` | Behållare i behållar gruppen startas alltid om. Detta är **standardinställningen** som tillämpas när ingen princip för omstart har angetts vid skapande av behållare. |
| `Never` | Behållare i behållar gruppen startas aldrig om. Behållarna körs högst en gång. |
| `OnFailure` | Behållare i behållar gruppen startas om endast när processen som körs i behållaren Miss lyckas (om den avslutas med en slutkod som inte är noll). Behållarna körs minst en gång. |

## <a name="specify-a-restart-policy"></a>Ange en princip för omstart

Hur du anger en princip för omstart beror på hur du skapar dina behållar instanser, till exempel med Azure CLI, Azure PowerShell-cmdlets eller i Azure Portal. I Azure CLI anger du parametern `--restart-policy` när du anropar [AZ container Create][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Exempel på Kör till slut för ande

Om du vill se principen starta om i praktiken skapar du en behållar instans från Microsoft [ACI-WORDCOUNT-][aci-wordcount-image] avbildningen och anger `OnFailure` starta om principen. Den här exempel behållaren kör ett Python-skript som, som standard, analyserar texten i Shakespeare- [Hamlet](http://shakespeare.mit.edu/hamlet/full.html), skriver de 10 vanligaste orden till stdout och sedan avslutar.

Kör exempel behållaren med följande [AZ container Create][az-container-create] -kommando:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure Container Instances startar behållaren och stoppar den sedan när dess program (eller skript, i det här fallet) avslutas. När Azure Container Instances stoppar en behållare vars omstarts princip är `Never` eller `OnFailure`anges behållarens status som **avslutad**. Du kan kontrol lera statusen för en behållare med kommandot [AZ container show][az-container-show] :

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Exempel på utdata:

```bash
"Terminated"
```

När exempel behållarens status visas som *avslutad*kan du se dess Uppgiftsutdata genom att Visa behållar loggarna. Kör kommandot [AZ container logs][az-container-logs] för att Visa skriptets utdata:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Resultat:

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

I det här exemplet visas utdata som skriptet skickat till STDOUT. Dina uppgifter i behållare kan dock i stället skriva sina utdata till beständig lagring för senare hämtning. Till exempel till en [Azure-filresurs](container-instances-mounting-azure-files-volume.md).

## <a name="next-steps"></a>Nästa steg

Uppgiftsbaserade scenarier, t. ex. batchbearbetning av en stor data uppsättning med flera behållare, kan dra nytta av anpassade [miljövariabler](container-instances-environment-variables.md) eller [kommando rader](container-instances-start-command.md) vid körning.

Mer information om hur du behåller utdata från dina behållare som körs för slut för ande finns i [montera en Azure-filresurs med Azure Container instances](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
