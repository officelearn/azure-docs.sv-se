---
title: Montera en hemlig volym i Azure Container Instances
description: Lär dig hur du monterar en hemlig volym för att lagra känslig information för åtkomst genom att dina behållarinstanser
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 07/19/2018
ms.author: danlep
ms.openlocfilehash: 3c1c83bb0c3e46a7eaab519050d9c556e2cc1a7a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563094"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Montera en hemlig volym i Azure Container Instances

Använd en *hemlighet* volymen för att ange känslig information till behållarna i en behållargrupp. Den *hemlighet* volym lagrar dina hemligheter i filer på volymen, som är tillgängliga för behållare i behållargruppen. Genom att lagra hemligheter i ett *hemlighet* volym, kan du undvika känsliga data som SSH-nycklar eller Databasautentiseringsuppgifter läggs till i din programkod.

Alla *hemlighet* volymer backas upp av [tmpfs][tmpfs], ett RAM-baserade filsystem; deras innehåll skrivs aldrig till beständig lagring.

> [!NOTE]
> *Hemlighet* volymerna är för närvarande begränsade till Linux-behållare. Lär dig hur du skickar säker miljövariabler för både Windows och Linux-behållare i [miljövariabler](container-instances-environment-variables.md). Medan vi arbetar för att göra alla funktioner till Windows-behållare, du kan hitta nuvarande skillnaderna i [kvoter och regiontillgänglighet för Azure Container Instances](container-instances-quotas.md).

## <a name="mount-secret-volume---azure-cli"></a>Montera hemlighetsvolymen – Azure CLI

Om du vill distribuera en behållare med en eller flera hemligheter med hjälp av Azure CLI, innehåller den `--secrets` och `--secrets-mount-path` parametrar i den [az container skapa] [ az-container-create] kommando. Det här exemplet monterar en *hemlighet* volym som består av två hemligheter, ”mysecret1” och ”mysecret2” på `/mnt/secrets`:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

Följande [az container exec] [ az-container-exec] utdata visar att öppna ett gränssnitt i behållaren som körs, lista över filer i den hemliga volymen och sedan visa deras innehåll:

```console
$ az container exec --resource-group myResourceGroup --name secret-volume-demo --exec-command "/bin/sh"
/usr/src/app # ls -1 /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>Montera hemlighetsvolymen - YAML

Du kan också distribuera grupper av behållare med Azure CLI och en [YAML mallen](container-instances-multi-container-yaml.md). Distribution av YAML-mallen är den bästa metoden när du distribuerar behållargrupper som består av flera behållare.

När du distribuerar med en YAML-mall hemliga värden måste vara **Base64-kodad** i mallen. De hemliga värdena visas dock i klartext i filerna i behållaren.

Följande YAML-mall definierar en behållargrupp med en behållare som monterar en *hemlighet* volymen vid `/mnt/secrets`. Hemlighetsvolymen har två hemligheter, ”mysecret1” och ”mysecret2”.

```yaml
apiVersion: '2018-06-01'
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

För att distribuera med mallen YAML, spara den föregående YAML till en fil med namnet `deploy-aci.yaml`, kör du den [az container skapa] [ az-container-create] med den `--file` parameter:

```azurecli-interactive
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Montera hemlighetsvolymen - Resource Manager

Förutom CLI och YAML-distributionen kan du distribuera en behållargrupp med en Azure [Resource Manager-mall](/azure/templates/microsoft.containerinstance/containergroups).

Först fylla i `volumes` matris i behållargruppen `properties` avsnitt i mallen. När du distribuerar med en Resource Manager-mall hemliga värden måste vara **Base64-kodad** i mallen. De hemliga värdena visas dock i klartext i filerna i behållaren.

För varje behållare i behållargruppen där du vill montera den *hemlighet* volym, Fyll i den `volumeMounts` matrisen i den `properties` avsnitt i behållardefinitionen.

Följande Resource Manager-mall definierar en behållargrupp med en behållare som monterar en *hemlighet* volymen vid `/mnt/secrets`. Hemlighetsvolymen har två hemligheter, ”mysecret1” och ”mysecret2”.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

För att distribuera med Resource Manager-mallen, spara ovanstående JSON till en fil med namnet `deploy-aci.json`, kör du den [az group deployment skapa] [ az-group-deployment-create] med den `--template-file` parameter:

```azurecli-interactive
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```

## <a name="next-steps"></a>Nästa steg

### <a name="volumes"></a>Volymer

Lär dig hur du monterar andra volymtyper i Azure Container Instances:

* [Montera en Azure-filresursen i Azure Container instanser](container-instances-volume-azure-files.md)
* [Montera en emptyDir volymen i Azure Container instanser](container-instances-volume-emptydir.md)
* [Montera en gitRepo volym i Azure Container instanser](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Säker miljövariabler

En annan metod för att tillhandahålla känslig information till behållare (inklusive Windows-behållare) är med [skydda miljövariabler](container-instances-environment-variables.md#secure-values).

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
