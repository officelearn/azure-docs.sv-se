---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en Azure-behållarinstansresurs från Azure CLI.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/06/2020
ms.author: dapine
ms.openlocfilehash: 700a04b58c13a9c7fd5301875226ca234cabeb96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75689469"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Skapa en Azure Container Instance-resurs från Azure CLI

YAML nedan definierar Azure Container Instance-resursen. Kopiera och klistra in innehållet i `my-aci.yaml` en ny fil, namnge och ersätta de kommenterade värdena med dina egna. Se [mallformatet][template-format] för giltig YAML. Se [behållardatabaser och avbildningar][repositories-and-images] för tillgängliga bildnamn och motsvarande databas. Mer information om YAML-referensen för behållarinstanser finns i [YAML-referens: Azure Container Instances][aci-yaml-ref].

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials: # This is required when pulling a non-public image
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
properties:
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  volumes: # This node, is only required for container instances that pull their model in at runtime, such as LUIS.
  - name: aci-file-share
    azureFile:
      shareName: # < File share name >
      storageAccountName: # < Storage account name>
      storageAccountKey: # < Storage account key >
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> Alla platser har inte samma cpu- och minnestillgänglighet. Se [plats- och resurstabellen][location-to-resource] för listningen av tillgängliga resurser för behållare per plats och operativsystem.

Vi förlitar oss på YAML-filen [`az container create`][azure-container-create] som vi skapade för kommandot. Från Azure CLI kör `az container create` du `<resource-group>` kommandot som ersätter med din egen. För att skydda värden inom en YAML-distribution finns dessutom [säkra värden][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

Utdata för kommandot `Running...` är om det är giltigt, efter någon gång utdata ändras till en JSON sträng som representerar den nyligen skapade ACI-resursen. Behållaravbildningen är mer än sannolikt inte tillgänglig på ett tag, men resursen distribueras nu.

> [!TIP]
> Var uppmärksam på platserna för offentliga azure cognitive service-erbjudanden, eftersom YAML måste justeras i enlighet med detta för att matcha platsen.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
