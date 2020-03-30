---
title: Starta om princip för körning en gång
description: Lär dig hur du använder Azure Container Instances för att köra uppgifter som körs till slutförande, till exempel i bygg-, test- eller avbildningsrenderingsjobb.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: 8ef4ef228038242f53abc8041470f7f596ab1157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131498"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Kör behållaraktiviteter med omstartsprinciper

Eftersom det går snabbt att distribuera containrar i Azure Container Instances är det en bra plattform för att köra engångsuppgifter som att skapa, testa och återge avbildningar i en containerinstans.

Med en konfigurerbar omstartsprincip kan du ange att dina containrar ska stoppas när deras processer har slutförts. Eftersom du faktureras per sekund för containerinstanser debiteras du endast för de beräkningsresurser som används när containern kör dina uppgifter.

De exempel som presenteras i den här artikeln använder Azure CLI. Du måste ha Azure CLI version 2.0.21 eller senare [installerat lokalt][azure-cli-install]eller använda CLI i [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Princip för omstart av behållare

När du skapar en [behållargrupp](container-instances-container-groups.md) i Azure Container Instances kan du ange en av tre principinställningar för omstart.

| Omstartsprincip   | Beskrivning |
| ---------------- | :---------- |
| `Always` | Containrar i containergruppen startas alltid om. Det här är **standardvärdet** som används om du inte anger någon omstartsprincip när du skapar containern. |
| `Never` | Containrar i containergruppen startas aldrig om. Containers körs högst en gång. |
| `OnFailure` | Containrar i containergruppen startas bara om när processen som körs i containern inte slutförs utan fel (när den avslutas med en annan slutkod än noll). Containers körs minst en gång. |

## <a name="specify-a-restart-policy"></a>Ange en omstartsprincip

Hur du anger en omstartsprincip beror på hur du skapar behållarinstanserna, till exempel med Azure CLI, Azure PowerShell-cmdlets eller i Azure-portalen. I Azure CLI anger `--restart-policy` du parametern när du [anropar az-behållarskap][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Exempel på körning till slutförande

Om du vill se omstartsprincipen i praktiken skapar du en behållarinstans från Microsoft [aci-wordcount-avbildningen][aci-wordcount-image] `OnFailure` och anger omstartsprincipen. Den här exempelbehållaren kör ett Python-skript som som standard analyserar texten i Shakespeares [Hamlet](http://shakespeare.mit.edu/hamlet/full.html), skriver de 10 vanligaste orden till STDOUT och sedan avslutas.

Kör exempelbehållaren med följande kommando [för az-behållare:][az-container-create]

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure Container Instances startar containern och stoppar den när appen (eller skriptet i det här fallet) avslutas. När Azure Container Instances stoppar en `Never` `OnFailure`behållare vars omstartsprincip är eller är behållarens status inställd på **Avslutad**. Du kan kontrollera en behållares status med kommandot [az container show:][az-container-show]

```azurecli-interactive
az container show \
    --resource-group myResourceGroup \
    --name mycontainer \
    --query containers[0].instanceView.currentState.state
```

Exempel på utdata:

```bash
"Terminated"
```

När exempelcontainerns status blir *Avslutad* kan du se utdata för uppgiften i containerloggarna. Kör kommandot [az container logs][az-container-logs] för att visa skriptets utdata:

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

I det här exemplet visas utdata som skriptet skickade till STDOUT. Dina behållaruppgifter kan dock i stället skriva utdata till beständig lagring för senare hämtning. Till exempel till en [Azure-filresurs](container-instances-mounting-azure-files-volume.md).

## <a name="next-steps"></a>Nästa steg

Aktivitetsbaserade scenarier, till exempel batchbearbetning av en stor datauppsättning med flera behållare, kan dra nytta av anpassade [miljövariabler](container-instances-environment-variables.md) eller [kommandorader](container-instances-start-command.md) vid körning.

Mer information om hur du behåller utdata för dina behållare som körs till slutförande finns i [Montera en Azure-filresurs med Azure Container Instances](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
