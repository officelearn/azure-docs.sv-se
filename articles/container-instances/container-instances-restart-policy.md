---
title: Köra av uppgifter i Azure Container instanser
description: Lär dig hur du använder Azure Behållarinstanser för att köra aktiviteter som körs kan slutföras, som i build-, test- eller image-återgivning jobb.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 11/16/2017
ms.author: marsma
ms.openlocfilehash: 3bbe3e891423b6ad62a1d1093daef304206f3d76
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="run-a-containerized-task-in-azure-container-instances"></a>Köra en av aktivitet i Azure Container instanser

Enkel och hastighet för att distribuera behållare i Azure Container instanser är en tvingande plattform för att köra körs en gång aktiviteter som att skapa, testa och avbildningen återgivning i en behållare-instans.

Du kan ange att behållarna har stoppats när deras processer har slutförts med en omstartsprincip för konfigurerbara. Eftersom behållarinstanser debiteras med andra är du debiteras för de beräkningsresurser som används när behållaren köra uppgiften körs.

Exemplen som presenteras i den här artikeln används Azure CLI. Du måste ha Azure CLI version 2.0.21 eller större [installeras lokalt][azure-cli-install], eller använda CLI i den [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Princip för omstart av behållare

När du skapar en behållare i Azure Behållarinstanser ange du en av tre inställningar för omstart.

| Starta om principen   | Beskrivning |
| ---------------- | :---------- |
| `Always` | Behållare i behållargruppen alltid startas om. Det här är den **standard** inställningen tillämpas när ingen omstartsprincip har angetts när behållare skapas. |
| `Never` | Behållare i behållargruppen aldrig startas om. Behållarna som kör en gång. |
| `OnFailure` | Behållare i behållargruppen har startats om endast om processen körs i behållaren misslyckas (när det avslutas med slutkoden noll). Behållarna som körs minst en gång. |

## <a name="specify-a-restart-policy"></a>Ange en omstartsprincip för

Hur du anger en princip för omstart beror på hur du skapar din behållarinstanser som med Azure CLI, Azure PowerShell-cmdlets eller i Azure-portalen. I Azure CLI, ange den `--restart-policy` parameter när du anropar [az behållaren skapa][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Kör till exempel för slutförande

Om du vill se omstartspolicyn i praktiken, skapa en behållare-instans från den [aci/microsoft-wordcount] [ aci-wordcount-image] avbildning och ange den `OnFailure` starta om principen. Den här behållaren exempel kör Python-skriptet som standard, analyserar du texten i Shakespeare's [samhälle](http://shakespeare.mit.edu/hamlet/full.html), skriver du 10 vanligaste orden STDOUT och sedan avslutas.

Kör exempel behållaren med följande [az behållaren skapa] [ az-container-create] kommando:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure Behållarinstanser startar behållaren och stoppar den när dess program (eller skript i det här fallet) avslutas. När en behållare vars omstartspolicyn är slutar att Azure Behållarinstanser `Never` eller `OnFailure`, behållarens status anges till **Uppsagd**. Du kan kontrollera statusen för en behållare med den [az behållaren visa] [ az-container-show] kommando:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Exempel på utdata:

```bash
"Terminated"
```

När behållaren exempel status visar *Uppsagd*, du kan se dess uppgiftsutdata genom att visa loggar för behållaren. Kör den [az behållaren loggar] [ az-container-logs] utdata från kommandot för att visa skript:

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

Det här exemplet visas utdata som skriptet skickas till STDOUT. Av aktiviteterna, men kan i stället skriva sina utdata till beständig lagring för senare hämtning. Till exempel en [Azure-filresursen](container-instances-mounting-azure-files-volume.md).

## <a name="configure-containers-at-runtime"></a>Konfigurera behållare vid körning

När du skapar en behållare instans måste du ange dess **miljövariabler**, samt ange en anpassad **kommandoraden** ska köras när behållaren har startats. Du kan använda de här inställningarna i batch-jobb för att förbereda varje behållare med uppgiften konfiguration.

## <a name="environment-variables"></a>Miljövariabler

Ange miljövariabler i din behållare för att tillhandahålla dynamisk konfiguration av program eller skript som körs av behållaren. Detta liknar den `--env` kommandoradsargument till `docker run`.

Du kan till exempel ändra funktionssättet för skriptet i behållaren exempel genom att ange följande miljövariabler när du skapar behållaren-instans:

*NumWords*: antalet ord som skickas till STDOUT.

*MinLength*: det minsta antalet tecken i ett ord att inventeras. En hög siffra ignorerar vanliga ord som ”av” och ”i”.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Genom att ange `NumWords=5` och `MinLength=8` för behållarens miljövariabler loggar för behållaren ska visa resultatet. När behållaren status visas som *Uppsagd* (Använd `az container show` att kontrollera dess status), visa loggar om du vill se nya utdata:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Resultat:

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="command-line-override"></a>Åsidosättning av kommandoraden

Ange en kommandorad när du skapar en behållare instans att åsidosätta kommandoraden inbyggd i behållaren avbildningen. Detta liknar den `--entrypoint` kommandoradsargument till `docker run`.

Du kan exempelvis ha behållaren exempel analysera text än *samhälle* genom att ange en annan kommandorad. Python-skript som körs av behållare, *wordcount.py*accepterar en URL som ett argument och bearbetar den sidan innehåll i stället för standardvärdet.

Till exempel för att fastställa upp 3 fem bokstäver ord i *Romeo och Juliet*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer3 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Igen när behållaren har *Uppsagd*, visar utdata genom att visa behållarens loggar:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer3
```

Resultat:

```bash
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Nästa steg

### <a name="persist-task-output"></a>Spara uppgiftsutdata

Mer information om hur du bevara utdata från de behållare som att slutföras finns [montera en filresurs som Azure med Azure Container instanser](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[azure-cli-install]: /cli/azure/install-azure-cli
