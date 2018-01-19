---
title: Azure Service Fabric CLI skript distribuerar exempel
description: "Skapa en säker Service Fabric Linux-kluster i Azure med hjälp av Azure Service Fabric CLI."
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: d49383b4f7b3b13beb9ea36ae725938e17ef1456
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>Skapa en säker Service Fabric Linux-kluster i Azure

Det här kommandot skapar ett självsignerat certifikat, läggs till en nyckelvalvet och hämtar certifikat lokalt.  Det nya certifikatet används för att skydda klustret när distribueras.  Du kan också använda ett befintligt certifikat i stället för att skapa en ny.  Oavsett hur certifikatets ämnesnamn måste matcha den domän som du använder för att få åtkomst till Service Fabric-klustret. Matchningen krävs för att tillhandahålla en SSL för klustrets HTTPS management slutpunkter och Service Fabric Explorer. Du kan skaffa ett SSL-certifikat från en Certifikatutfärdare för den `.cloudapp.azure.com` domän. Du måste skaffa ett anpassat domännamn för klustret. När du begär ett certifikat från en Certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamnet som du använder för klustret.

Om det behövs installerar den [Azure CLI 2.0](/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="sample-script"></a>Exempelskript

[!code-sh[main](../../../cli_scripts/service-fabric/create-cluster/create-cluster.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Rensa distribution

Följande kommando kan användas för att ta bort resursgruppen, kluster och alla relaterade resurser efter skriptexempel har körts.

```azurecli
ResourceGroupName = "aztestclustergroup"
az group delete --name $ResourceGroupName
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ SA kluster](https://docs.microsoft.com/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) | Skapar ett nytt Service Fabric-kluster.  |

## <a name="next-steps"></a>Nästa steg

Ytterligare Service Fabric CLI prover för Azure Service Fabric kan hittas i den [Service Fabric CLI exempel](../samples-cli.md).
