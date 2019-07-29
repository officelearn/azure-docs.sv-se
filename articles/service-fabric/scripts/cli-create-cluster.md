---
title: Exempel på Azure CLI-skriptdistribution
description: Skapa ett säkert Service Fabric Linux-kluster i Azure med hjälp av Azure CLI.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 397ae7ee3b5accbbac1bc17020e6c32397c105a2
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592384"
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>skapa ett säkert Service Fabric Linux-kluster i Azure

Med det här kommandot skapar du ett självsignerat certifikat, lägger till det i ett nyckelvalv och hämtar certifikatet lokalt.  Det nya certifikatet används för att skydda klustret när distribueras.  Du kan också använda ett befintligt certifikat i stället för att skapa ett nytt.  Oavsett hur du gör måste certifikatets ämnesnamn överensstämma med den domän du använder för att få åtkomst till Service Fabric-klustret. Den här matchningen krävs för att tillhandahålla en SSL för klustrets HTTPS-hanteringsslutpunkter och Service Fabric Explorer. Du kan skaffa ett SSL-certifikat från en certifikatutfärdare för `.cloudapp.azure.com`-domänen. Du måste skaffa ett anpassat domännamn för ditt kluster. När du begär ett certifikat från en certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamn du använder för klustret.

Installera vid behov [Azure CLI](/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="sample-script"></a>Exempelskript

[!code-sh[main](../../../cli_scripts/service-fabric/create-cluster/create-cluster.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Rensa distribution

När skriptet har körts kan följande kommando användas för att ta bort resursgruppen, klustret och alla relaterade resurser.

```azurecli
ResourceGroupName = "aztestclustergroup"
az group delete --name $ResourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az sf cluster create](https://docs.microsoft.com/cli/azure/sf/cluster?view=azure-cli-latest) | Skapar ett nytt Service Fabric-kluster.  |

## <a name="next-steps"></a>Nästa steg

Fler Service Fabric CLI-exempel för Azure Service Fabric finns i [Service Fabric CLI-exempel](../samples-cli.md).
