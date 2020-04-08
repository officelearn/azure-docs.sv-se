---
title: Använda Azure AD och RBAC för kluster
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du använder Azure Active Directory-gruppmedlemskap för att begränsa åtkomsten till klusterresurser med hjälp av rollbaserad åtkomstkontroll (RBAC) i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: ad195085c049776bf0db418c57f2c72830f1adff
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803577"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Kontrollera åtkomst till klusterresurser med hjälp av rollbaserad åtkomstkontroll och Azure Active Directory-identiteter i Azure Kubernetes Service

Azure Kubernetes Service (AKS) kan konfigureras för att använda Azure Active Directory (AD) för användarautentisering. I den här konfigurationen loggar du in på ett AKS-kluster med hjälp av en Azure AD-autentiseringstoken. Du kan också konfigurera Kubernetes rollbaserade åtkomstkontroll (RBAC) för att begränsa åtkomsten till klusterresurser som baseras på en användares identitet eller gruppmedlemskap.

Den här artikeln visar hur du använder Azure AD-gruppmedlemskap för att styra åtkomsten till namnområden och klusterresurser med Kubernetes RBAC i ett AKS-kluster. Exempelgrupper och användare skapas i Azure AD och sedan skapas roller och rollbindningar i AKS-klustret för att bevilja lämpliga behörigheter för att skapa och visa resurser.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster aktiverat med Azure AD-integrering. Om du behöver ett AKS-kluster läser du [Integrera Azure Active Directory med AKS][azure-ad-aks-cli].

Du behöver Azure CLI version 2.0.61 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Skapa demogrupper i Azure AD

I den här artikeln ska vi skapa två användarroller som kan användas för att visa hur Kubernetes RBAC och Azure AD styr åtkomst till klusterresurser. Följande två exempelroller används:

* **Programutvecklare**
    * En användare med namnet *aksdev* som ingår i *appdev-gruppen.*
* **Tekniker för platstillförlitlighet**
    * En användare med namnet *akssre* som ingår i *opssre-gruppen.*

I produktionsmiljöer kan du använda befintliga användare och grupper i en Azure AD-klientorganisation.

Hämta först resurs-ID:t för AKS-klustret med kommandot [az aks show.][az-aks-show] Tilldela resurs-ID:t till en variabel med namnet *AKS_ID* så att det kan refereras i ytterligare kommandon.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Skapa den första exempelgruppen i Azure AD för programutvecklare med kommandot [az ad group create.][az-ad-group-create] I följande exempel skapas en grupp med namnet *appdev:*

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Skapa nu en Azure-rolltilldelning för *appdev-gruppen* med kommandot [az role assignment create.][az-role-assignment-create] Med den här tilldelningen `kubectl` kan alla medlemmar i gruppen använda för att interagera med ett AKS-kluster genom att ge dem *användarrollen Azure Kubernetes Service Cluster*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Om du får ett `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`felmeddelande, till exempel, vänta några sekunder för Azure AD-gruppobjekt-ID att sprida sig genom katalogen och försök sedan `az role assignment create` kommandot igen.

Skapa en andra exempelgrupp, den här för SREs som heter *opssre:*

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Skapa återigen en Azure-rolltilldelning för att ge medlemmar i gruppen *användarrollen Azure Kubernetes Service Cluster:*

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Skapa demoanvändare i Azure AD

Med två exempelgrupper som skapats i Azure AD för våra programutvecklare och SRE kan nu skapa två exempelanvändare. Om du vill testa RBAC-integreringen i slutet av artikeln loggar du in på AKS-klustret med dessa konton.

Skapa det första användarkontot i Azure AD med kommandot [az ad user create.][az-ad-user-create]

I följande exempel skapas en användare med visningsnamnet *AKS Dev* `aksdev@contoso.com`och användarens huvudnamn (UPN) för . Uppdatera UPN för att inkludera en verifierad domän för din Azure AD-klientorganisation (ersätt *contoso.com* med din egen domän) och ange din egen säkra `--password` autentiseringsuppgifter:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

