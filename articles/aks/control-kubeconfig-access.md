---
title: Begränsa åtkomsten till kubeconfig i Azure Kubernetes Service (AKS)
description: Lär dig hur du styr åtkomsten till kubernetes konfigurationsfilen (kubeconfig) för klusteradministratörer och klusteranvändare
services: container-service
ms.topic: article
ms.date: 01/28/2020
ms.openlocfilehash: 25c710cce2855d6af985d3f46082f47573bbc101
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259557"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Använda Azure-rollbaserade åtkomstkontroller för att definiera åtkomst till Kubernetes konfigurationsfil i Azure Kubernetes Service (AKS)

Du kan interagera med Kubernetes-kluster med hjälp av verktyget. `kubectl` Azure CLI är ett enkelt sätt att få åtkomstautentiseringsuppgifter och konfigurationsinformation `kubectl`för att ansluta till dina AKS-kluster med . Om du vill begränsa vem som kan få kubernetes-konfigurationen *(kubeconfig)* information och begränsa de behörigheter de sedan har kan du använda Azure-rollbaserade åtkomstkontroller (RBAC).

Den här artikeln visar hur du tilldelar RBAC-roller som begränsar vem som kan hämta konfigurationsinformationen för ett AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster läser du SNABBSTARTen för AKS [med Azure CLI][aks-quickstart-cli] eller använder [Azure-portalen][aks-quickstart-portal].

Den här artikeln kräver också att du kör Azure CLI version 2.0.65 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Behörigheter för tillgängliga klusterroller

När du interagerar med ett `kubectl` AKS-kluster med verktyget används en konfigurationsfil som definierar information om klusteranslutning. Den här konfigurationsfilen lagras vanligtvis i *~/.kube/config*. Flera kluster kan definieras i den här *kubeconfig-filen.* Du växlar mellan kluster med kommandot [kubectl config use-context.][kubectl-config-use-context]

Med kommandot [az aks get-credentials][az-aks-get-credentials] kan du hämta åtkomstautentiseringsuppgifterna för ett AKS-kluster och sammanfoga dem till *kubeconfig-filen.* Du kan använda Azure-rollbaserade åtkomstkontroller (RBAC) för att styra åtkomsten till dessa autentiseringsuppgifter. Med de här Azure RBAC-rollerna kan du definiera vem som kan hämta *kubeconfig-filen* och vilka behörigheter de sedan har i klustret.

De två inbyggda rollerna är:

* **Azure Kubernetes tjänstkluster administratörsroll**  
  * Ger åtkomst till *API-anropet Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action.* Det här API-anropet [visar autentiseringsuppgifterna för klusteradministratören][api-cluster-admin].
  * Hämtar *kubeconfig* för *rollen clusterAdmin.*
* **Användarroll för Azure Kubernetes-tjänstkluster**
  * Ger åtkomst till *API-anropet Microsoft.ContainerService/managedClusters/listClusterUserCredential/action.* Det här API-anropet [visar klusteranvändarautentiseringsuppgifterna][api-cluster-user].
  * Hämtar *kubeconfig* för *rollen clusterUser.*

Dessa RBAC-roller kan tillämpas på en Azure Active Directory (AD) användare eller grupp.

> ! - Jag vet inte vad du säger. På kluster som använder Azure AD har användare med *rollen clusterUser* en tom *kubeconfig-fil* som frågar en inloggning. När användarna har loggat in har de åtkomst baserat på sina Azure AD-användar- eller gruppinställningar. Användare med *rollen clusterAdmin* har administratörsåtkomst.
>
> Kluster som inte använder Azure AD använder bara *rollen clusterAdmin.*

## <a name="assign-role-permissions-to-a-user-or-group"></a>Tilldela rollbehörigheter till en användare eller grupp

Om du vill tilldela en av de tillgängliga rollerna måste du hämta resurs-ID:t för AKS-klustret och ID:t för Azure AD-användarkontot eller azure AD-gruppen. Följande exempelkommandon:

* Hämta klusterresurs-ID:t med kommandot az aks show för [klustret][az-aks-show] *myAKSCluster* i resursgruppen *myResourceGroup.* Ange ditt eget kluster- och resursgruppnamn efter behov.
* Använd [az-kontovisningen][az-account-show] och [az-annonsanvändaren visa][az-ad-user-show] kommandon för att få ditt användar-ID.
* Tilldela slutligen en roll med kommandot [skapa az-rolltilldelning.][az-role-assignment-create]

I följande exempel tilldelas *azure kubernetes-tjänstklusterklusteradministratörsrollen* till ett enskilt användarkonto:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!TIP]
> Om du vill tilldela behörigheter till en `--assignee` Azure AD-grupp uppdaterar du parametern som visas i föregående exempel med objekt-ID för *gruppen* i stället för en *användare*. Om du vill hämta objekt-ID:et för en grupp använder du kommandot [az ad group show.][az-ad-group-show] I följande exempel hämtar objekt-ID:n för Azure AD-gruppen *appdev:*`az ad group show --group appdev --query objectId -o tsv`

Du kan ändra den tidigare tilldelningen till *klusteranvändarrollen* efter behov.

Följande exempelutdata visar att rolltilldelningen har skapats:

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

Med RBAC-roller tilldelade använder du kommandot [az aks get-credentials][az-aks-get-credentials] för att hämta *kubeconfig-definitionen* för AKS-klustret. I följande exempel får *--admin-autentiseringsuppgifterna,* som fungerar korrekt om användaren har tilldelats *rollen klusteradministratör:*

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Du kan sedan använda kommandot [kubectl config view][kubectl-config-view] för att kontrollera att *kontexten* för klustret visar att konfigurationsinformationen för administratörer har tillämpats:

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

## <a name="remove-role-permissions"></a>Ta bort rollbehörigheter

Om du vill ta bort rolltilldelningar använder du kommandot [ta bort az-rolltilldelning.][az-role-assignment-delete] Ange konto-ID och klusterresurs-ID, enligt erhållna i föregående kommandon. Om du har tilldelat rollen till en grupp i stället för en användare anger du `--assignee` lämpligt gruppobjekt-ID i stället för kontoobjekt-ID för parametern:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Nästa steg

Om du vill ha ökad säkerhet vid åtkomst till AKS-kluster [integrerar du Azure Active Directory-autentisering][aad-integration].

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
[aad-integration]: azure-ad-integration.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
