---
title: Integrera Azure Active Directory med Azure Kubernetes Service
description: Lär dig hur du använder Azure CLI för att skapa och Azure Active Directory-aktiverade AKS-kluster (Azure Kubernetes Service)
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: d17ae12beecf9d83ef6d688af799787c5ccf322b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253057"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Integrera Azure Active Directory med Azure Kubernetes-tjänsten med Azure CLI

Azure Kubernetes Service (AKS) kan konfigureras för att använda Azure Active Directory (AD) för användarautentisering. I den här konfigurationen kan du logga in på ett AKS-kluster med hjälp av en Azure AD-autentiseringstoken. Klusteroperatorer kan också konfigurera Kubernetes rollbaserad åtkomstkontroll (RBAC) baserat på en användares identitet eller kataloggruppmedlemskap.

Den här artikeln visar hur du skapar de nödvändiga Azure AD-komponenterna, distribuerar sedan ett Azure AD-aktiverat kluster och skapar en grundläggande RBAC-roll i AKS-klustret. Du kan också [utföra dessa steg med Azure-portalen][azure-ad-portal].

Det fullständiga exempelskriptet som används i den här artikeln finns i [Azure CLI-exempel - AKS-integrering med Azure AD][complete-script].

Följande begränsningar gäller:

- Azure AD kan bara aktiveras när du skapar ett nytt, RBAC-aktiverat kluster. Du kan inte aktivera Azure AD i ett befintligt AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI version 2.0.61 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

