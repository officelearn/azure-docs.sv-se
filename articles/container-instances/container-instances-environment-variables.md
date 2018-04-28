---
title: Ange miljövariabler i Azure Container instanser
description: Lär dig hur du anger miljövariabler i Azure Container instanser
services: container-instances
author: david-stanford
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/13/2018
ms.author: dastanfo
ms.openlocfilehash: 37fde41b6dc2ea0a4d3b4b38a0e3df81a297c125
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="set-environment-variables"></a>Ange miljövariabler

Ange miljövariabler i behållarinstanser kan du tillhandahålla dynamisk konfiguration av program eller skript som körs av behållaren.

Du är för närvarande kan ange miljövariabler från CLI och PowerShell. I båda fallen använder du flaggan kommandon för att ställa in miljövariabler. Ange miljövariabler i ACI liknar den `--env` kommandoradsargument till `docker run`. Om du använder microsoft/aci-wordcount behållare bild ändra du beteendet genom att ange följande miljövariabler:

*NumWords*: antalet ord som skickas till STDOUT.

*MinLength*: det minsta antalet tecken i ett ord att inventeras. En hög siffra ignorerar vanliga ord som ”av” och ”i”.

## <a name="azure-cli-example"></a>Azure CLI-exempel

Om du vill se standard utdata från behållaren för att köra följande kommando:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Genom att ange `NumWords=5` och `MinLength=8` för behållarens miljövariabler loggar för behållaren ska visa resultatet.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

När behållaren status visas som *Uppsagd* (använda [az behållaren visa] [ az-container-show] att kontrollera dess status), visa loggar för att se utdata.  För att visa resultatet av behållare med ingen miljö variabler har angetts--namnet ska vara mycontainer1 i stället för mycontainer2.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

## <a name="azure-powershell-example"></a>Azure PowerShell-exempel

Om du vill se standard utdata från behållaren för att köra följande kommando:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Genom att ange `NumWords=5` och `MinLength=8` för behållarens miljövariabler loggar för behållaren ska visa resultatet.

```azurepowershell-interactive
$envVars = @{NumWord=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

När behållaren status är *Uppsagd* (använda [Get-AzureRmContainerInstanceLog] [ azure-instance-log] att kontrollera dess status), visa loggar för att se utdata. Om du vill visa behållaren ange loggar med ingen miljövariabler ContainerGroupName ska mycontainer1 i stället för mycontainer2.

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog `
    -ResourceGroupName myResourceGroup `
    -ContainerGroupName mycontainer2
```

## <a name="example-output-without-environment-variables"></a>Exempel på utdata utan miljövariabler

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

## <a name="example-output-with-environment-variables"></a>Exempel på utdata med miljövariabler

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du anpassar indata till din behållaren lär du dig hur du bevara utdata från behållare som att slutföras.
> [!div class="nextstepaction"]
> [Montera en filresurs som Azure med Azure Container instanser](container-instances-mounting-azure-files-volume.md)

<!-- LINKS Internal -->
[azure-cloud-shell]: ../cloud-shell/overview.md
[azure-cli-install]: /cli/azure/
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show