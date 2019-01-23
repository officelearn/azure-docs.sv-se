---
title: Begränsa åtkomsten till kubeconfig i Azure Kubernetes Service (AKS)
description: Lär dig att styra åtkomsten till Kubernetes-konfigurationsfil (kubeconfig) för klusteradministratörer och användare för kluster
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/03/2019
ms.author: iainfou
ms.openlocfilehash: 40588ec29eb6f7c33ba5e1d6071caf5c8ed43424
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54450183"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Använda Azure rollbaserade åtkomstkontroller för att definiera åtkomst till konfigurationsfilen för Kubernetes i Azure Kubernetes Service (AKS)

Du kan interagera med Kubernetes-kluster med den `kubectl` verktyget. Azure CLI tillhandahåller ett enkelt sätt att hämta autentiseringsuppgifter för åtkomst och konfigurationsinformation för att ansluta till ditt AKS-kluster med `kubectl`. Att begränsa vem som kan komma att Kubernetes-konfigurationen (*kubeconfig*) information och för att begränsa de behörigheter som de sedan har, kan du använda Azure rollbaserad åtkomstkontroll (RBAC).

Den här artikeln visar hur du tilldelar RBAC-roller som begränsa vem som kan hämta konfigurationsinformation för ett AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster finns i snabbstarten om AKS [med Azure CLI] [ aks-quickstart-cli] eller [med Azure portal][aks-quickstart-portal].

Den här artikeln kräver också att du kör Azure CLI version 2.0.53 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Tillgängliga rollbehörigheter

När du interagerar med ett AKS-kluster med den `kubectl` används verktyg, en konfigurationsfil som definierar klustrets anslutningsinformation. Den här konfigurationsfilen lagras vanligtvis i *~/.kube/config*. Flera kluster kan definieras i den här *kubeconfig* fil. Du växlar mellan kluster med hjälp av den [kubectl config Använd sammanhangsberoende] [ kubectl-config-use-context] kommando.

Den [aaz aks get-credentials] [ az-aks-get-credentials] kommandot kan du hämta autentiseringsuppgifterna för ett AKS-kluster och sammanfogar dem till den *kubeconfig* fil. Du kan använda Azure rollbaserad åtkomstkontroll (RBAC) för att styra åtkomsten till dessa autentiseringsuppgifter. Dessa Azure RBAC-roller kan du definiera vem som kan hämta den *kubeconfig* filen och vilka behörigheter de har sedan i klustret.

De två inbyggda rollerna är:

* **Administratörsroll för Azure Kubernetes Service-kluster**  
    * Ger åtkomst till *Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action* API-anrop. Den här API-anrop [visar en lista över kluster administratörsautentiseringsuppgifter][api-cluster-admin].
    * Hämtar *kubeconfig* för den *clusterAdmin* roll.
* **Användarrollen för Azure Kubernetes Service-kluster**
    * Ger åtkomst till *Microsoft.ContainerService/managedClusters/listClusterUserCredential/action* API-anrop. Den här API-anrop [visar en lista över kluster autentiseringsuppgifter][api-cluster-user].
    * Hämtar *kubeconfig* för *clusterUser* roll.

## <a name="assign-role-permissions-to-a-user"></a>Tilldela behörigheter till en användare

Om du vill tilldela en Azure-roller till en användare, måste du hämta resurs-ID för AKS-klustret och ID för användarkontot. Kommandona i följande exempel utför följande steg:

* Hämtar klustret resource ID med den [az aks show] [ az-aks-show] kommandot för kluster med namnet *myAKSCluster* i den *myResourceGroup* resursgrupp. Ange ditt eget kluster och resurs gruppnamn efter behov.
* Använder den [az konto show] [ az-account-show] och [az ad user show] [ az-ad-user-show] kommandon få ditt användar-ID.
* Slutligen tilldelar en roll med hjälp av den [az-rolltilldelning skapa] [ az-role-assignment-create] kommando.

I följande exempel tilldelas den *administratörsroll för Azure Kubernetes Service-kluster*:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --upn-or-object-id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

Du kan ändra den tidigare tilldelningen till den *kluster användarrollen* efter behov.

Följande Exempelutdata visar rolltilldelningen har skapats:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>Hämta och verifiera konfigurationsinformationen

Använda med RBAC-roller som har tilldelats den [aaz aks get-credentials] [ az-aks-get-credentials] kommando för att hämta den *kubeconfig* definition för AKS-klustret. I följande exempel hämtas den *--admin* autentiseringsuppgifter, vilket fungerar korrekt om användaren har beviljats den *kluster administratörsroll*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Du kan sedan använda den [kubectl config visa] [ kubectl-config-view] kommando för att kontrollera att den *kontext* för klustret visar att administratören konfigurationsinformationen har tillämpats:

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.eastus.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>Ta bort behörigheter

Ta bort rolltilldelningar med den [az-rolltilldelning ta bort] [ az-role-assignment-delete] kommando. Ange konto-ID och resurs-ID för kluster som erhålls i tidigare kommandon:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Nästa steg

För ökad säkerhet för åtkomst till AKS-kluster, [integrera Azure Active Directory-autentisering][aad-integration].

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-role-assignment-delete]: /cli/azure/role/assignment#az-role-assignment-delete
[aad-integration]: aad-integration.md
