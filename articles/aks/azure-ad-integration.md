---
title: Integrera Azure Active Directory med Azure Kubernetes Service
description: Skapa Aks-kluster (Azure Active Directory-aktiverade Azure Kubernetes Service)
services: container-service
ms.topic: article
ms.date: 02/02/2019
ms.openlocfilehash: 0476acadf5af3a3e2c470fe6c08ebbd355653e22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596597"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrera Azure Active Directory med Azure Kubernetes Service

Azure Kubernetes Service (AKS) kan konfigureras för att använda Azure Active Directory (Azure AD) för användarautentisering. I den här konfigurationen kan du logga in på ett AKS-kluster med hjälp av din Azure AD-autentiseringstoken.

Klusteradministratörer kan konfigurera Kubernetes rollbaserade åtkomstkontroll (RBAC) baserat på en användares identitet eller kataloggruppmedlemskap.

I den här artikeln beskrivs hur du:

- Distribuera förutsättningarna för AKS och Azure AD.
- Distribuera ett Azure AD-aktiverat kluster.
- Skapa en grundläggande RBAC-roll i AKS-klustret med hjälp av Azure-portalen.

Du kan också slutföra dessa steg med hjälp av [Azure CLI][azure-ad-cli].

> [!NOTE]
> Azure AD kan bara aktiveras när du skapar ett nytt RBAC-aktiverat kluster. Du kan inte aktivera Azure AD i ett befintligt AKS-kluster.

## <a name="authentication-details"></a>Information om autentisering

Azure AD-autentisering tillhandahålls till AKS-kluster som har OpenID Connect. OpenID Connect är ett identitetslager som är byggt ovanpå OAuth 2.0-protokollet.

Mer information om OpenID Connect finns i [Auktorisera åtkomst till webbprogram med OpenID Connect och Azure AD][open-id-connect].

I ett Kubernetes-kluster används webhook-tokenautentisering för autentiseringstoken. Webhook token autentisering konfigureras och hanteras som en del av AKS-klustret.

Mer information om webhook-tokenautentisering finns i avsnittet [Webhook Token Authentication][kubernetes-webhook] i Kubernetes-dokumentationen.

För att tillhandahålla Azure AD-autentisering för ett AKS-kluster skapas två Azure AD-program. Det första programmet är en serverkomponent som tillhandahåller användarautentisering. Det andra programmet är en klientkomponent som används när du uppmanas av CLI för autentisering. Det här klientprogrammet använder serverprogrammet för faktisk autentisering av autentiseringsuppgifterna som tillhandahålls av klienten.

> [!NOTE]
> När du konfigurerar Azure AD för AKS-autentisering konfigureras två Azure AD-program. Stegen för att delegera behörigheter för varje program måste fyllas i av en Azure-klientadministratör.

## <a name="create-the-server-application"></a>Skapa serverprogrammet

Det första Azure AD-programmet tillämpas för att hämta en användares Azure AD-gruppmedlemskap. Så här skapar du det här programmet i Azure-portalen:

1. Välj **Azure Active Directory** > **App registreringar** > **Ny registrering**.

    a. Ge programmet ett namn, till exempel *AKSAzureADServer*.

    b. För **kontotyper som stöds**väljer du Konton i den här **organisationskatalogen**.
    
    c. Välj **Webb** för typen Omdirigera URI och ange sedan ett URI-formaterat värde, till exempel *https://aksazureadserver*.

    d. Välj **Registrera** när du är klar.

2. Välj **Manifest**och redigera sedan **gruppenMedlemskapKlamerna:** värde som **alla**. När du är klar med uppdateringarna väljer du **Spara**.

    ![Uppdatera gruppmedlemskap till alla](media/aad-integration/edit-manifest.png)

3. I den vänstra rutan i Azure AD-programmet väljer du **Certifikat & hemligheter**.

    a. Välj **+ Ny klienthemlighet**.

    b. Lägg till en nyckelbeskrivning, till exempel *AKS Azure AD-server*. Välj en förfallotid och välj sedan **Lägg till**.

    c. Observera nyckelvärdet, som visas först just nu. När du distribuerar ett Azure AD-aktiverat AKS-kluster kallas det här värdet för serverprogrammet hemligt.

