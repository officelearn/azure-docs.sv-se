---
title: Ange miljövariabler i behållarinstans
description: Lär dig hur du anger miljövariabler i behållarna som du kör i Azure Container Instances
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: c3c76ba0c6131a8ab3de68c13c9dfddaf7e8749a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247233"
---
# <a name="set-environment-variables-in-container-instances"></a>Ange miljövariabler i behållarinstanser

Genom att ställa in miljövariabler i dina containerinstanser kan du skapa en dynamisk konfiguration av programmet eller skriptet som körs av containern. Detta liknar kommandoradsargumentet `--env` som `docker run`. 

Om du vill ange miljövariabler i en behållare anger du dem när du skapar en behållarinstans. Den här artikeln visar exempel på inställningsmiljövariabler när du startar en behållare med [Azure CLI,](#azure-cli-example) [Azure PowerShell](#azure-powershell-example)och [Azure-portalen](#azure-portal-example). 

Om du till exempel kör behållaravbildningen för Microsoft [aci-wordcount][aci-wordcount] kan du ändra dess beteende genom att ange följande miljövariabler:

*NumWords*: Antalet ord som skickas till STDOUT.

*MinLength*: Det minsta antalet tecken i ett ord för att det ska räknas. Ett högre tal ignorerar vanliga ord som "av" och "the".

Om du behöver skicka hemligheter som miljövariabler stöder Azure Container Instances [säkra värden](#secure-values) för både Windows- och Linux-behållare.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Azure CLI-exempel

Om du vill visa standardutdata för [aci-wordcount-behållaren][aci-wordcount] kör du den först med det här [az-kommandot för att skapa behållare][az-container-create] (inga miljövariabler har angetts):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Om du vill ändra utdata `--environment-variables` startar du en andra behållare med argumentet tillagt och anger värden för *variablerna NumWords* och *MinLength.* (Det här exemplet förutsätter att du kör CLI i ett Bash-skal eller Azure Cloud Shell. Om du använder Kommandotolken i Windows anger du variablerna `--environment-variables "NumWords"="5" "MinLength"="8"`med dubbla citattecken, till exempel .)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

När båda behållarnas tillstånd visas som *Avslutad* (använd [az-behållaren visa][az-container-show] för att kontrollera tillståndet) visar du sina loggar med [az-behållarloggar][az-container-logs] för att se utdata.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

Utdata från behållarna visar hur du har ändrat den andra behållarens skriptbeteende genom att ange miljövariabler.

**mycontainer1**
```output
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

**mycontainer2**
```output
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Azure PowerShell-exempel

Att ange miljövariabler i PowerShell liknar `-EnvironmentVariable` CLI, men använder kommandoradsargumentet.

Starta först [behållaren aci-wordcount][aci-wordcount] i standardkonfigurationen med det här kommandot [New-AzContainerGroup:][new-Azcontainergroup]

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Kör nu följande [new-azcontainergroup-kommando.][new-Azcontainergroup] Den här anger *NumWords* och *MinLength* miljö variabler efter `envVars`att ha fyllt en matris variabel, :

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

När båda behållarnas tillstånd *har avslutats* (använd [Get-AzContainerInstanceLog][azure-instance-log] för att kontrollera tillståndet) drar du sina loggar med kommandot [Get-AzContainerInstanceLog.][azure-instance-log]

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

Utdata för varje behållare visar hur du har ändrat skriptet som körs av behållaren genom att ange miljövariabler.

```console
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
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
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Exempel på Azure-portal

Om du vill ange miljövariabler när du startar en behållare i Azure-portalen anger du dem på sidan **Avancerat** när du skapar behållaren.

1. På sidan **Avancerat** ställer du in **omstartsprincipen** *på vid fel*
2. Under **Miljövariabler**anger `NumWords` du `5` med värdet för `MinLength` den första `8` variabeln och anger med värdet för den andra variabeln. 
1. Välj **Granska + skapa** för att verifiera och distribuera sedan behållaren.

![Portalsida som visar miljövariabel Knappen Aktivera och textrutor][portal-env-vars-01]

Om du vill visa behållarens loggar väljer du **Behållare**under **Inställningar** och sedan **Loggar**. I likhet med utdata som visas i föregående CLI- och PowerShell-avsnitt kan du se hur skriptets beteende har ändrats av miljövariablerna. Endast fem ord visas, var och en med en minsta längd på åtta tecken.

![Portal som visar utdata för behållarlogg][portal-env-vars-02]

## <a name="secure-values"></a>Säkra värden

Objekt med säkra värden är avsedda att innehålla känslig information som lösenord eller nycklar för ditt program. Att använda säkra värden för miljövariabler är både säkrare och mer flexibelt än att inkludera dem i behållarens avbildning. Ett annat alternativ är att använda hemliga volymer som beskrivs i [Montera en hemlig volym i Azure Container Instances](container-instances-volume-secret.md).

Miljövariabler med säkra värden visas inte i behållarens egenskaper – deras värden kan endast nås inifrån behållaren. Till exempel visar behållaregenskaper som visas i Azure-portalen eller Azure CLI bara en säker variabels namn, inte dess värde.

Ange en säker miljövariabel `secureValue` genom att `value` ange egenskapen i stället för den vanliga för variabelns typ. De två variabler som definierats i följande YAML visar de två variabla typerna.

### <a name="yaml-deployment"></a>YAML-distribution

Skapa `secure-env.yaml` en fil med följande utdrag.

```yaml
apiVersion: 2018-10-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Kör följande kommando för att distribuera behållargruppen med YAML (justera resursgruppsnamnet efter behov):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Verifiera miljövariabler

Kör kommandot [az container show][az-container-show] för att fråga efter behållarens miljövariabler:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

JSON-svaret visar både den osäkra miljövariabelns nyckel och värde, men bara namnet på variabeln säker miljö:

```json
[
  [
    {
      "name": "NOTSECRET",
      "secureValue": null,
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET",
      "secureValue": null,
      "value": null
    }
  ]
]
```

Med kommandot [az container exec,][az-container-exec] som gör det möjligt att köra ett kommando i en behållare som körs, kan du kontrollera att variabeln säker miljö har angetts. Kör följande kommando för att starta en interaktiv bash-session i behållaren:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

När du har öppnat ett interaktivt skal i `SECRET` behållaren kan du komma åt variabelns värde:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Nästa steg

Aktivitetsbaserade scenarier, till exempel batchbearbetning av en stor datauppsättning med flera behållare, kan dra nytta av anpassade miljövariabler vid körning. Mer information om hur du kör aktivitetsbaserade behållare finns i [Kör behållaraktiviteter med omstartsprinciper](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
