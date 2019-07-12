---
title: Integrera Azure Active Directory med Azure Kubernetes Service
description: Hur du skapar kluster i Azure Active Directory-aktiverade Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: mlearned
ms.openlocfilehash: 80137023643630e8472a70fcca6cb656aeba7123
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616395"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrera Azure Active Directory med Azure Kubernetes Service

Azure Kubernetes Service (AKS) kan konfigureras för att använda Azure Active Directory (Azure AD) för autentisering av användare. I den här konfigurationen kan du logga in på ett AKS-kluster med hjälp av din Azure AD-autentiseringstoken.

Klusteradministratörer kan konfigurera Kubernetes rollbaserad åtkomstkontroll (RBAC) baserat på en användares identitet eller directory gruppmedlemskap.

Den här artikeln förklarar hur du:

- Distribuera förutsättningarna för AKS och Azure AD.
- Distribuera ett Azure AD-aktiverade-kluster.
- Skapa en grundläggande RBAC-roll i AKS-kluster med hjälp av Azure portal.

Du kan också utföra dessa steg med hjälp av den [Azure CLI][azure-ad-cli].

> [!NOTE]
> Azure AD kan bara aktiveras när du skapar ett nytt kluster med RBAC-aktiverade. Du kan inte aktivera Azure AD i ett befintligt AKS-kluster.

## <a name="authentication-details"></a>Autentisering-information

Azure AD-autentisering har angetts för AKS-kluster som har OpenID Connect. OpenID Connect är en Identitetslagret som bygger på OAuth 2.0-protokollet.

Läs mer om OpenID Connect, [bevilja åtkomst till webbprogram med hjälp av OpenID Connect och Azure AD][open-id-connect].

I ett Kubernetes-kluster används autentisering med webhook-token för att autentiseringstoken. Webhook-tokenautentisering konfigureras och hanteras som en del av AKS-klustret.

Mer information om webhook-tokenautentisering finns i den [Webhook Tokenautentisering][kubernetes-webhook] avsnittet i Kubernetes-dokumentationen.

För att ge Azure AD-autentisering för ett AKS-kluster, skapas två Azure AD-program. Det första programmet är en serverkomponent som tillhandahåller autentisering av användare. Det andra programmet är en klientkomponent som används när du uppmanas av CLI för autentisering. Det här klientprogrammet använder serverprogrammet för den faktiska autentiseringen av autentiseringsuppgifter som tillhandahålls av klienten.

> [!NOTE]
> När du konfigurerar Azure AD för AKS-autentisering kan konfigureras två Azure AD-program. Stegen för att delegera behörigheter för varje program måste utföras av en administratör för Azure-klient.

## <a name="create-the-server-application"></a>Skapa serverprogrammet

Första Azure AD-programmet används för att få en användares Azure AD-gruppmedlemskap. Skapa det här programmet i Azure portal:

1. Välj **Azure Active Directory** > **appregistreringar** > **ny registrering**.

    a. Ge programmet ett namn, till exempel *AKSAzureADServer*.

    b. För **stöds kontotyper**väljer **konton i den här organisationens katalogen**.
    
    c. Välj **Web** för omdirigerings-URI Skriv och ange valfritt värde URI-format, till exempel *https://aksazureadserver* .

    d. Välj **registrera** när du är klar.

2. Välj **Manifest**, och redigera den **groupMembershipClaims:** värde som **alla**. När du är klar med uppdateringar väljer **spara**.

    ![Uppdatera medlemskap för alla](media/aad-integration/edit-manifest.png)

3. I den vänstra rutan i Azure AD-programmet väljer **certifikat och hemligheter**.

    a. Välj **+ nya klienthemligheten**.

    b. Lägg till en nyckelbeskrivning som *AKS Azure AD-server*. Välj en förfallotid och välj sedan **Lägg till**.

    c. Anteckna värdet för nyckeln, som visas endast just nu. När du distribuerar ett Azure AD-aktiverade AKS-kluster kan kallas det här värdet serverhemlighet för programmet.