4. I den vänstra rutan i Azure AD-programmet väljer du **API-behörigheter**och väljer sedan **+ Lägg till en behörighet**.

    a. Under **Microsoft API:er**väljer du **Microsoft Graph**.

    b. Välj **Delegerade behörigheter**och markera kryssrutan bredvid **Katalog > Katalog.Read.All (Läs katalogdata)**.

    c. Om det inte finns en standarddelad delegerad behörighet för **Användare > User.Read (Logga in och läs användarprofil)** markerar du kryssrutan bredvid den.

    d. Välj **Programbehörigheter**och markera kryssrutan bredvid **Katalog > Katalog.Read.All (Läs katalogdata)**.

    ![Ange diagrambehörigheter](media/aad-integration/graph-permissions.png)

    e. Välj **Lägg till behörigheter** för att spara uppdateringarna.

    f. Under **Bevilja medgivande**väljer du **Bevilja administratörssamtycke**. Den här knappen är inte tillgänglig det aktuella kontot som används visas inte som klientadministratör.

    När behörigheter har beviljats visas följande meddelande i portalen:

   ![Meddelande om beviljade lyckade behörigheter](media/aad-integration/permissions-granted.png)

5. I den vänstra rutan i Azure AD-programmet väljer du **Exponera ett API**och väljer sedan + Lägg till ett **scope**.
    
    a. Ange ett **scopenamn**, ett **administratörsmedgivandenamn**och sedan en **administratörsmedgivandebeskrivning** som *AKSAzureADServer*.

    b. Kontrollera att **tillståndet** är inställt **på Aktiverad**.

    ![Exponera serverappen som ett API för användning med andra tjänster](media/aad-integration/expose-api.png)

    c. Välj **Lägg till omfattning**.