Gå [https://shell.azure.com](https://shell.azure.com) till för att öppna Cloud Shell i din webbläsare.

Skapa en variabel för önskat AKS-klusternamn för konsekvens och för att hjälpa till att köra kommandona i den här artikeln. I följande exempel används namnet *myakscluster:*

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Översikt över Azure AD-autentisering

Azure AD-autentisering tillhandahålls till AKS-kluster med OpenID Connect. OpenID Connect är ett identitetslager som är byggt ovanpå OAuth 2.0-protokollet. Mer information om OpenID Connect finns i [dokumentationen för Öppna ID connect][open-id-connect].

Från insidan av Kubernetes-klustret används Webhook Token Authentication för att verifiera autentiseringstoken. Webhook token autentisering konfigureras och hanteras som en del av AKS-klustret. Mer information om Webhook-tokenautentisering finns i [webhook-autentiseringsdokumentationen][kubernetes-webhook].

> [!NOTE]
> När du konfigurerar Azure AD för AKS-autentisering konfigureras två Azure AD-program. Den här åtgärden måste slutföras av en Azure-klientadministratör.

## <a name="create-azure-ad-server-component"></a>Skapa Azure AD-serverkomponent

Om du vill integrera med AKS skapar och använder du ett Azure AD-program som fungerar som en slutpunkt för identitetsbegäranden. Det första Azure AD-programmet du behöver får Azure AD-gruppmedlemskap för en användare.

Skapa serverprogramkomponenten med kommandot [az ad app create][az-ad-app-create] och uppdatera sedan anspråk på gruppmedlemskap med kommandot az ad app [update.][az-ad-app-update] I följande exempel används variabeln *aksname* som definierats i avsnittet [Innan du börjar](#before-you-begin) och en variabel skapas

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Nu skapa en tjänst huvudnamn för servern app med hjälp av [az ad sp skapa][az-ad-sp-create] kommandot. Det här tjänsthuvudhuvudet används för att autentisera sig själv i Azure-plattformen. Hämta sedan tjänstens huvudhemlighet med kommandot [az ad sp-återställning][az-ad-sp-credential-reset] och tilldela variabeln *serverApplicationSecret* för användning i något av följande steg:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Azure AD behöver behörigheter för att utföra följande åtgärder:

* Läs katalogdata
* Logga in och läsa användarprofil

Tilldela dessa behörigheter med kommandot [az ad app-behörighet:][az-ad-app-permission-add]

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Slutligen bevilja de behörigheter som tilldelats i föregående steg för serverprogrammet med kommandot [az ad app permission grant.][az-ad-app-permission-grant] Det här steget misslyckas om det aktuella kontot inte är en klientadministratör. Du måste också lägga till behörigheter för Azure AD-program för att begära information som annars kan kräva administrativt medgivande med hjälp av [admin-consent för az ad-app-behörighet:][az-ad-app-permission-admin-consent]

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Skapa Azure AD-klientkomponent

Det andra Azure AD-programmet används när en användare loggar till AKS-klustret med Kubernetes CLI (`kubectl`). Det här klientprogrammet tar autentiseringsbegäran från användaren och verifierar deras autentiseringsuppgifter och behörigheter. Skapa Azure AD-appen för klientkomponenten med kommandot [az ad app create:][az-ad-app-create]

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Skapa ett tjänsthuvudnamn för klientprogrammet med kommandot [az ad sp create:][az-ad-sp-create]

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Hämta oAuth2-ID:et för serverappen så att autentiseringsflödet mellan de två appkomponenterna med kommandot [az ad app show.][az-ad-app-show] Detta oAuth2-ID används i nästa steg.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Lägg till behörigheterna för klientprogrammet och serverprogramkomponenterna om du vill använda kommunikationsflödet oAuth2 med kommandot [az ad app-behörighet.][az-ad-app-permission-add] Bevilja sedan behörigheter för klientprogrammet till kommunikation med serverprogrammet med kommandot [az ad app permission grant:][az-ad-app-permission-grant]

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Distribuera klustret

Med de två Azure AD-program som skapats, nu skapa AKS klustret själv. Skapa först en resursgrupp med kommandot [az group create.][az-group-create] I följande exempel skapas resursgruppen i *Regionen EastUS:*

Skapa en resursgrupp för klustret:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Hämta klient-ID:et för din Azure-prenumeration med kommandot [az account show.][az-account-show] Skapa sedan AKS-klustret med kommandot [az aks create.][az-aks-create] Kommandot för att skapa AKS-klustret tillhandahåller server- och klientprogram-ID: n, huvudhemligheten för serverprogramtjänsten och klient-ID:

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

Slutligen hämtar du klusteradministratörsuppgifterna med kommandot [az aks get-credentials.][az-aks-get-credentials] I något av följande steg får *user* du de vanliga användarklusterautentiseringsuppgifterna för att se Azure AD-autentiseringsflödet i praktiken.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>Skapa RBAC-bindning

Innan ett Azure Active Directory-konto kan användas med AKS-klustret måste en rollbindning eller klusterrollbindning skapas. *Roller* definierar behörigheterna att bevilja och *bindningar* tillämpar dem på önskade användare. Dessa tilldelningar kan tillämpas på ett visst namnområde eller i hela klustret. Mer information finns i [Använda RBAC-auktorisering][rbac-authorization].

Hämta upn-namnet (user principal name) för den användare som för närvarande är inloggad med kommandot [az ad signed-in-user show.][az-ad-signed-in-user-show] Det här användarkontot är aktiverat för Azure AD-integrering i nästa steg.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Om användaren som du beviljar RBAC-bindningen för finns i samma Azure AD-klient, tilldela behörigheter baserat på *userPrincipalName*. Om användaren finns i en annan Azure AD-klient frågar du efter och använder egenskapen *objectId* i stället.

Skapa ett YAML-manifest med namnet `basic-azure-ad-binding.yaml` och klistra in följande innehåll. På den sista raden ersätter *du userPrincipalName_or_objectId* med UPN- eller objekt-ID-utdata från föregående kommando:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Skapa ClusterRoleBinding med kommandot [kubectl apply][kubectl-apply] och ange filnamnet för YAML-manifestet:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Komma åt kluster med Azure AD

Nu ska vi testa integreringen av Azure AD-autentisering för AKS-klustret. Ange `kubectl` config-kontexten så att vanliga användarautentiseringsuppgifter används. Den här kontexten skickar tillbaka alla autentiseringsbegäranden via Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Använd nu kommandot [kubectl get pods][kubectl-get] för att visa poddar över alla namnområden:

```console
kubectl get pods --all-namespaces
```

Du får en inloggningsfråga för att autentisera med Azure AD-autentiseringsuppgifter med hjälp av en webbläsare. När du har autentiserats `kubectl` visas poddarna i AKS-klustret, vilket visas i följande exempelutdata:

```console
kubectl get pods --all-namespaces
```

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

Autentiseringstoken `kubectl` som tas emot för cachelagras. Du uppmanas bara att logga in igen när token har upphört att gälla eller kubernetes config-filen återskapas.

Om ett auktoriseringsfelmeddelande visas när du har loggat in med en webbläsare som i följande exempelutdata kontrollerar du följande möjliga problem:

```output
error: You must be logged in to the server (Unauthorized)
```

* Du har definierat rätt objekt-ID eller UPN, beroende på om användarkontot finns i samma Azure AD-klient eller inte.
* Användaren är inte medlem i mer än 200 grupper.
* Hemligheten som definieras i programregistreringen för servern matchar värdet som konfigurerats med`--aad-server-app-secret`

## <a name="next-steps"></a>Nästa steg

Det fullständiga skriptet som innehåller kommandona som visas i den här artikeln finns [i Azure AD-integreringsskriptet i AKS-exemplen repo][complete-script].

Information om hur du använder Azure AD-användare och -grupper för att kontrollera åtkomsten till klusterresurser finns i [Kontrollera åtkomst till klusterresurser med hjälp av rollbaserad åtkomstkontroll och Azure AD-identiteter i AKS][azure-ad-rbac].

Mer information om hur du skyddar Kubernetes-kluster finns i [Åtkomst- och identitetsalternativ för AKS)][rbac-authorization].

Bästa praxis för identitets- och resurskontroll finns [i Metodtips för autentisering och auktorisering i AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[azure-ad-portal]: azure-ad-integration.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