4. I den vänstra rutan i Azure AD-programmet väljer **API-behörigheter**, och välj sedan **+ Lägg till en behörighet**.

    a. Under **Microsoft APIs**väljer **Microsoft Graph**.

    b. Välj **delegerade behörigheter**, och välj sedan kryssrutan bredvid **Directory > Directory.Read.All (läsa kataloginformation)** .

    c. Om en standard delegerad behörighet för **användare > User.Read (logga in och läsa användarprofil)** inte finns, markera kryssrutan bredvid den.

    d. Välj **programbehörigheter**, och välj sedan kryssrutan bredvid **Directory > Directory.Read.All (läsa kataloginformation)** .

    ![Ange graph-behörigheter](media/aad-integration/graph-permissions.png)

    e. Välj **Lägg till behörigheter** spara uppdateringarna.

    f. Under **ge medgivande**väljer **bevilja administratörens godkännande**. Den här knappen är inte tillgänglig om det aktuella kontot inte är en Innehavaradministratör.

    När behörigheterna har har beviljats, visas följande meddelande i portalen:

   ![Meddelande om lyckad behörigheter](media/aad-integration/permissions-granted.png)

5. I den vänstra rutan i Azure AD-programmet väljer **exponerar ett API**, och välj sedan **+ Lägg till ett omfång**.
    
    a. Ange en **scopenamn**, en **visningsnamn för administratörsmedgivande**, och sedan en **beskrivning av administratörsmedgivande** som *AKSAzureADServer*.

    b. Se till att **tillstånd** är inställd på **aktiverad**.

    ![Exponera appen server som en API för användning med andra tjänster](media/aad-integration/expose-api.png)

    c. Välj **Lägg till omfattning**.