6. Gå tillbaka till **sidan** Översikt för programmet och notera **program-ID:t (klient)**. När du distribuerar ett AZURE AD-aktiverat AKS-kluster kallas det här värdet serverprogram-ID.

    ![Hämta program-ID](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>Skapa klientprogrammet

Det andra Azure AD-programmet används när du loggar in med Kubernetes CLI (kubectl).

1. Välj **Azure Active Directory** > **App registreringar** > **Ny registrering**.

    a. Ge programmet ett namn, till exempel *AKSAzureADClient*.

    b. För **kontotyper som stöds**väljer du Konton i den här **organisationskatalogen**.

    c. Välj **Webb** för typen Omdirigera URI och ange sedan ett URI-formaterat värde, *https://aksazureadclient*till exempel .

    >[!NOTE]
    >Om du skapar ett nytt RBAC-aktiverat kluster för att stödja Azure Monitor för behållare **Web** lägger du till följande två ytterligare omdirigeringsadresser i den här listan som webbprogramtyper. Det första bas-URL-värdet ska vara `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` och `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`det andra bas-URL-värdet ska vara .
    >
    >Om du använder den här funktionen i Azure Kina `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` ska det första bas-URL-värdet vara och det andra bas-URL-värdet ska vara `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.
    >
    >Mer information finns i [Så här konfigurerar du funktionen Live Data (förhandsversion)](../azure-monitor/insights/container-insights-livedata-setup.md) för Azure Monitor för behållare och stegen för att konfigurera autentisering under avsnittet [Konfigurera AD-integrerad autentisering.](../azure-monitor/insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication)

    d. Välj **Registrera** när du är klar.

2. I den vänstra rutan i Azure AD-programmet väljer du **API-behörigheter**och väljer sedan **+ Lägg till en behörighet**.

    a. Välj **Mina API:er**och välj sedan ditt Azure AD-serverprogram som skapats i föregående steg, till exempel *AKSAzureADServer*.

    b. Välj **Delegerade behörigheter**och markera kryssrutan bredvid din Azure AD-serverapp.

    ![Konfigurera programbehörigheter](media/aad-integration/select-api.png)

    c. Välj **Lägg till behörigheter**.

    d. Under **Bevilja medgivande**väljer du **Bevilja administratörssamtycke**. Den här knappen är inte tillgänglig om det aktuella kontot inte är en klientadministratör. När behörigheter beviljas visas följande meddelande i portalen:

    ![Meddelande om beviljade lyckade behörigheter](media/aad-integration/permissions-granted.png)

3. Välj **Autentisering**i den vänstra rutan i Azure AD-programmet .

    - Under **Standardklienttyp**väljer du **Ja** för att **behandla klienten som en offentlig klient**.

5. I den vänstra rutan i Azure AD-programmet noterar du program-ID:t. När du distribuerar ett Azure AD-aktiverat AKS-kluster kallas det här värdet klientprogram-ID.

   ![Hämta program-ID:et](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>Hämta klient-ID

Hämta sedan ID:et för din Azure-klientorganisation. Det här värdet används när du skapar AKS-klustret.

Välj Azure Active > **Directory-egenskaper** i **Azure-portalen**och notera **katalog-ID.** När du skapar ett Azure AD-aktiverat AKS-kluster kallas det här värdet klient-ID.

![Hämta Azure-klient-ID](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Distribuera AKS-klustret

Använd kommandot [az group create][az-group-create] för att skapa en resursgrupp för AKS-klustret.

```azurecli
az group create --name myResourceGroup --location eastus
```

Använd kommandot [az aks create][az-aks-create] för att distribuera AKS-klustret. Ersätt sedan värdena i följande exempelkommando. Använd de värden som samlades in när du skapade Azure AD-programmen för serverapp-ID, apphemlighet, klientapp-ID och klient-ID.

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

Ett AKS-kluster tar några minuter att skapa.

## <a name="create-an-rbac-binding"></a>Skapa en RBAC-bindning

> [!NOTE]
> Klusterrollbindningsnamnet är skiftlägeskänsligt.

Innan du använder ett Azure Active Directory-konto med ett AKS-kluster måste du skapa rollbindning eller klusterrollbindning. Roller definierar behörigheterna att bevilja och bindningar tillämpar dem på önskade användare. Dessa tilldelningar kan tillämpas på ett visst namnområde eller i hela klustret. Mer information finns i [Använda RBAC-auktorisering][rbac-authorization].

Använd först kommandot [az aks get-credentials][az-aks-get-credentials] med `--admin` argumentet att logga in i klustret med administratörsåtkomst.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Skapa sedan ClusterRoleBinding för ett Azure AD-konto som du vill bevilja åtkomst till AKS-klustret. I följande exempel får kontot fullständig åtkomst till alla namnområden i klustret:

- Om användaren som du beviljar RBAC-bindningen för finns i samma Azure AD-klient, tilldela behörigheter baserat på användarens huvudnamn (UPN). Gå vidare till steget för att skapa YAML-manifestet för ClusterRoleBinding.

- Om användaren finns i en annan Azure AD-klient frågar du efter och använder egenskapen **objectId** i stället. Om det behövs hämtar du objectId för det nödvändiga användarkontot med kommandot [az ad user show.][az-ad-user-show] Ange användarens huvudnamn (UPN) för det konto som krävs:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Skapa en fil, till exempel *rbac-aad-user.yaml,* och klistra sedan in följande innehåll. På den sista raden ersätter **du userPrincipalName_or_objectId** med UPN- eller objekt-ID. Valet beror på om användaren är samma Azure AD-klient eller inte.

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

Använd bindningen med kommandot [kubectl apply][kubectl-apply] som visas i följande exempel:

```console
kubectl apply -f rbac-aad-user.yaml
```

En rollbindning kan också skapas för alla medlemmar i en Azure AD-grupp. Azure AD-grupper anges med hjälp av gruppobjekt-ID, som visas i följande exempel.

Skapa en fil, till exempel *rbac-aad-group.yaml,* och klistra sedan in följande innehåll. Uppdatera gruppobjekt-ID:n med ett från din Azure AD-klient:

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

Använd bindningen med kommandot [kubectl apply][kubectl-apply] som visas i följande exempel:

```console
kubectl apply -f rbac-aad-group.yaml
```

Mer information om hur du skyddar ett Kubernetes-kluster med RBAC finns i [Använda RBAC-auktorisering][rbac-authorization].

## <a name="access-the-cluster-with-azure-ad"></a>Få tillgång till klustret med Azure AD

Hämta kontexten för icke-admin-användaren med kommandot [az aks get-credentials.][az-aks-get-credentials]

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

När du `kubectl` har kört kommandot uppmanas du att autentisera med hjälp av Azure. Följ anvisningarna på skärmen för att slutföra processen, som visas i följande exempel:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

När processen är klar cachelagras autentiseringstoken. Du uppmanas bara att logga in igen när token upphör att gälla, eller kubernetes config-filen återskapas.

Om ett auktoriseringsfelmeddelande visas när du har loggat in kontrollerar du följande villkor:

```console
error: You must be logged in to the server (Unauthorized)
```


- Du har definierat rätt objekt-ID eller UPN, beroende på om användarkontot finns i samma Azure AD-klient eller inte.
- Användaren är inte medlem i mer än 200 grupper.
- Hemligheten som definieras i programregistreringen för servern `--aad-server-app-secret`matchar värdet som konfigurerats med .

## <a name="next-steps"></a>Nästa steg

Information om hur du använder Azure AD-användare och -grupper för att kontrollera åtkomsten till klusterresurser finns i [Kontrollera åtkomst till klusterresurser med hjälp av rollbaserad åtkomstkontroll och Azure AD-identiteter i AKS][azure-ad-rbac].

Mer information om hur du skyddar Kubernetes-kluster finns i [Åtkomst- och identitetsalternativ för AKS][rbac-authorization].

Mer information om identitets- och resurskontroll finns [i Metodtips för autentisering och auktorisering i AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
