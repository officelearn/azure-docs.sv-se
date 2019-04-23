---
title: Klusterresurser för åtkomstkontroll med RBAC och Azure AD i Azure Kubernetes Service
description: Lär dig hur du använder Azure Active Directory-gruppmedlemskap för att begränsa åtkomsten till klusterresurser med rollbaserad åtkomstkontroll (RBAC) i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: iainfou
ms.openlocfilehash: e974c47d1dfb04f66b622c64a7143d00de87c4cb
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014490"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Kontrollera åtkomsten till klusterresurser med rollbaserad åtkomstkontroll och Azure Active Directory-identiteter i Azure Kubernetes Service

Azure Kubernetes Service (AKS) kan konfigureras för att använda Azure Active Directory (AD) för autentisering av användare. I den här konfigurationen måste logga du in till ett AKS-kluster med hjälp av en Azure AD-autentiseringstoken. Du kan också konfigurera Kubernetes rollbaserad åtkomstkontroll (RBAC) för att begränsa åtkomsten till klusterresurser baserat en användares identitet eller gruppmedlemskap.

Den här artikeln visar hur du använder Azure AD-gruppmedlemskap för att styra åtkomsten till namnområden och klusterresurser med Kubernetes RBAC i ett AKS-kluster. Exempel grupper och användare som har skapats i Azure AD och roller och RoleBindings har skapats i AKS-klustret för att bevilja behörighet att skapa och visa resurser.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett AKS-kluster som aktiveras med Azure AD-integrering. Om du behöver ett AKS-kluster, se [integrera Azure Active Directory med AKS][azure-ad-aks-cli].

Du behöver Azure CLI version 2.0.61 eller senare installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Skapa demo-grupper i Azure AD

I den här artikeln ska vi skapa två användarroller som kan användas för att visa hur Kubernetes RBAC och Azure AD kontrollera åtkomsten till klusterresurser. Följande två exempel roller används:

* **Programutvecklare**
    * En användare med namnet *aksdev* som ingår i den *appdev* grupp.
* **Webbplatstillförlitlighet**
    * En användare med namnet *akssre* som ingår i den *opssre* grupp.

Du kan använda befintliga användare och grupper i en Azure AD-klient i produktionsmiljöer.

Hämta först resurs-ID för AKS-kluster med den [az aks show] [ az-aks-show] kommando. Tilldela resurs-ID till en variabel med namnet *AKS_ID* så att den kan refereras i ytterligare kommandon.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Skapa den första exemplet-gruppen i Azure AD för programutvecklare som använder den [az ad group skapa] [ az-ad-group-create] kommando. I följande exempel skapas en grupp med namnet *appdev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Nu skapar en Azure rolltilldelningen för den *appdev* gruppen med hjälp av den [az-rolltilldelning skapa] [ az-role-assignment-create] kommando. Denna tilldelning kan en medlem i gruppen använder `kubectl` att interagera med ett AKS-kluster genom att tilldela dem den *användarrollen för Azure Kubernetes Service-kluster*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Om du får ett felmeddelande som `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`, Vänta några sekunder för den Azure AD-gruppobjekt-ID för sprida genom katalogen och försök sedan den `az role assignment create` -kommandot på nytt.

Skapa en andra exempel-grupp, det här en för SREs med namnet *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Igen, skapa en Azure rolltilldelning för att tilldela medlemmar i gruppen de *användarrollen för Azure Kubernetes Service-kluster*:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Skapa demo-användare i Azure AD

Med två exempel grupper som skapats i Azure AD för våra utvecklare av företagsprogram och SREs, nu ska vi skapa två exempelanvändare. Om du vill testa RBAC-integrering i slutet av artikeln kan logga du in på AKS-kluster med dessa konton.

Skapa det första användarkontot i Azure AD via den [az ad-användare skapa] [ az-ad-user-create] kommando.

I följande exempel skapas en användare med namnet *AKS Dev* och användarens huvudnamn (UPN) för `aksdev@contoso.com`. Uppdatera UPN för att inkludera en verifierad domän för din Azure AD-klient (Ersätt *contoso.com* med din egen domän), och ange ditt eget säkra `--password` autentiseringsuppgifter:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