6. Gå tillbaka till programmet **översikt** sidan och anteckna den **(klient)-ID: T**. När du distribuerar ett Azure AD-aktiverade AKS-kluster kan kallas det här värdet server-program-ID.

    ![Hämta program-ID](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>Skapa klientprogrammet

Andra Azure AD-programmet används när du loggar in med Kubernetes CLI (kubectl).

1. Välj **Azure Active Directory** > **appregistreringar** > **ny registrering**.

    a. Ge programmet ett namn, till exempel *AKSAzureADClient*.

    b. För **stöds kontotyper**väljer **konton i den här organisationens katalogen**.

    c. Välj **Web** för omdirigerings-URI Skriv och ange ett värde för URI-formaterad som *https://aksazureadclient* .

    d. Välj **registrera** när du är klar.

2. I den vänstra rutan i Azure AD-programmet väljer **API-behörigheter**, och välj sedan **+ Lägg till en behörighet**.

    a. Välj **Mina API: er**, och välj sedan ditt program för Azure AD-server som skapade i föregående steg, till exempel *AKSAzureADServer*.

    b. Välj **delegerade behörigheter**, och välj sedan kryssrutan bredvid din Azure AD-serverapp.

    ![Konfigurera behörigheter för programmet](media/aad-integration/select-api.png)

    c. Välj **Lägg till behörigheter**.

    d. Under **ge medgivande**väljer **bevilja administratörens godkännande**. Den här knappen är inte tillgängligt om det aktuella kontot inte är en Innehavaradministratör. När behörigheterna har beviljats, visas följande meddelande i portalen:

    ![Meddelande om lyckad behörigheter](media/aad-integration/permissions-granted.png)

3. I den vänstra rutan i Azure AD-programmet väljer **autentisering**.

    - Under **standard klienttyp**väljer **Ja** till **hantera klienten som en offentlig klient**.

5. I den vänstra rutan i Azure AD-programmet, Observera program-ID. När du distribuerar ett Azure AD-aktiverade AKS-kluster kan kallas det här värdet programmets klient-ID.

   ![Hämta program-ID](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>Hämta klient-ID

Nu ska hämta ID för din Azure-klient. Det här värdet används när du skapar AKS-klustret.

Azure-portalen väljer du **Azure Active Directory** > **egenskaper** och anteckna den **katalog-ID**. När du skapar ett Azure AD-aktiverade AKS-kluster kan kallas det här värdet klient-ID.

![Hämta Azure-klient-ID](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Distribuera AKS-kluster

Använd den [az gruppen skapa][az-group-create] kommando för att skapa en resursgrupp för AKS-klustret.

```azurecli
az group create --name myResourceGroup --location eastus
```

Använd den [az aks skapa][az-aks-create] kommando för att distribuera AKS-klustret. Ersätt värdena i kommandot i följande exempel. Använda de värden som samlas in när du har skapat Azure AD-program för server app-ID, apphemlighet, klientapp-ID och klient-ID.

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

Det tar några minuter att skapa ett AKS-kluster.

## <a name="create-an-rbac-binding"></a>Skapa en bindning för RBAC

Innan du använder ett Azure Active Directory-konto med ett AKS-kluster, måste du skapa rollen bindningen eller kluster roll-bindning. Rollerna definierar de behörigheter som ska tilldelas och bindningar koppla dem till önskade användare. Dessa uppgifter kan tillämpas till ett visst namnområde eller över hela klustret. Mer information finns i [med RBAC-auktorisering][rbac-authorization].

Använd först den [aaz aks get-credentials][az-aks-get-credentials] med den `--admin` argumentet att logga in på klustret med administratörsåtkomst.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Skapa sedan ClusterRoleBinding för en Azure AD-konto som du vill bevilja åtkomst till AKS-klustret. I följande exempel ger konto fullständig åtkomst till alla namnområden i klustret:

- Om användaren du ger RBAC-bindningen för finns i samma Azure AD-klient kan tilldela behörigheter till följd av användarens huvudnamn (UPN). Gå vidare till steg att skapa YAML-manifest för ClusterRoleBinding.

- Om användaren är i en annan Azure AD-klient, fråga efter och använda den **objectId** egenskap i stället. Om det behövs kan hämta objectId för användarkontot som krävs genom att använda den [az ad user show][az-ad-user-show] kommando. Ange användarens huvudnamn (UPN) för kontot som krävs:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Skapa en fil som *rbac-aad-user.yaml*, och klistra in följande innehåll. På den sista raden, ersätter **userPrincipalName_or_objectId** med UPN eller objekt-ID. Valet beror på om användaren är i samma Azure AD-klient eller inte.

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

Tillämpa bindningen med hjälp av den [kubectl gäller][kubectl-apply] kommandot som visas i följande exempel:

```console
kubectl apply -f rbac-aad-user.yaml
```

En bindning för rollen kan även skapas för alla medlemmar i en Azure AD-grupp. Azure AD-grupper har angetts med hjälp av grupp objekt-ID som visas i följande exempel.

Skapa en fil som *rbac-aad-group.yaml*, och klistra in följande innehåll. Uppdatera objekt-ID för gruppen med en från Azure AD-klienten:

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

Tillämpa bindningen med hjälp av den [kubectl gäller][kubectl-apply] kommandot som visas i följande exempel:

```console
kubectl apply -f rbac-aad-group.yaml
```

Läs mer om hur du skyddar ett Kubernetes-kluster med RBAC [med RBAC-auktorisering][rbac-authorization].

## <a name="access-the-cluster-with-azure-ad"></a>Åtkomst till klustret med Azure AD

Hämta kontext för icke-administratörsanvändare med hjälp av den [aaz aks get-credentials][az-aks-get-credentials] kommando.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

När du har kört den `kubectl` kommandot uppmanas du att autentisera med hjälp av Azure. Följ den på skärmen instruktionerna för att slutföra processen, som visas i följande exempel:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

När processen är klar, cachelagras autentiseringstoken. Endast uppmanas du att logga in igen när token upphör att gälla eller Kubernetes-konfigurationsfilen har startats.

Om du ser ett meddelande om auktoriseringsfel när du har loggat in, kontrollerar du följande kriterier:

```console
error: You must be logged in to the server (Unauthorized)
```


- Du har definierat lämpliga objekt-ID eller UPN, beroende på om användarkontot är i samma Azure AD-klienten eller inte.
- Användaren är inte medlem i fler än 200.
- Hemligheten som definierats i programmet registreringen för server överensstämmer med värdet som konfigurerats med hjälp av `--aad-server-app-secret`.

## <a name="next-steps"></a>Nästa steg

Om du vill använda Azure AD-användare och grupper för att styra åtkomsten till klusterresurser, se [styra åtkomsten till klusterresurser med rollbaserad åtkomstkontroll och Azure AD-identiteter i AKS][azure-ad-rbac].

Mer information om hur du säkra Kubernetes-kluster finns i [alternativen för åtkomst och identitet för AKS][rbac-authorization].

Läs mer om identitets- och kontroll i [bästa praxis för autentisering och auktorisering i AKS][operator-best-practices-identity].

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
