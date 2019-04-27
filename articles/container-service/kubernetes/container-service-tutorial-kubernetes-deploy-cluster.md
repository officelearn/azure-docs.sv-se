---
title: (INAKTUELL) Självstudie om Azure Container Service – Distribuera ett kluster
description: Självstudie för Azure Container Service – Distribuera ett kluster
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 58fef0357a903f2ab1d238bbab7b2d9dca673eb4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576177"
---
# <a name="deprecated-deploy-a-kubernetes-cluster-in-azure-container-service"></a>(INAKTUELL) Distribuera ett Kubernetes-kluster i Azure Container Service

> [!TIP]
> Den uppdaterade versionen av den här självstudien som använder Azure Kubernetes Service finns i [Självstudie: Distribuera ett AKS-kluster (Azure Kubernetes Service)](../../aks/tutorial-kubernetes-deploy-cluster.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Kubernetes tillhandahåller en distribuerad plattform för containerbaserade program. Med Azure Container Service kan du snabbt och enkelt etablera ett produktionsklart Kubernetes-kluster. I den här självstudiekursen, som är del 3 av 7, distribueras ett Azure Container Service Kubernetes-kluster. Det här är några av stegen:

> [!div class="checklist"]
> * Distribuera ett Kubernetes ACS-kluster
> * Installera Kubernetes CLI (kubectl)
> * Konfigurera kubectl

I efterföljande självstudier distribuerar du programmet Azure Vote till klustret, skalar ut det, uppdaterar det och konfigurerar Log Analytics för att övervaka Kubernetes-klustret.

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier skapades en behållaravbildning som sedan överfördes till en Azure Container Registry-instans. Om du inte har gjort det här och vill följa med återgår du till [Självstudie 1 – Skapa containeravbildningar](./container-service-tutorial-kubernetes-prepare-app.md).

## <a name="create-kubernetes-cluster"></a>Skapa Kubernetes-kluster

Skapa ett Kubernetes-kluster i Azure Container Service med kommandot [az acs create](/cli/azure/acs#az-acs-create). 

I följande exempel skapas ett kluster med namnet `myK8sCluster` i en resursgrupp med namnet `myResourceGroup`. Den här resursgruppen skapades i [föregående självstudie](./container-service-tutorial-kubernetes-prepare-acr.md).

```azurecli-interactive 
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8SCluster --generate-ssh-keys 
```

I vissa fall, som vid en begränsad utvärderingsversion, har en Azure-prenumeration begränsad åtkomst till Azure-resurser. Om distributionen misslyckas på grund av begränsade tillgängliga kärnor minskar du antalet standardagenter genom att lägga till `--agent-count 1` till kommandot [az acs create](/cli/azure/acs#az-acs-create). 

Efter några minuter slutförs distributionen och jdon-formaterad information om ACS-distributionen returneras.

## <a name="install-the-kubectl-cli"></a>Installera CLI:t kubectl

När du ska ansluta till Kubernetes-klustret från klientdatorn använder du [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), Kubernetes kommandoradsklient. 

Om du använder Azure CloudShell är kubectl redan installerat. Om du vill installera det lokalt kan du använda kommandot [az acs kubernetes install-cli](/cli/azure/acs/kubernetes).

Om du kör det i Linux eller macOS kan du behöva köra med sudo. I Windows ska du se till att skalet körs som administratör.

```azurecli-interactive 
az acs kubernetes install-cli 
```

I Windows är standardsökvägen för installationen *c:\program files (x86)\kubectl.exe*. Du kan behöva lägga till den här filen i path-variabeln i Windows. 

## <a name="connect-with-kubectl"></a>Ansluta med kubectl

Du konfigurerar kubectl att ansluta till ditt Kubernetes-kluster genom att köra kommandot [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes).

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group myResourceGroup --name myK8SCluster
```

Kontrollera anslutningen till klustret med kommandot [kubectl get nodes](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get).

```azurecli-interactive
kubectl get nodes
```

Utdata:

```bash
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.6.2
k8s-agent-98dc3136-1    Ready                      5m        v1.6.2
k8s-agent-98dc3136-2    Ready                      5m        v1.6.2
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.6.2
```

När självstudien är utförd har du ett Kubernetes ACS-kluster som är redo för arbetsbelastningar. I senare självstudier distribuerar du ett program med flera behållare i det här klustret, skalar ut programmet, uppdaterar och övervakar det.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen distribuerades ett Azure Container Service Kubernetes-kluster. Följande steg har slutförts:

> [!div class="checklist"]
> * Distribuera ett Kubernetes ACS-kluster
> * Installera Kubernetes CLI (kubectl)
> * Konfigurera kubectl

Gå vidare till nästa självstudie om du vill lära dig om att köra programmet i klustret.

> [!div class="nextstepaction"]
> [Distribuera programmet i Kubernetes](./container-service-tutorial-kubernetes-deploy-application.md)
