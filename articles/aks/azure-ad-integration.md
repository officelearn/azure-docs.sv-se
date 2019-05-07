---
title: Integrera Azure Active Directory med Azure Kubernetes Service
description: Så här att skapa kluster i Azure Active Directory-aktiverade Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: iainfou
ms.openlocfilehash: 2a218a48223c81e009b83cb1f129601a8035e18e
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138423"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrera Azure Active Directory med Azure Kubernetes Service

Azure Kubernetes Service (AKS) kan konfigureras för att använda Azure Active Directory (AD) för autentisering av användare. I den här konfigurationen kan du logga in på ett AKS-kluster med hjälp av din Azure Active Directory-autentiseringstoken. Dessutom är klusteradministratörer kan konfigurera Kubernetes rollbaserad åtkomstkontroll (RBAC) baserat på en användares identitet eller directory gruppmedlemskap.

Den här artikeln visar hur du distribuerar förutsättningarna för AKS och Azure AD och sedan hur du distribuerar ett Azure AD-aktiverade-kluster och skapa en grundläggande RBAC-roll i AKS-kluster med Azure portal. Du kan också [slutföra de här stegen med Azure CLI][azure-ad-cli].

Följande begränsningar gäller:

- Azure AD kan bara aktiveras när du skapar en ny, RBAC-aktiverade kluster. Du kan inte aktivera Azure AD i ett befintligt AKS-kluster.
- *Gästen* användare i Azure AD, till exempel som om du använder en federerad inloggning från en annan katalog stöds inte.

## <a name="authentication-details"></a>Autentisering-information

Azure AD-autentisering har angetts för AKS-kluster med OpenID Connect. OpenID Connect är en Identitetslagret som bygger på OAuth 2.0-protokollet. Mer information om OpenID Connect finns i den [öppna ID connect dokumentation][open-id-connect].

Från inuti Kubernetes-klustret används Webhook Tokenautentisering för att verifiera autentiseringstoken. Webhook-tokenautentisering konfigureras och hanteras som en del av AKS-klustret. Mer information om Webhook-tokenautentisering finns i den [webhook authentication dokumentation][kubernetes-webhook].

För att ge Azure AD-autentisering för ett AKS-kluster, skapas två Azure AD-program. Det första programmet är en serverkomponent som tillhandahåller autentisering av användare. Det andra programmet är en klientkomponent som används när du uppmanas av CLI för autentisering. Det här klientprogrammet använder serverprogrammet för den faktiska autentiseringen av autentiseringsuppgifter som tillhandahålls av klienten.

> [!NOTE]
> När du konfigurerar Azure AD för AKS-autentisering kan konfigureras två Azure AD-program. Stegen för att delegera behörigheter för var och en av program som måste utföras av en administratör för Azure-klient.

## <a name="create-server-application"></a>Skapa serverprogram

Första Azure AD-programmet används för att hämta en Azure AD medlemskap i användargrupper. Skapa det här programmet i Azure-portalen.

1. Välj **Azure Active Directory** > **appregistreringar** > **ny registrering**.

    * Ge programmet ett namn, till exempel *AKSAzureADServer*.
    * För **stöds kontotyper**, Välj *konton i den här organisationens katalogen*.
    * Välj *Web* för den **omdirigerings-URI** skriver och ange valfritt formaterad URI-värde som *https://aksazureadserver*.
    * Välj **registrera** när du är klar.

1. Välj **Manifest** och redigera den `groupMembershipClaims` värde att `"All"`.

    ![Uppdatera medlemskap för alla](media/aad-integration/edit-manifest.png)

    **Spara** uppdateringar när du är klar.

1. I det vänstra navigeringsfältet på Azure AD-programmet väljer **certifikat och hemligheter**.

    * Välj **+ nya klienthemligheten**.
    * Lägg till en nyckelbeskrivning som *AKS Azure AD-server*. Välj en förfallotid och välj **Lägg till**.
    * Anteckna värdet för nyckeln. Det har bara visas den här första gången. När du distribuerar ett Azure AD-aktiverade AKS-kluster kan det här värdet kallas den `Server application secret`.