Lägg nu till användaren i *appdev-gruppen* som skapats i föregående avsnitt med kommandot [az ad group member add:][az-ad-group-member-add]

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Skapa ett andra användarkonto. I följande exempel skapas en användare med visningsnamnet *AKS SRE* `akssre@contoso.com`och användarens huvudnamn (UPN) för . Uppdatera UPN för att inkludera en verifierad domän *contoso.com* för din Azure AD-klientorganisation (ersätt `--password` contoso.com med din egen domän) och ange din egen säkra autentiseringsuppgifter:

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name akssre@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Skapa AKS-klusterresurser för appdelegeringar

Azure AD-grupper och användare skapas nu. Azure-rolltilldelningar skapades för gruppmedlemmarna att ansluta till ett AKS-kluster som en vanlig användare. Nu ska vi konfigurera AKS-klustret så att dessa olika grupper får åtkomst till specifika resurser.

Hämta först klusteradministratörsuppgifterna med kommandot [az aks get-credentials.][az-aks-get-credentials] I något av följande avsnitt får *user* du de vanliga användarklusterautentiseringsuppgifterna för att se Azure AD-autentiseringsflödet i praktiken.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Skapa ett namnområde i AKS-klustret med kommandot [kubectl create namespace.][kubectl-create] I följande exempel skapas en *namnområdesnamnsdelegering:*

```console
kubectl create namespace dev
```

I Kubernetes *definierar roller* behörigheterna att bevilja och *Rollbindningar* tillämpar dem på önskade användare eller grupper. Dessa tilldelningar kan tillämpas på ett visst namnområde eller i hela klustret. Mer information finns i [Använda RBAC-auktorisering][rbac-authorization].

Skapa först en roll för *dev-namnområdet.* Den här rollen ger namnområdet fullständig behörighet. I produktionsmiljöer kan du ange mer detaljerade behörigheter för olika användare eller grupper.

Skapa en `role-dev-namespace.yaml` fil med namnet och klistra in följande YAML-manifest:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Skapa rollen med kommandot [kubectl apply][kubectl-apply] och ange filnamnet för YAML-manifestet:

```console
kubectl apply -f role-dev-namespace.yaml
```

Hämta sedan resurs-ID:n för *appdev-gruppen* med kommandot [az ad group show.][az-ad-group-show] Den här gruppen anges som ämne för ett rollbindning i nästa steg.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Skapa nu ett rollbindning för *appdev-gruppen* om du vill använda den tidigare skapade rollen för namnområdesåtkomst. Skapa en `rolebinding-dev-namespace.yaml` fil med namnet och klistra in följande YAML-manifest. På den sista raden ersätter du *groupObjectId* med gruppobjekt-ID-utdata från föregående kommando:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

Skapa Rollbindningen med kommandot [kubectl apply][kubectl-apply] och ange filnamnet för YAML-manifestet:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Skapa AKS-klusterresurserna för SRE:er

Upprepa nu föregående steg för att skapa ett namnområde, en roll och ett rollbindning för SRE:erna.

Skapa först ett namnområde för *sre* med kommandot [kubectl create namespace:][kubectl-create]

```console
kubectl create namespace sre
```

Skapa en `role-sre-namespace.yaml` fil med namnet och klistra in följande YAML-manifest:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Skapa rollen med kommandot [kubectl apply][kubectl-apply] och ange filnamnet för YAML-manifestet:

```console
kubectl apply -f role-sre-namespace.yaml
```

Hämta resurs-ID:et för *opssre-gruppen* med kommandot [az ad group show:][az-ad-group-show]

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Skapa ett rollbindning för *opssre-gruppen* om du vill använda den tidigare skapade rollen för namnområdesåtkomst. Skapa en `rolebinding-sre-namespace.yaml` fil med namnet och klistra in följande YAML-manifest. På den sista raden ersätter du *groupObjectId* med gruppobjekt-ID-utdata från föregående kommando:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

