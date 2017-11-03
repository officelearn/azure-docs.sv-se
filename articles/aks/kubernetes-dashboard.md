---
title: "Hantera Azure Kubernetes kluster med webbgränssnittet | Microsoft Docs"
description: Med instrumentpanelen Kubernetes i AKS
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 764bbb95b661bb750d7802ee5996d8a491be928d
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="kubernetes-dashboard-with-azure-container-service-aks"></a>Kubernetes instrumentpanel med Azure Container Service (AKS)

Azure CLI kan användas för att starta Kubernetes instrumentpanelen. Det här dokumentet går igenom börjar Kubernetes instrumentpanelen med Azure CLI och även går igenom några grundläggande instrumentpanelen åtgärder. Mer information om instrumentpanelen finns i den Kubernetes [Kubernetes instrumentpanel för webbgränssnitt](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

## <a name="before-you-begin"></a>Innan du börjar

Stegen som beskrivs i det här dokumentet förutsätter att du har skapat ett AKS-kluster och har upprättat en kubectl-anslutning med klustret. Se [AKS-snabbstart](./kubernetes-walkthrough.md) om du behöver de här objekten.

Du måste också ha installerat och konfigurerat Azure CLI, version 2.0.20 eller senare. Kör az --version för att se versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="start-kubernetes-dashboard"></a>Starta Kubernetes instrumentpanelen

Använd den `az aks browse` kommando för att starta Kubernetes instrumentpanelen. När du kör det här kommandot ersätter du resursnamnet grupp och kluster.

```azurecli
az aks browse --resource-group myResourceGroup --name myK8SCluster
```

Det här kommandot skapar en proxy mellan utvecklingssystemet och Kubernetes API och öppnar en webbläsare på Kubernetes instrumentpanelen.

## <a name="run-an-application"></a>Köra ett program

I instrumentpanelen Kubernetes klickar du på den **skapa** knappen i det övre högra fönstret. Ge distributionen namnet `nginx` och ange `nginx:latest` för bilder namn. Under **Service**väljer **externa** och ange `80` för både porten och målporten.

När du är klar klickar du på **distribuera** distributionen skapas.

![Dialogrutan Skapa i Kubernetes Service](./media/container-service-kubernetes-ui/create-deployment.png)

## <a name="view-the-application"></a>Visa programmet

Statusinformation om programmet visas på instrumentpanelen Kubernetes. När programmet körs har varje komponent en grön kryssrutan bredvid den.

![Kubernetes skida](./media/container-service-kubernetes-ui/complete-deployment.png)

Om du vill se mer information om programmet skida klickar du på **skida** i den vänstra menyn och välj sedan den **NGINX** baljor. Här kan du se baljor-specifik information, till exempel resursförbrukning.

![Kubernetes resurser](./media/container-service-kubernetes-ui/running-pods.png)

För att hitta IP-adressen för programmet, klickar du på **Services** i den vänstra menyn och välj sedan den **NGINX** service.

![nginx-vyn](./media/container-service-kubernetes-ui/nginx-service.png)

## <a name="edit-the-application"></a>Redigera programmet

Förutom att skapa och visa program kan Kubernetes instrumentpanelen användas för att redigera och uppdatera application-distributioner.

Om du vill redigera en distribution **distributioner** i den vänstra menyn och välj sedan den **NGINX** distribution. Välj slutligen **redigera** i det övre högra navigeringsfältet.

![Redigera Kubernetes](./media/container-service-kubernetes-ui/view-deployment.png)

Leta upp den `spec.replica` -värde som ska vara 1, ändra värdet till 3. På så sätt kan ökas replikantalet NGINX-distributionen från 1 till 3.

Välj **uppdatering** när redo.

![Redigera Kubernetes](./media/container-service-kubernetes-ui/edit-deployment.png)

## <a name="next-steps"></a>Nästa steg

Mer information om Kubernetes instrumentpanelen finns i Kubernetes-dokumentationen.

> [!div class="nextstepaction"]
> [Instrumentpanel för webbgränssnitt Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)