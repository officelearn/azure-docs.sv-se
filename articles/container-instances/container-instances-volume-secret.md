---
title: Montera hemlig volym till behållargrupp
description: Lär dig hur du monterar en hemlig volym för att lagra känslig information för åtkomst av dina behållarinstanser
ms.topic: article
ms.date: 04/03/2020
ms.openlocfilehash: 756828e71174246450245938595c8872afc62961
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657157"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Montera en hemlig volym i Azure Container Instances

Använd en *hemlig* volym för att ange känslig information till behållarna i en behållargrupp. Den *hemliga* volymen lagrar dina hemligheter i filer i volymen, som är tillgängliga för behållarna i behållargruppen. Genom att lagra hemligheter i en *hemlig* volym kan du undvika att lägga till känsliga data som SSH-nycklar eller databasreferenser i programkoden.

* När en hemliga volym har distribuerats med hemligheter i en behållargrupp är *den skrivskyddade*.
* Alla hemliga volymer backas upp av [tmpfs][tmpfs], ett RAM-stödda filsystem. deras innehåll skrivs aldrig till icke-flyktig lagring.

> [!NOTE]
> *Hemliga* volymer är för närvarande begränsade till Linux-behållare. Lär dig hur du skickar säkra miljövariabler för både Windows- och Linux-behållare i [Ange miljövariabler](container-instances-environment-variables.md). Medan vi arbetar med att få alla funktioner till Windows-behållare kan du hitta aktuella plattformsskillnader i [översikten](container-instances-overview.md#linux-and-windows-containers).

## <a name="mount-secret-volume---azure-cli"></a>Montera hemlig volym - Azure CLI

Om du vill distribuera en behållare med en eller `--secrets` `--secrets-mount-path` flera hemligheter med hjälp av Azure CLI inkluderar du och parametrarna i kommandot [az container create.][az-container-create] I det här exemplet monteras en *hemlig* volym bestående av två filer som innehåller `/mnt/secrets`hemligheter, "mysecret1" och "mysecret2", på :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

Följande [az container exec][az-container-exec] utdata visar att öppna ett skal i den löpande behållaren, lista filerna inom den hemliga volymen och sedan visa deras innehåll:

```azurecli
az container exec \
  --resource-group myResourceGroup \
  --name secret-volume-demo --exec-command "/bin/sh"
```

```output
/usr/src/app # ls /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>Montera hemlig volym - YAML

Du kan också distribuera behållargrupper med Azure CLI och en [YAML-mall](container-instances-multi-container-yaml.md). Distribution av YAML-mall är den metod som föredras när du distribuerar behållargrupper som består av flera behållare.

När du distribuerar med en YAML-mall måste de hemliga värdena vara **Base64-kodade** i mallen. De hemliga värdena visas dock i klartext i filerna i behållaren.

Följande YAML-mall definierar en behållargrupp med *secret* en `/mnt/secrets`behållare som monterar en hemlig volym på . Den hemliga volymen har två filer som innehåller hemligheter, "mysecret1" och "mysecret2."

```yaml
apiVersion: '2018-10-01'
location: eastus
name: secret-volume-demo
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /mnt/secrets
        name: secretvolume1
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: secretvolume1
    secret:
      mysecret1: TXkgZmlyc3Qgc2VjcmV0IEZPTwo=
      mysecret2: TXkgc2Vjb25kIHNlY3JldCBCQVIK
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Om du vill distribuera med YAML-mallen sparar `deploy-aci.yaml`du föregående YAML i en `--file` fil med namnet och kör sedan kommandot az container [create][az-container-create] med parametern:

```azurecli-interactive
# Deploy with YAML template
az container create \
  --resource-group myResourceGroup \
  --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Montera hemlig volym - Resource Manager

Förutom CLI- och YAML-distribution kan du distribuera en behållargrupp med hjälp av en Azure [Resource Manager-mall](/azure/templates/microsoft.containerinstance/containergroups).

Fyll först i `volumes` matrisen i `properties` behållargruppen i mallen. När du distribuerar med en Resource Manager-mall måste de hemliga värdena vara **Base64-kodade** i mallen. De hemliga värdena visas dock i klartext i filerna i behållaren.

För varje behållare i behållargruppen där du vill montera den *hemliga* volymen `properties` fyller du i matrisen `volumeMounts` i avsnittet i behållardefinitionen.

Följande Resource Manager-mall definierar en behållargrupp *secret* med en `/mnt/secrets`behållare som monterar en hemlig volym på . Den hemliga volymen har två hemligheter, "mysecret1" och "mysecret2."

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Om du vill distribuera med Resource Manager-mallen sparar `deploy-aci.json`du föregående JSON i en `--template-file` fil med namnet och kör sedan kommandot az deployment group [create][az-deployment-group-create] med parametern:

```azurecli-interactive
# Deploy with Resource Manager template
az deployment group create \
  --resource-group myResourceGroup \
  --template-file deploy-aci.json
```

## <a name="next-steps"></a>Nästa steg

### <a name="volumes"></a>Volymer

Lär dig hur du monterar andra volymtyper i Azure Container Instances:

* [Montera en Azure-filresurs i Azure Container Instances](container-instances-volume-azure-files.md)
* [Montera en emptyDir-volym i Azure Container-instanser](container-instances-volume-emptydir.md)
* [Montera en gitRepo-volym i Azure Container-instanser](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Säkra miljövariabler

En annan metod för att tillhandahålla känslig information till behållare (inklusive Windows-behållare) är att använda [säkra miljövariabler](container-instances-environment-variables.md#secure-values).

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
