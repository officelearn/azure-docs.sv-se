---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en resurs för Azure container-instans från Azure CLI.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 5e7a3d849f726ae4dbbd559d541464404e427775
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711740"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Skapa en Behållarinstans i Azure-resurs från Azure CLI

YAML nedan definierar Azure-Behållarinstans resursen. Kopiera och klistra in innehållet i en ny fil med namnet `my-aci.yaml` och ersätter kommenterade värdena med dina egna. Referera till [mallformat] [mall-formant] för giltiga YAML. Referera till den [behållare databaser och bilder][repositories-and-images] för tillgängliga namnen och deras motsvarande lagringsplats.

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials:
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
> Inte alla platser har samma tillgängligheten för CPU och minne. Referera till den [plats och resurser][location-to-resource] tabell lista över tillgängliga resurser för behållare per plats och operativsystem.

Vi kommer förlitar sig på YAML-fil som vi skapade för den [ `az container create` ][azure-container-create] kommando. Från Azure CLI, kör den `az container create` kommando ersätter den `<resource-group>` med dina egna. Dessutom för att skydda värden i en YAML distribution avser [säkra värden][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

Utdata från kommandot är `Running...` om giltiga, efter en stund utdata ändras till en JSON-sträng som representerar den nyligen skapade ACI-resursen. Behållaravbildningen är mer än sannolikt inte är tillgängliga för ett tag, men resursen har nu distribuerats.

> [!TIP]
> Var uppmärksam på platser i offentlig förhandsversion Azure Cognitive Service produkter behov i YAML ska justeras enligt detta för att matcha platsen.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format

[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values