Nu lägga till användaren i *appdev* grupp som skapades i föregående avsnitt med den [az ad group member Lägg till] [ az-ad-group-member-add] kommando:

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Skapa ett andra användarkonto. I följande exempel skapas en användare med namnet *AKS SRE* och användarens huvudnamn (UPN) för `akssre@contoso.com`. Igen, uppdatera UPN för att inkludera en verifierad domän för din Azure AD-klient (Ersätt *contoso.com* med din egen domän), och ange ditt eget säkra `--password` autentiseringsuppgifter:

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

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Skapa AKS-klusterresurser för utvecklare: app

Azure AD-grupper och användare skapas nu. Azure rolltilldelningar skapades för gruppmedlemmar att ansluta till ett AKS-kluster som en vanlig användare. Nu ska vi konfigurera AKS-klustret för att tillåta dessa olika grupper åtkomst till specifika resurser.

Först hämtar klustret autentiseringsuppgifter som administratör med hjälp av den [aaz aks get-credentials] [ az-aks-get-credentials] kommando. I följande avsnitt, du får vanliga *användaren* kluster autentiseringsuppgifter för att se Azure AD-autentisering flöde körs.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Skapa ett namnområde i AKS-kluster med den [kubectl skapa namnområde] [ kubectl-create] kommando. I följande exempel skapas ett namn för namnområdet *dev*:

```console
kubectl create namespace dev
```

I Kubernetes, *roller* definierar behörigheterna som ska tilldelas, och *RoleBindings* tillämpa dem till önskade användare eller grupper. Dessa uppgifter kan tillämpas till ett visst namnområde eller över hela klustret. Mer information finns i [med RBAC-auktorisering][rbac-authorization].

Börja med att skapa en roll för den *dev* namnområde. Den här rollen ger fullständig behörighet till namnområdet. Du kan ange mer detaljerade behörigheter för olika användare eller grupper i produktionsmiljöer.

Skapa en fil med namnet `role-dev-namespace.yaml` och klistra in följande YAML-manifest:

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

Skapa en roll med hjälp av den [kubectl gäller] [ kubectl-apply] kommandot och ange filnamnet på ditt YAML-manifest:

```console
kubectl apply -f role-dev-namespace.yaml
```

Nu ska hämta resurs-ID för den *appdev* gruppen med hjälp av den [az ad group show] [ az-ad-group-show] kommando. Den här gruppen har angetts som omfattas av en RoleBinding i nästa steg.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Nu skapar du en RoleBinding för den *appdev* gruppen rollen tidigare skapade för namnområdet åtkomst. Skapa en fil med namnet `rolebinding-dev-namespace.yaml` och klistra in följande YAML-manifestet. På den sista raden, ersätter *groupObjectId* med grupp-objekt ID: t från föregående kommando:

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

Skapa RoleBinding med hjälp av den [kubectl gäller] [ kubectl-apply] kommandot och ange filnamnet på ditt YAML-manifest:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Skapa AKS-klusterresurser för SREs

Upprepa föregående steg för att skapa ett namnområde, rollen och RoleBinding för SREs.

Börja med att skapa ett namnområde för *sre* med hjälp av den [kubectl skapa namnområde] [ kubectl-create] kommando:

```console
kubectl create namespace sre
```

Skapa en fil med namnet `role-sre-namespace.yaml` och klistra in följande YAML-manifest:

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

Skapa en roll med hjälp av den [kubectl gäller] [ kubectl-apply] kommandot och ange filnamnet på ditt YAML-manifest:

```console
kubectl apply -f role-sre-namespace.yaml
```

Hämta resurs-ID för den *opssre* gruppen med hjälp av den [az ad group show] [ az-ad-group-show] kommando:

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Skapa en RoleBinding för den *opssre* gruppen rollen tidigare skapade för namnområdet åtkomst. Skapa en fil med namnet `rolebinding-sre-namespace.yaml` och klistra in följande YAML-manifestet. På den sista raden, ersätter *groupObjectId* med grupp-objekt ID: t från föregående kommando:

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

