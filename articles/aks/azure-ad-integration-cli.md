---
title: Integrera Azure Active Directory med Azure Kubernetes Service
description: Lär dig hur du använder Azure CLI för att skapa och kluster i Azure Active Directory-aktiverade Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: iainfou
ms.openlocfilehash: c403d809aa1fc6cdbb0555eef414f79b02ab5a8a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764117"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Integrera Azure Active Directory med Azure Kubernetes Service med hjälp av Azure CLI

Azure Kubernetes Service (AKS) kan konfigureras för att använda Azure Active Directory (AD) för autentisering av användare. I den här konfigurationen måste du logga in på ett AKS-kluster med hjälp av en Azure AD-autentiseringstoken. Klusteroperatörer kan också konfigurera Kubernetes rollbaserad åtkomstkontroll (RBAC) baserat på en användares identitet eller directory gruppmedlemskap.

Den här artikeln visar hur du skapar de nödvändiga komponenter för Azure AD, sedan distribuera ett Azure AD-aktiverade-kluster och skapa en grundläggande RBAC-roll i AKS-klustret. Du kan också [slutföra de här stegen med Azure portal][azure-ad-portal].

Den fullständiga exempelskript som används i den här artikeln finns i [Azure CLI-exempel – AKS-integrering med Azure AD][complete-script].

Följande begränsningar gäller:

- Azure AD kan bara aktiveras när du skapar en ny, RBAC-aktiverade kluster. Du kan inte aktivera Azure AD i ett befintligt AKS-kluster.
- *Gästen* användare i Azure AD, till exempel som om du använder en federerad inloggning från en annan katalog stöds inte.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI version 2.0.61 eller senare installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

Skapa en variabel för din önskade AKS-klusternamnet för konsekvens och för att köra kommandon i den här artikeln. I följande exempel används namnet *myakscluster*:

```azurecli-interactive
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Översikt över autentisering i Azure AD

Azure AD-autentisering har angetts för AKS-kluster med OpenID Connect. OpenID Connect är en Identitetslagret som bygger på OAuth 2.0-protokollet. Mer information om OpenID Connect finns i den [öppna ID connect dokumentation][open-id-connect].

Från inuti Kubernetes-klustret används Webhook Tokenautentisering för att verifiera autentiseringstoken. Webhook-tokenautentisering konfigureras och hanteras som en del av AKS-klustret. Mer information om Webhook-tokenautentisering finns i den [webhook authentication dokumentation][kubernetes-webhook].

> [!NOTE]
> När du konfigurerar Azure AD för AKS-autentisering kan konfigureras två Azure AD-program. Den här åtgärden måste utföras av en administratör för Azure-klient.

## <a name="create-azure-ad-server-component"></a>Skapa Azure AD-serverkomponenten

Om du vill integrera med AKS du skapar och använder en Azure AD-program som fungerar som en slutpunkt för identity-begäranden. Den första Azure AD-program som du hämtar Azure AD-gruppmedlemskap för en användare.

Skapa servern program komponenten med den [az ad app skapar] [ az-ad-app-create] kommando och sedan uppdatera gruppmedlemskapet anspråk med hjälp av den [az ad app update] [ az-ad-app-update] kommando. I följande exempel används den *aksname* variabel som anges i den [innan du börjar](#before-you-begin) avsnitt och skapar en variabel

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Nu skapa ett huvudnamn för tjänsten för den server med hjälp av den [az ad sp skapa] [ az-ad-sp-create] kommando. Tjänstens huvudnamn används för att autentisera sig själv i Azure-plattformen. Sedan kan hämta den huvudnamn för tjänsten hemliga med den [az ad sp credential återställa] [ az-ad-sp-credential-reset] kommandot och tilldela variabeln med namnet *serverApplicationSecret* för användning i en av den följande steg:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Azure AD måste ha behörighet att utföra följande åtgärder:

* Läsa katalogdata
* Logga in och läsa användarprofil
* Läsa katalogdata

Tilldelar dessa behörigheter med hjälp av den [az ad appbehörighet Lägg till] [ az-ad-app-permission-add] kommando:

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Slutligen kan bevilja de behörigheter som tilldelats i föregående steg för den server som använder den [behörighetsbeviljande för az ad app] [ az-ad-app-permission-grant] kommando. Det här steget misslyckas om det aktuella kontot inte är en Innehavaradministratör. Du måste också lägga till behörigheter för Azure AD-program att begära information som annars kan kräva administratörs godkännande med hjälp av den [az ad app behörighet-administratörsmedgivande][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Skapa Azure AD-klientkomponent

Andra Azure AD-programmet används när en användare loggar in AKS-kluster med Kubernetes CLI (`kubectl`). Det här klientprogrammet tar autentiseringsbegäran från användaren och kontrollerar deras autentiseringsuppgifter och behörigheter. Skapa Azure AD-app för komponenten klienten med den [az ad app skapar] [ az-ad-app-create] kommando:

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Skapa ett tjänsthuvudnamn för det klientprogram som använder den [az ad sp skapa] [ az-ad-sp-create] kommando:

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Hämta oAuth2-ID för appen server att tillåta autentiseringsflödet mellan de två appkomponenter som använder den [az ad app show] [ az-ad-app-show] kommando. Den här oAuth2-ID används i nästa steg.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Lägg till behörigheter för klientprogrammet och programkomponenter som servern ska använda oAuth2-kommunikation flödar med hjälp av den [az ad appbehörighet Lägg till] [ az-ad-app-permission-add] kommando. Sedan bevilja behörigheter för klientprogrammet för kommunikation med den server som använder den [behörighetsbeviljande för az ad app] [ az-ad-app-permission-grant] kommando:

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions $oAuthPermissionId=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Distribuera klustret

Med de två Azure AD-program som skapats, kan nu skapa själva AKS-klustret. Börja med att skapa en resurs med det [az gruppen skapa] [ az-group-create] kommando. I följande exempel skapas en resursgrupp i den *EastUS* region:

Skapa en resursgrupp för klustret:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Hämta klient-ID för din Azure-prenumeration med hjälp av den [az konto show] [ az-account-show] kommando. Skapa sedan AKS-kluster med den [az aks skapa] [ az-aks-create] kommando. Kommandot för att skapa AKS-kluster tillhandahåller servern och klienten program-ID, server application tjänstens huvudhemlighet och ditt klient-ID:

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

Slutligen hämtar klustret autentiseringsuppgifter som administratör med hjälp av den [aaz aks get-credentials] [ az-aks-get-credentials] kommando. En av följande steg, du får vanliga *användaren* kluster autentiseringsuppgifter för att se Azure AD-autentisering flöde körs.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>Skapa RBAC-bindning

Innan ett Azure Active Directory-konto kan användas med AKS-kluster, måste en bindning för rollen eller kluster roll-koppling som ska skapas. *Roller* definierar behörigheterna som ska tilldelas, och *bindningar* tillämpa dem till önskade användare. Dessa uppgifter kan tillämpas till ett visst namnområde eller över hela klustret. Mer information finns i [med RBAC-auktorisering][rbac-authorization].

Hämta användarens huvudnamn (UPN) för användaren för närvarande inloggad med hjälp av den [az ad loggat in användare visa] [ az-ad-signed-in-user-show] kommando. Det här användarkontot har aktiverats för Azure AD-integrering i nästa steg.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Om användaren du ge RBAC-bindningen för finns i samma Azure AD-klienten, kan du tilldela behörigheter till följd av den *userPrincipalName*. Om användaren är i en annan Azure AD-klient, fråga efter och använda den *objectId* egenskap i stället.

Skapa en YAML-manifest med namnet `basic-azure-ad-binding.yaml` och klistra in följande innehåll. På den sista raden, ersätter *userPrincipalName_or_objectId* med det UPN eller objekt-ID: t från föregående kommando:

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

Skapa ClusterRoleBinding med hjälp av den [kubectl gäller] [ kubectl-apply] kommandot och ange filnamnet på ditt YAML-manifest:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Åtkomst till klustret med Azure AD

Nu ska vi testa integreringen av Azure AD-autentisering för AKS-klustret. Ange den `kubectl` config kontext använder vanlig användarens autentiseringsuppgifter. Den här kontexten skickar alla autentiseringsbegäranden tillbaka via Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Nu använda den [kubectl hämta poddar] [ kubectl-get] kommando för att visa poddar över alla namnområden:

```console
kubectl get pods --all-namespaces
```

Du får ett tecken i uppmaningen för att autentisera med hjälp av Azure AD-autentiseringsuppgifter med hjälp av en webbläsare. När du har autentiserats den `kubectl` kommandot visar poddarna i AKS-kluster som visas i följande Exempelutdata:

```console
$ kubectl get pods --all-namespaces

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

Autentiseringstoken togs emot för `kubectl` cachelagras. Du är bara reprompted för inloggning när token har upphört att gälla eller Kubernetes-konfigurationsfilen har startats.

Om du ser ett meddelande om auktoriseringsfel när du har loggat in med en webbläsare som i följande Exempelutdata, kontrollerar du följande:

```console
error: You must be logged in to the server (Unauthorized)
```

* Användaren som du har loggat in om det är inte en *gäst* i Azure AD-instans (detta är ofta fallet om du använder en federerad inloggning från en annan katalog).
* Användaren är inte medlem i fler än 200.

## <a name="next-steps"></a>Nästa steg

Det fullständiga skriptet som innehåller de kommandon som visas i den här artikeln finns det [exempel lagringsplatsen för Azure AD-integreringsskript i AKS][complete-script].

Om du vill använda Azure AD-användare och grupper för att styra åtkomsten till klusterresurser, se [styra åtkomsten till klusterresurser med rollbaserad åtkomstkontroll och Azure AD-identiteter i AKS][azure-ad-rbac].

Mer information om hur du skyddar Kubernetes-kluster finns i [alternativen för åtkomst och identitet för AKS)][rbac-authorization].

Bästa metoder för identitets- och kontroll, se [bästa praxis för autentisering och auktorisering i AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
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
