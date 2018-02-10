---
title: "Hantera Azure Kubernetes kluster med webbgränssnittet"
description: Med instrumentpanelen Kubernetes i AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: f13ca506197ff8b9b86cf1f69300fd974b9ddd5a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="kubernetes-dashboard-with-azure-container-service-aks"></a>Kubernetes instrumentpanel med Azure Container Service (AKS)

Azure CLI kan användas för att starta Kubernetes instrumentpanelen. Det här dokumentet går igenom börjar Kubernetes instrumentpanelen med Azure CLI och även går igenom några grundläggande instrumentpanelen åtgärder. Mer information om instrumentpanelen finns i den Kubernetes [Kubernetes instrumentpanel för webbgränssnitt][kubernetes-dashboard].

## <a name="before-you-begin"></a>Innan du börjar

Stegen som beskrivs i det här dokumentet förutsätter att du har skapat ett AKS-kluster och har upprättat en kubectl-anslutning med klustret. Om du behöver dessa objekt visas den [AKS quickstart][aks-quickstart].

Du måste också ha installerat och konfigurerat Azure CLI version 2.0.21 eller senare. Kör az --version för att se versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="start-kubernetes-dashboard"></a>Starta Kubernetes instrumentpanelen

Använd den `az aks browse` kommando för att starta Kubernetes instrumentpanelen. När du kör det här kommandot ersätter du resursnamnet grupp och kluster.

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Det här kommandot skapar en proxy mellan utvecklingssystemet och Kubernetes API och öppnar en webbläsare på Kubernetes instrumentpanelen.

## <a name="run-an-application"></a>Kör ett program

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

![Kubernetes Edit](./media/container-service-kubernetes-ui/view-deployment.png)

Leta upp den `spec.replica` -värde som ska vara 1, ändra värdet till 3. På så sätt kan ökas replikantalet NGINX-distributionen från 1 till 3.

Välj **uppdatering** när redo.

![Kubernetes Edit](./media/container-service-kubernetes-ui/edit-deployment.png)

## <a name="next-steps"></a>Nästa steg

Mer information om Kubernetes instrumentpanelen finns i Kubernetes-dokumentationen.

> [!div class="nextstepaction"]
> [Instrumentpanel för webbgränssnitt Kubernetes][kubernetes-dashboard]

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli