---
title: Använda en från kommandoraden i Azure Container Instances
description: Åsidosätt entrypoint som konfigurerats i en behållaravbildning när du distribuerar ett Azure container-instans
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: da94a4c79694f511d41e5c8dda8c786fc7049726
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64569649"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Ange kommandoraden i en behållarinstans åsidosätta standardåtgärd för kommandoraden

När du skapar en behållarinstans kan du ange ett kommando för att åsidosätta standard kommandoraden instruktionen inbyggd i behållaravbildningen. Detta liknar den `--entrypoint` kommandoradsargument till `docker run`.

Som inställningen [miljövariabler](container-instances-environment-variables.md) för container instances, att ange en från kommandoraden är användbart för batch-jobb där måste du förbereda varje behållare dynamiskt med uppgiftsspecifika konfiguration.

## <a name="command-line-guidelines"></a>Riktlinjer för kommandoraden

* Som standard kommandoraden anger en *enkel process som startar utan ett gränssnitt* i behållaren. Från kommandoraden kan till exempel köra en Python-skriptet eller den körbara filen. 

* Börja från kommandoraden genom att ange en shell-miljö som stöds i operativsystemet behållare för att köra flera kommandon. Exempel:

  |Operativsystem  |Standardgränssnitt  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Följ konventioner för gränssnittet för att kombinera flera kommandon som ska köras i följd.

* Beroende på konfigurationen av behållaren kan du behöva ange en fullständig sökväg till kommandoraden körbara eller argument.

* Ange ett lämpligt [omstartsprincip](container-instances-restart-policy.md) för behållarinstans, beroende på om kommandoraden anger en tidskrävande uppgift eller en uppgift körs en gång. Exempelvis kan en princip för omstart av `Never` eller `OnFailure` rekommenderas för en aktivitet körs en gång. 

* Om du behöver information om standard-startpunkt som angetts i en behållaravbildning kan använda den [docker-avbildning inspektera](https://docs.docker.com/engine/reference/commandline/image_inspect/) kommando.

## <a name="command-line-syntax"></a>Kommandoradssyntax

Kommandoradssyntaxen varierar beroende på Azure-API eller verktyg som används för att skapa instanser. Om du anger en shell-miljö kan du också se kommando syntax konventioner för gränssnittet.

* [Skapa AZ container] [ az-container-create] kommando: Skicka en sträng med den `--command-line` parametern. Exempel: `--command-line "python myscript.py arg1 arg2"`).

* [Ny-AzureRmContainerGroup] [ new-azurermcontainergroup] Azure PowerShell-cmdlet: Skicka en sträng med den `-Command` parametern. Exempel: `-Command "echo hello"`.

* Azure-portalen: I den **kommandot åsidosättning** egenskapen för konfigurationen av behållaren, ange en kommaavgränsad lista med strängar, utan citattecken. Exempel: `python, myscript.py, arg1, arg2`). 

* Resource Manager-mall eller YAML-fil eller en av Azure-SDK: er: Ange egenskapen kommandoraden som en matris med strängar. Exempel: i JSON-matris `["python", "myscript.py", "arg1", "arg2"]` i en Resource Manager-mall. 

  Om du är bekant med [Dockerfile](https://docs.docker.com/engine/reference/builder/) syntax, det här formatet är liknar den *exec* CMD-instruktion form.

### <a name="examples"></a>Exempel

|    |  Azure CLI   | Portalen | Mall | 
| ---- | ---- | --- | --- |
| Enda kommando | `--command-line "python myscript.py arg1 arg2"` | **Kommandot åsidosättning**: `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Flera kommandon | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Kommandot åsidosättning**: `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Exempel på Azure CLI

Till exempel ändra funktionssättet för den [microsoft/aci-wordcount] [ aci-wordcount] behållaravbildning som analyserar text i Shakespeare's *samhälle* att hitta de mest inträffar orden. I stället för att analysera *samhälle*, du kan ange en kommandorad som pekar på en annan text-källa.

Att se utdata från den [microsoft/aci-wordcount] [ aci-wordcount] behållaren när funktionen analyserar standardtexten, kör den med följande [az container skapa] [ az-container-create] kommando. Ingen start-kommandorad har angetts, så att kommandot behållaren körs. För tydlighetens skull det här exemplet anger [miljövariabler](container-instances-environment-variables.md) att hitta topp 3-ord som är minst fem bokstäver som är långt:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

När behållarens status visas som *Uppsagd* (Använd [az container show] [ az-container-show] att kontrollera tillstånd), visa loggen med [az behållarloggarna] [ az-container-logs] att se utdata.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Utdata:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Konfigurera nu en andra exempel behållare för att analysera olika text genom att ange en annan kommandorad. Python-skriptet som körs av behållare, *wordcount.py*accepterar en URL som ett argument och bearbetar den sidan innehållet i stället för standardvärdet.

Till exempel för att fastställa upp 3 ord som är minst fem bokstäverna länge i *Romeo och Juliet*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Igen, när behållaren är *Uppsagd*, visa utdata genom att visa behållarens loggar:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Utdata:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Nästa steg

Uppgiftsbaserade scenarier, till exempel en stor datauppsättning med flera behållare för batchbearbetning kan dra nytta av anpassade kommandorader vid körning. Mer information om hur du kör uppgiftsbaserade behållare finns i [köra behållarbaserade uppgifter med principer för omstart](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