Skapa Rollbindningen med kommandot [kubectl apply][kubectl-apply] och ange filnamnet för YAML-manifestet:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interagera med klusterresurser med Azure AD-identiteter

Nu ska vi testa de förväntade behörigheterna fungerar när du skapar och hanterar resurser i ett AKS-kluster. I de här exemplen schemalägger och visar du poddar i användarens tilldelade namnområde. Sedan försöker du schemalägga och visa poddar utanför det tilldelade namnområdet.

Återställ först *kubeconfig-kontexten* med kommandot [az aks get-credentials.][az-aks-get-credentials] I ett tidigare avsnitt anger du kontexten med hjälp av klusteradministratörsautentiseringsuppgifterna. Administratörsanvändaren kringgår Azure AD-inloggningsuppmaningar. Utan `--admin` parametern tillämpas användarkontexten som kräver att alla begäranden autentiseras med Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Schemalägg en grundläggande NGINX-pod med kommandot [kubectl run][kubectl-run] i *dev-namnområdet:*

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

Som inloggningsfråga anger du autentiseringsuppgifterna för ditt eget `appdev@contoso.com` konto som skapades i början av artikeln. När du har loggat in cachelagras kontotoken för framtida `kubectl` kommandon. NGINX har schemaläggs, vilket visas i följande exempelutdata:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Använd nu kommandot [kubectl get pods][kubectl-get] för att visa poddar i *dev-namnområdet.*

```console
kubectl get pods --namespace dev
```

Som visas i följande exempelutdata *körs*NGINX-podden:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Skapa och visa klusterresurser utanför det tilldelade namnområdet

Försök nu att visa poddar utanför *dev-namnområdet.* Använd [kommandot kubectl get pods][kubectl-get] igen, `--all-namespaces` den här gången för att se följande:

```console
kubectl get pods --all-namespaces
```

Användarens gruppmedlemskap har ingen Kubernetes-roll som tillåter den här åtgärden, vilket visas i följande exempelutdata:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Försök på samma sätt att schemalägga en pod i *sre* ett annat namnområde, till exempel sre-namnområdet. Användarens gruppmedlemskap stämmer inte överens med en Kubernetes-roll och Rollbindning om att bevilja dessa behörigheter, vilket visas i följande exempelutdata:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Testa SRE-åtkomsten till AKS-klusterresurserna

Om du vill bekräfta att vårt Azure AD-gruppmedlemskap och Kubernetes RBAC fungerar korrekt mellan olika användare och grupper provar du de tidigare kommandona när de är inloggade som *opssre-användare.*

Återställ *kubeconfig-kontexten* med kommandot [az aks get-credentials][az-aks-get-credentials] som rensar den tidigare cachelagrade autentiseringstoken för *aksdev-användaren:*

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Försök att schemalägga och visa poddar i det tilldelade *sre-namnområdet.* Logga in med dina `opssre@contoso.com` egna autentiseringsuppgifter som skapats i början av artikeln när du uppmanas att göra det:

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

Som visas i följande exempelutdata kan du skapa och visa poddar:

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Försök nu att visa eller schemalägga poddar utanför tilldelat SRE-namnområde:

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

Dessa `kubectl` kommandon misslyckas, vilket visas i följande exempelutdata. Användarens gruppmedlemskap och Kubernetes-roll och rollbindningar ger inte behörighet att skapa eller hantera resurser i andra namnområden:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Rensa resurser

I den här artikeln skapade du resurser i AKS-klustret och användare och grupper i Azure AD. Om du vill rensa alla dessa resurser kör du följande kommandon:

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du skyddar Kubernetes-kluster finns i [Åtkomst- och identitetsalternativ för AKS)][rbac-authorization].

Bästa praxis för identitets- och resurskontroll finns [i Metodtips för autentisering och auktorisering i AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
