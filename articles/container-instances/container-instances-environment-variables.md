---
title: Ange miljövariabler i Azure Container instanser
description: Lär dig hur du kan ange miljövariabler i behållare som körs i Azure Container instanser
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 05/16/2018
ms.author: marsma
ms.openlocfilehash: 1a025ce647cb3c071a6549a433e6505b85409fdc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="set-environment-variables"></a>Ange miljövariabler

Ange miljövariabler i behållarinstanser kan du tillhandahålla dynamisk konfiguration av program eller skript som körs av behållaren. Miljövariabler i en behållare, ange dem när du skapar en instans i behållaren. Du kan ange miljövariabler när du startar en behållare med den [Azure CLI](#azure-cli-example), [Azure PowerShell](#azure-powershell-example), och [Azure-portalen](#azure-portal-example).

Om du kör till exempel den [aci/microsoft-wordcount] [ aci-wordcount] behållare bilden, du kan ändra sitt beteende genom att ange följande miljövariabler:

*NumWords*: antalet ord som skickas till STDOUT.

*MinLength*: det minsta antalet tecken i ett ord att inventeras. En hög siffra ignorerar vanliga ord som ”av” och ”i”.

## <a name="azure-cli-example"></a>Azure CLI-exempel

Se standardutdata från den [aci/microsoft-wordcount] [ aci-wordcount] behållare, kör först med den här [az behållaren skapa] [ az-container-create] kommando (Nej miljövariablerna anges):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Om du vill ändra utdata, starta en andra behållare med den `--environment-variables` argumentet som har lagts till, värden anges för den *NumWords* och *MinLength* variabler:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

När båda behållare status visas som *Uppsagd* (använda [az behållaren visa] [ az-container-show] att kontrollera tillstånd), visar sina loggar med [az behållaren loggar] [ az-container-logs] att se utdata.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

Utdata från behållarna som visar hur du har ändrat behållaren andra skript beteende genom att ange miljövariabler.

```console
azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer1
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

azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Azure PowerShell-exempel

Ange miljövariabler i PowerShell liknar CLI men använder den `-EnvironmentVariable` kommandoradsargument.

Starta först, den [aci/microsoft-wordcount] [ aci-wordcount] behållare i standardkonfigurationen med den här [ny AzureRmContainerGroup] [ new-azurermcontainergroup]kommando:

```azurepowershell-interactive
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image microsoft/aci-wordcount:latest
```

Kör nu följande [ny AzureRmContainerGroup] [ new-azurermcontainergroup] kommando. Den här anger den *NumWords* och *MinLength* miljövariabler efter fylla en matrisvariabel `envVars`:

```azurepowershell-interactive
$envVars = @{NumWords=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

När båda behållare tillstånd är *Uppsagd* (använda [Get-AzureRmContainerInstanceLog] [ azure-instance-log] att kontrollera tillstånd), hämtar sina loggar med den [ Get-AzureRmContainerInstanceLog] [ azure-instance-log] kommando.

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

Utdata för varje behållare visar hur du har ändrat skriptet körs från behållaren genom att ange miljövariabler.

```console
PS Azure:\> Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
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

Azure:\
PS Azure:\> Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Azure portal exempel

Om du vill ange miljövariabler när du startar en behållare i Azure-portalen, kan du ange dem i den **Configuration** när du skapar behållaren.

När du distribuerar med portalen kan du är för tillfället begränsad till tre variabler och du måste ange dem i det här formatet: `"variableName":"value"`

Om du vill se ett exempel kan du starta den [aci/microsoft-wordcount] [ aci-wordcount] behållare med den *NumWords* och *MinLength* variabler.

1. I **Configuration**, ange den **starta om principen** till *vid fel*
2. Ange `"NumWords":"5"` för den första variabeln, väljer **Ja** under **lägga till ytterligare miljövariabler**, och ange `"MinLength":"8"` för den andra variabeln. Välj **OK** att kontrollera och sedan distribuera behållaren.

![Portalsida visar miljö variabeln aktivera knappen och textrutor][portal-env-vars-01]

Visa behållarens loggar under **inställningar** Välj **behållare**, sedan **loggar**. Liknar utdata som visas i föregående CLI och PowerShell avsnitt ser du hur den skriptfunktion har ändrats av miljövariablerna. Endast fem orden visas med en minsta längd på åtta tecken.

![Portalen visar behållaren loggutdata][portal-env-vars-02]

## <a name="next-steps"></a>Nästa steg

Uppgiftsbaserade scenarier, till exempel en stor datamängd med flera behållare för batchbearbetning kan dra nytta av anpassade miljövariabler vid körning. Mer information om hur du kör uppgiftsbaserade behållare finns [köra av uppgifter i Azure Behållarinstanser](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com