Skapa RoleBinding med hjälp av den [kubectl gäller] [ kubectl-apply] kommandot och ange filnamnet på ditt YAML-manifest:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interagera med klusterresurser med Azure AD-identiteter

Nu ska vi testa arbetet förväntade behörigheter när du skapar och hanterar resurser i ett AKS-kluster. I det här du schemalägger och visa poddar i användarens tilldelade namnområde. Sedan försöker att schemalägga och visa poddar utanför tilldelade namnområdet.

Först måste återställa den *kubeconfig* kontext med hjälp av den [aaz aks get-credentials] [ az-aks-get-credentials] kommando. I föregående avsnitt, kan du ange kontexten med administratörsautentiseringsuppgifter för klustret. Administratörsanvändare kringgår Azure AD-inloggningen anvisningarna. Utan den `--admin` parametern användarkontexten används som kräver att alla förfrågningar ska kunna autentiseras med hjälp av Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Schema som en grundläggande NGINX pod med hjälp av den [kubectl kör] [ kubectl-run] i den *dev* namnområde:

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

Ange autentiseringsuppgifterna för din egen som inloggningsprompten, `appdev@contoso.com` konto har skapats i början av artikeln. När du har loggat in, konto token cachelagras för framtiden `kubectl` kommandon. NGINX är har schemalägger, som visas i följande Exempelutdata:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Nu använda den [kubectl hämta poddar] [ kubectl-get] kommando för att visa poddar i den *dev* namnområde.

```console
kubectl get pods --namespace dev
```

Visas i följande Exempelutdata NGINX-pod är har *kör*:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Skapa och visa klusterresurser utanför tilldelade namnområdet

Prova att visa poddar utanför den *dev* namnområde. Använd den [kubectl hämta poddar] [ kubectl-get] kommandot på nytt, nu att se `--all-namespaces` på följande sätt:

```console
kubectl get pods --all-namespaces
```

Användarens gruppmedlemskap har inte en Kubernetes-roll som gör att den här åtgärden, som visas i följande Exempelutdata:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

På samma sätt som försöker schemalägga en pod i olika namnområdet som den *sre* namnområde. Användarens gruppmedlemskap överensstämmer inte med ett Kubernetes-rollen och RoleBinding för att bevilja dessa behörigheter som visas i följande Exempelutdata:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Testa SRE åtkomst till resurser för AKS-kluster

För att bekräfta att vår Azure AD-gruppmedlemskap och Kubernetes RBAC fungerar korrekt mellan olika användare och grupper, försök tidigare kommandon när du loggat in som den *opssre* användare.

Återställ den *kubeconfig* kontext med hjälp av den [aaz aks get-credentials] [ az-aks-get-credentials] kommando som rensar tidigare cachelagrade Autentiseringstoken för den *aksdev*  användare:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Försök att schemalägga och visa poddar i den tilldelade *sre* namnområde. När du uppmanas logga in med dina egna `opssre@contoso.com` autentiseringsuppgifter som skapats i början av artikeln:

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

I följande Exempelutdata visas du har skapar och visar poddarna:

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Prova att visa eller schemalägga poddar utanför den tilldelade SRE namnområde:

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

Dessa `kubectl` kommandon misslyckas, som visas i följande Exempelutdata. Användarens gruppmedlemskap och Kubernetes-rollen och RoleBindings bevilja inte behörighet att skapa eller manager-resurser i andra namnområden:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Rensa resurser

I den här artikeln har skapat du resurser i AKS-kluster och användare och grupper i Azure AD. Om du vill rensa alla dessa resurser, kör du följande kommandon:

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

Mer information om hur du skyddar Kubernetes-kluster finns i [alternativen för åtkomst och identitet för AKS)][rbac-authorization].

Bästa metoder för identitets- och kontroll, se [bästa praxis för autentisering och auktorisering i AKS][operator-best-practices-identity].

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