1. I det vänstra navigeringsfältet på Azure AD-programmet väljer **API-behörigheter**, sedan välja att **+ Lägg till en behörighet**.

    * Under **Microsoft APIs**, Välj *Microsoft Graph*.
    * Välj **delegerade behörigheter**, markera kryssrutan bredvid **Directory > Directory.Read.All (läsa kataloginformation)**.
        * Om en standard delegerad behörighet för **användare > User.Read (logga in och läsa användarprofil)** inte finns, markera den här behörigheten.
    * Välj **programbehörigheter**, markera kryssrutan bredvid **Directory > Directory.Read.All (läsa kataloginformation)**.

        ![Ange graph-behörigheter](media/aad-integration/graph-permissions.png)

    * Välj **Lägg till behörigheter** spara uppdateringarna.

    * Under den **ge medgivande** väljer att **bevilja administratörens godkännande**. Den här knappen är nedtonad och är inte tillgängligt om det aktuella kontot inte är en administratör.

        När behörigheterna har beviljats har, visas följande meddelande i portalen:

        ![Meddelande om lyckad behörigheter](media/aad-integration/permissions-granted.png)

1. I det vänstra navigeringsfältet på Azure AD-programmet väljer **exponerar ett API**, sedan välja att **+ Lägg till ett omfång**.
    
    * Ange en *scopenamn*, *visningsnamn för administratörsmedgivande*, och *beskrivning av administratörsmedgivande*, till exempel *AKSAzureADServer*.
    * Kontrollera att den **tillstånd** är inställd på *aktiverad*.

        ![Exponera appen server som en API för användning med andra tjänster](media/aad-integration/expose-api.png)

    * Välj **Lägg till omfattning**.

