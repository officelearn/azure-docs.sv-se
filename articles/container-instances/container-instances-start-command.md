---
title: Åsidosätta startpunkt i behållarinstans
description: Ange att en kommandorad ska åsidosätta startpunkten i en behållaravbildning när du distribuerar en Azure-behållarinstans
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: d9554603f78a07fa44af51d8f39a91e1b3c39f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247129"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Ange att kommandoraden i en behållarförekomst ska åsidosätta standardkommandoradens åtgärd

När du skapar en behållarförekomst anger du ett kommando för att åsidosätta standardkommandoradens instruktion som bakas in i behållaravbildningen. Det här beteendet `--entrypoint` liknar kommandoradsargumentet som `docker run`.

Precis som att ange [miljövariabler](container-instances-environment-variables.md) för behållarinstanser är det användbart att ange en startkommandorad för batchjobb där du behöver förbereda varje behållare dynamiskt med uppgiftsspecifik konfiguration.

## <a name="command-line-guidelines"></a>Riktlinjer för kommandoraden

* Som standard anger kommandoraden en *enda process som startar utan ett skal* i behållaren. Kommandoraden kan till exempel köra ett Python-skript eller körbar fil. Processen kan ange ytterligare parametrar eller argument.

* Om du vill köra flera kommandon börjar du kommandoraden genom att ange en skalmiljö som stöds i behållaroperativsystemet. Exempel:

  |Operativsystem  |Standardskal  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Följ konventionerna i skalet för att kombinera flera kommandon som ska köras i följd.

* Beroende på behållarkonfigurationen kan du behöva ange en fullständig sökväg till den körbara kommandoraden eller argumenten.

* Ange en lämplig [omstartsprincip](container-instances-restart-policy.md) för behållarinstansen, beroende på om kommandoraden anger en tidskrävande aktivitet eller en körningsaktivitet. Till exempel en omstartsprincip för `Never` eller `OnFailure` rekommenderas för en körningsaktivitet. 

* Om du behöver information om standardpostpunktsuppsättningen i en behållaravbildning använder du kommandot [docker image inspect.](https://docs.docker.com/engine/reference/commandline/image_inspect/)

## <a name="command-line-syntax"></a>Syntax för kommandoraden

Syntaxen för kommandoraden varierar beroende på Azure API eller verktyg som används för att skapa instanserna. Om du anger en skalmiljö bör du också följa kommandot syntaxkonventionerna för skalet.

* [kommandot az container create:][az-container-create] Skicka `--command-line` en sträng med parametern. Exempel: `--command-line "python myscript.py arg1 arg2"`).

* [Ny-AzureRmContainerGroup][new-azurermcontainergroup] Azure PowerShell cmdlet: Skicka `-Command` en sträng med parametern. Exempel: `-Command "echo hello"`.

* Azure-portal: Ange en kommaavgränsad lista över strängar utan citattecken i egenskapen **Kommando åsidosättning** för behållarkonfigurationen. Exempel: `python, myscript.py, arg1, arg2`). 

* Resource Manager-mall eller YAML-fil, eller en av Azure SDK: Ange kommandoradsegenskapen som en matris med strängar. Exempel: JSON-matrisen `["python", "myscript.py", "arg1", "arg2"]` i en Resource Manager-mall. 

  Om du är bekant med [Dockerfile-syntaxen](https://docs.docker.com/engine/reference/builder/) liknar det här formatet den *exec-formen* för CMD-instruktionen.

### <a name="examples"></a>Exempel

|    |  Azure CLI   | Portalen | Mall | 
| ---- | ---- | --- | --- |
| Kommandot Enda | `--command-line "python myscript.py arg1 arg2"` | **Åsidosättning av kommandot:**`python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Flera kommandon | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Åsidosättning av kommandot:**`/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Azure CLI-exempel

Som ett exempel, ändra beteendet hos [microsoft/aci-wordcount][aci-wordcount] container bild, som analyserar text i Shakespeares *Hamlet* för att hitta de vanligaste orden. I stället för att analysera *Hamlet*kan du ange en kommandorad som pekar på en annan textkälla.

Om du vill visa utdata från [microsoft/aci-wordcount-behållaren][aci-wordcount] när den analyserar standardtexten kör du den med följande kommando [för az-behållarskap.][az-container-create] Ingen startkommandorad har angetts, så standardkommandot för behållare körs. I det här exemplet anger det här exemplet [miljövariabler](container-instances-environment-variables.md) för att hitta de tre översta orden som är minst fem bokstäver långa:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

När behållarens tillstånd visas som *Avslutad* (använd [az-behållaren visa][az-container-show] för att kontrollera tillståndet) visar du loggen med [az-behållarloggar][az-container-logs] för att se utdata.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Resultat:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Ställ nu in en andra exempelbehållare för att analysera annan text genom att ange en annan kommandorad. Python-skriptet som körs av *behållaren, wordcount.py*, accepterar en URL som ett argument och bearbetar sidans innehåll i stället för standard.

Till exempel, för att bestämma de 3 ord som är minst fem bokstäver lång i *Romeo och Julia:*

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Återigen, när behållaren är *Avslutad*, visa utdata genom att visa behållarens loggar:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Resultat:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Nästa steg

Aktivitetsbaserade scenarier, till exempel batchbearbetning av en stor datauppsättning med flera behållare, kan dra nytta av anpassade kommandorader vid körning. Mer information om hur du kör aktivitetsbaserade behållare finns i [Kör behållaraktiviteter med omstartsprinciper](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
