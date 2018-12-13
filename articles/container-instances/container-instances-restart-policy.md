---
title: Använd omstartspolicyer med behållarbaserade uppgifter i Azure Container Instances
description: Lär dig hur du använder Azure Container Instances köra uppgifter som körs kan slutföras, till exempel i build-, test- eller image Renderingsjobb.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 12/10/2018
ms.author: danlep
ms.openlocfilehash: b254adb050aa9826170c0849c3811380db6d9b38
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321041"
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

Om du vill se omstartsprincip i praktiken skapar en behållarinstans från den [microsoft/aci-wordcount] [ aci-wordcount-image] bild och ange den `OnFailure` omstartsprincip. Den här behållaren exempel körs ett Python-skript som standard, analyserar du texten i Shakespeare's [samhälle](http://shakespeare.mit.edu/hamlet/full.html)skriver de 10 vanligaste ord till STDOUT och sedan avslutas.

Kör exemplet behållaren med följande [az container skapa] [ az-container-create] kommando:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image microsoft/aci-wordcount:latest \
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

## <a name="manually-stop-and-start-a-container-group"></a>Stoppa och starta en behållargrupp manuellt

Oavsett omstartsprincip som konfigurerats för en [behållargruppen](container-instances-container-groups.md), kanske du vill stoppa och starta en behållargrupp manuellt.

* **Stoppa** – du kan manuellt stoppa en behållargrupp som körs när som helst – till exempel med hjälp av den [az container stoppa] [ az-container-stop] kommando. För vissa arbetsbelastningar, kan du stoppa en behållargrupp när du har en definierad period att sänka kostnaderna. 

  Stoppa en behållargrupp avslutas och återanvänds behållare i gruppen. behållartillstånd bevaras inte. 

* **Starta** – när en behållargrupp har stoppats - antingen eftersom behållarna avbröts på egen hand eller manuellt stoppad gruppen – du kan använda den [behållare starta API](/rest/api/container-instances/containergroups/start) eller Azure portal för att manuellt starta behållarna i gruppen. Om behållaravbildning för alla behållare uppdateras, hämtas en ny avbildning. 

  Starta en behållargrupp börjar en ny distribution med samma behållarkonfiguration för. Med hjälp av den här åtgärden kan du snabbt återanvända en grupp kända container-konfiguration som fungerar som förväntat. Du behöver att skapa en ny behållargrupp som kör samma arbetsbelastning.

* **Starta om** – du kan starta om en behållargrupp när den körs – till exempel med hjälp av den [az container omstart] [ az-container-restart] kommando. Den här åtgärden startar om alla behållare i behållargruppen. Om behållaravbildning för alla behållare uppdateras, hämtas en ny avbildning. 

  Starta om en behållargrupp är användbart när du vill felsöka ett distributionsproblem. Till exempel om en temporär begränsningen förhindrar att dina behållare körs, kan startar om gruppen lösa problemet.

Starta om principen när du manuellt starta eller starta om en behållargrupp behållare grupp körs enligt det konfigurerade.

## <a name="configure-containers-at-runtime"></a>Konfigurera behållare vid körning

När du skapar en behållarinstans kan du ange dess **miljövariabler**, samt ange en anpassad **kommandoraden** ska köras när behållaren har startats. Du kan använda de här inställningarna i dina batch-jobb för att förbereda varje behållare med uppgiftsspecifika konfiguration.

## <a name="environment-variables"></a>Miljövariabler

Ange miljövariabler i din behållare för dynamisk konfiguration av program eller skript som körs av behållaren. Detta liknar den `--env` kommandoradsargument till `docker run`.

Du kan till exempel ändra beteendet för skriptet i behållaren exempel genom att ange följande miljövariabler när du skapar behållarinstansen:

*NumWords*: Antalet ord som skickas till STDOUT.

*MinLength*: Minsta antalet tecken i ett ord för att det ska räknas. En hög siffra ignorerar vanliga ord som ”av” och ”den”.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Genom att ange `NumWords=5` och `MinLength=8` för behållarens miljövariabler, behållarloggarna ska visa resultatet. När visas status för container *Uppsagd* (Använd `az container show` att kontrollera dess status), visa loggar för att se den nya utdatan:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Utdata:

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```



## <a name="command-line-override"></a>Åsidosättning av kommandoraden

Ange en kommandorad när du skapar en behållarinstans åsidosätta kommandoraden som är inbyggd i behållaravbildningen. Detta liknar den `--entrypoint` kommandoradsargument till `docker run`.

Du kan exempelvis ha behållaren exempel analysera text än *samhälle* genom att ange en annan kommandorad. Python-skriptet som körs av behållare, *wordcount.py*accepterar en URL som ett argument och bearbetar den sidan innehåll i stället för standardvärdet.

Till exempel för att fastställa topp 3 femsiffrig orden i *Romeo och Juliet*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer3 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Igen, när behållaren är *Uppsagd*, visa utdata genom att visa behållarens loggar:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer3
```

Utdata:

```bash
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Nästa steg

### <a name="persist-task-output"></a>Spara aktivitetsutdata

Mer information om hur du bevarar utdata för dina behållare att slutföras finns [montera en Azure-filresurs med Azure Container Instances](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
[azure-cli-install]: /cli/azure/install-azure-cli