1. Gå tillbaka till programmet **översikt** sidan och anteckna den **(klient)-ID: T**. När du distribuerar ett Azure AD-aktiverade AKS-kluster kan det här värdet kallas den `Server application ID`.

   ![Hämta program-ID](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Skapa klientprogram

Andra Azure AD-programmet används när du loggar in med Kubernetes CLI (`kubectl`).

1. Välj **Azure Active Directory** > **appregistreringar** > **ny registrering**.

    * Ge programmet ett namn, till exempel *AKSAzureADClient*.
    * För **stöds kontotyper**, Välj *konton i den här organisationens katalogen*.
    * Välj *Web* för den **omdirigerings-URI** skriver och ange valfritt formaterad URI-värde som *https://aksazureadclient*.
    * Välj **registrera** när du är klar.

1. I det vänstra navigeringsfältet på Azure AD-programmet väljer **API-behörigheter**, sedan välja att **+ Lägg till en behörighet**.

    * Välj **Mina API: er**, Välj din Azure AD-serverprogram som skapade i föregående steg, till exempel *AKSAzureADServer*.
    * Välj **delegerade behörigheter**, markera kryssrutan bredvid din Azure AD-serverapp.

        ![Konfigurera behörigheter för programmet](media/aad-integration/select-api.png)

    * Välj **Lägg till behörigheter**.

    * Under den **ge medgivande** väljer att **bevilja administratörens godkännande**. Den här knappen är nedtonad och är inte tillgängligt om det aktuella kontot inte är en administratör.

        När behörigheterna har beviljats har, visas följande meddelande i portalen:

        ![Meddelande om lyckad behörigheter](media/aad-integration/permissions-granted.png)

1. I det vänstra navigeringsfönstret för Azure AD-programmet, anteckna den **program-ID**. När du distribuerar ett Azure AD-aktiverade AKS-kluster kan det här värdet kallas den `Client application ID`.

   ![Hämta program-ID](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>Hämta klientorganisations-ID

Slutligen hämta ID för din Azure-klient. Det här värdet används när du skapar AKS-klustret.

Azure-portalen väljer du **Azure Active Directory** > **egenskaper** och anteckna den **katalog-ID**. När du skapar ett Azure AD-aktiverade AKS-kluster kan det här värdet kallas den `Tenant ID`.

![Hämta Azure-klient-ID](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Distribuera kluster

Använd den [az gruppen skapa] [ az-group-create] kommando för att skapa en resursgrupp för AKS-klustret.

```azurecli
az group create --name myResourceGroup --location eastus
```

Distribuera kluster med hjälp av den [az aks skapa] [ az-aks-create] kommando. Ersätt värdena i exemplet-kommandot nedan med de värden som samlas in när du skapar Azure AD-program för server app-ID och hemlighet, klientapp-ID och klient-ID:

```azurecli
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --generate-ssh-keys \
  --aad-server-app-id b1536b67-29ab-4b63-b60f-9444d0c15df1 \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 8aaf8bd5-1bdd-4822-99ad-02bfaa63eea7 \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

Det tar några minuter att skapa AKS-kluster.

## <a name="create-rbac-binding"></a>Skapa RBAC-bindning

Innan ett Azure Active Directory-konto kan användas med AKS-kluster, måste en bindning för rollen eller kluster roll-koppling som ska skapas. *Roller* definierar behörigheterna som ska tilldelas, och *bindningar* tillämpa dem till önskade användare. Dessa uppgifter kan tillämpas till ett visst namnområde eller över hela klustret. Mer information finns i [med RBAC-auktorisering][rbac-authorization].

Använd först den [aaz aks get-credentials] [ az-aks-get-credentials] med den `--admin` argumentet att logga in på klustret med administratörsåtkomst.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Skapa sedan en ClusterRoleBinding för en Azure AD-konto som du vill bevilja åtkomst till AKS-klustret. I följande exempel ger konto fullständig åtkomst till alla namnområden i klustret.

- Om användaren du ger RBAC-bindningen för finns i samma Azure AD-klient kan tilldela behörigheter till följd av användarens huvudnamn (UPN). Gå vidare till steg att skapa YAML-manifest för ClusterRuleBinding.

- Om användaren är i en annan Azure AD-klient, fråga efter och använda den *objectId* egenskap i stället. Om det behövs kan du hämta den *objectId* krävs användarens konto med hjälp av den [az ad user show] [ az-ad-user-show] kommando. Ange användarens huvudnamn (UPN) för kontot som krävs:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Skapa en fil som *rbac-aad-user.yaml*, och klistra in följande innehåll. På den sista raden, ersätter *userPrincipalName_or_objectId* med UPN eller objekt-ID beroende på om användaren är i samma Azure AD-klient eller inte.

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

Tillämpa en bindning med hjälp av den [kubectl gäller] [ kubectl-apply] kommandot som visas i följande exempel:

```console
kubectl apply -f rbac-aad-user.yaml
```

En bindning för rollen kan även skapas för alla medlemmar i en Azure AD-grupp. Azure AD-grupper har angetts med grupp-ID för objekt som visas i följande exempel. Skapa en fil som *rbac-aad-group.yaml*, och klistra in följande innehåll. Uppdatera objekt-ID för gruppen med en från Azure AD-klienten:

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
   kind: Group
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

Tillämpa en bindning med hjälp av den [kubectl gäller] [ kubectl-apply] kommandot som visas i följande exempel:

```console
kubectl apply -f rbac-aad-group.yaml
```

Läs mer om hur du skyddar ett Kubernetes-kluster med RBAC [med RBAC-auktorisering][rbac-authorization].

## <a name="access-cluster-with-azure-ad"></a>Åtkomst till klustret med Azure AD

Hämta sedan kontexten för en icke-användare med hjälp av den [aaz aks get-credentials] [ az-aks-get-credentials] kommando.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

När du har kört en `kubectl` kommandot uppmanas du att autentisera med Azure. Följ den på skärmen instruktionerna för att slutföra processen, som visas i följande exempel:

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

När du är klar cachelagras autentiseringstoken. Du är bara reprompted för inloggning när token har upphört att gälla eller Kubernetes-config-fil som skapas på nytt.

Om du ser ett meddelande om auktoriseringsfel efter inloggningen, kontrollera om:
1. Användaren du loggar in som är inte en gäst i Azure AD-instans (det här scenariot är ofta fallet om du använder ett federerat konto från en annan katalog).
2. Användaren är inte medlem i fler än 200.

```console
error: You must be logged in to the server (Unauthorized)
```

## <a name="next-steps"></a>Nästa steg

Om du vill använda Azure AD-användare och grupper för att styra åtkomsten till klusterresurser, se [styra åtkomsten till klusterresurser med rollbaserad åtkomstkontroll och Azure AD-identiteter i AKS][azure-ad-rbac].

Mer information om hur du skyddar Kubernetes-kluster finns i [alternativen för åtkomst och identitet för AKS)][rbac-authorization].

Bästa metoder för identitets- och kontroll, se [bästa praxis för autentisering och auktorisering i AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
