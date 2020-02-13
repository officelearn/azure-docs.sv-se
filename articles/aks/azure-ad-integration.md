---
title: Integrera Azure Active Directory med Azure Kubernetes-tjänsten
description: Så här skapar du Azure Active Directory-aktiverade Azure Kubernetes service-kluster (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 02/02/2019
ms.author: mlearned
ms.openlocfilehash: 956cfbafeebadaa23ba3a48f7d38c6f95b412bd9
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77158221"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrera Azure Active Directory med Azure Kubernetes-tjänsten

Azure Kubernetes service (AKS) kan konfigureras att använda Azure Active Directory (Azure AD) för användarautentisering. I den här konfigurationen kan du logga in på ett AKS-kluster med hjälp av din Azure AD-autentiseringstoken.

Kluster administratörer kan konfigurera Kubernetes-rollbaserad åtkomst kontroll (RBAC) baserat på användarens identitet eller katalog grupp medlemskap.

Den här artikeln förklarar hur du:

- Distribuera förutsättningarna för AKS och Azure AD.
- Distribuera ett Azure AD-aktiverat kluster.
- Skapa en grundläggande RBAC-roll i AKS-klustret med hjälp av Azure Portal.

Du kan också utföra dessa steg med hjälp av [Azure CLI][azure-ad-cli].

> [!NOTE]
> Azure AD kan bara aktive ras när du skapar ett nytt RBAC-aktiverat kluster. Du kan inte aktivera Azure AD på ett befintligt AKS-kluster.

## <a name="authentication-details"></a>Information om autentisering

Azure AD-autentisering tillhandahålls för AKS-kluster som har OpenID Connect. OpenID Connect är ett identitets lager som byggts ovanpå OAuth 2,0-protokollet.

Mer information om OpenID Connect finns i [bevilja åtkomst till webb program med OpenID Connect och Azure AD][open-id-connect].

I ett Kubernetes-kluster används autentisering med webhook-token för att autentisera tokens. Webhook-token-autentisering konfigureras och hanteras som en del av AKS-klustret.

Mer information om autentisering med webhook-token finns i avsnittet [webhook token Authentication][kubernetes-webhook] i Kubernetes-dokumentationen.

För att tillhandahålla Azure AD-autentisering för ett AKS-kluster skapas två Azure AD-program. Det första programmet är en Server komponent som tillhandahåller användarautentisering. Det andra programmet är en klient komponent som används när du uppmanas att använda CLI för autentisering. Det här klient programmet använder Server programmet för den faktiska autentiseringen av de autentiseringsuppgifter som anges av klienten.

> [!NOTE]
> När du konfigurerar Azure AD för AKS-autentisering konfigureras två Azure AD-program. Stegen för att delegera behörigheter för varje program måste slutföras av en Azure-innehavaradministratör.

## <a name="create-the-server-application"></a>Skapa serverprogrammet

Det första Azure AD-programmet används för att hämta användarens medlemskap i Azure AD-gruppen. Så här skapar du programmet i Azure Portal:

1. Välj **Azure Active Directory** > **Appregistreringar** > **ny registrering**.

    a. Ge programmet ett namn, till exempel *AKSAzureADServer*.

    b. För **konto typer som stöds**väljer du **konton endast i den här organisations katalogen**.
    
    c. Välj **webb** för den omdirigerings-URI-typen och ange sedan ett URI-formaterat värde, t. ex. *https://aksazureadserver* .

    d. Välj **Registrera** när du är klar.

2. Välj **manifest**och redigera sedan **groupMembershipClaims:** Value as **all**. När du är klar med uppdateringarna väljer du **Spara**.

    ![Uppdatera grupp medlemskap till alla](media/aad-integration/edit-manifest.png)

3. I det vänstra fönstret i Azure AD-programmet väljer du **certifikat & hemligheter**.

    a. Välj **+ ny klient hemlighet**.

    b. Lägg till en nyckel beskrivning, till exempel *AKS Azure AD server*. Välj en förfallo tid och välj sedan **Lägg till**.

    c. Anteckna nyckelvärdet, som endast visas för tillfället. När du distribuerar ett Azure AD-aktiverat AKS-kluster kallas det här värdet Server programmets hemlighet.

4. I det vänstra fönstret i Azure AD-programmet väljer du **API-behörigheter**och väljer sedan **+ Lägg till en behörighet**.

    a. Under **Microsoft API: er**väljer du **Microsoft Graph**.

    b. Välj **delegerade behörigheter**och markera sedan kryss rutan bredvid **katalogens > katalog. Read. all (Läs katalog data)** .

    c. Om en standard delegerad behörighet för **användaren > användare. Läs (logga in och läsa användar profil)** inte finns markerar du kryss rutan bredvid den.

    d. Välj **program behörigheter**och markera sedan kryss rutan bredvid **katalogens > katalog. Read. all (Läs katalog data)** .

    ![Ange Graph-behörigheter](media/aad-integration/graph-permissions.png)

    e. Välj **Lägg till behörigheter** för att spara uppdateringarna.

    f. Under **bevilja medgivande**väljer du **bevilja administrativt medgivande**. Den här knappen är inte tillgänglig. det aktuella kontot som används visas inte som innehavaradministratör.

    När behörigheterna har beviljats visas följande meddelande i portalen:

   ![Avisering om lyckade behörigheter har beviljats](media/aad-integration/permissions-granted.png)

5. I det vänstra fönstret i Azure AD-programmet väljer du **exponera ett API**och väljer sedan **+ Lägg till ett omfång**.
    
    a. Ange ett **scopenamn**, ett **visnings namn för administratörs medgivande**och sedan en **Beskrivning av administratörs medgivande** , till exempel *AKSAzureADServer*.

    b. Se till att **State** är inställt på **aktive rad**.

    ![Exponera Server-appen som ett API för användning med andra tjänster](media/aad-integration/expose-api.png)

    c. Välj **Lägg till omfattning**.

6. Gå tillbaka till program **översikts** sidan och anteckna **program-ID: t (klient)** . När du distribuerar ett Azure AD-aktiverat AKS-kluster kallas det här värdet serverns program-ID.

    ![Hämta program-ID](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>Skapa klient programmet

Det andra Azure AD-programmet används när du loggar in med Kubernetes CLI (kubectl).

1. Välj **Azure Active Directory** > **Appregistreringar** > **ny registrering**.

    a. Ge programmet ett namn, till exempel *AKSAzureADClient*.

    b. För **konto typer som stöds**väljer du **konton endast i den här organisations katalogen**.

    c. Välj **webb** för den omdirigerings-URI-typen och ange sedan ett URI-formaterat värde, till exempel *https://aksazureadclient* .

    >[!NOTE]
    >Om du skapar ett nytt RBAC-aktiverat kluster för att stödja Azure Monitor för behållare lägger du till följande två ytterligare omdirigerings-URL: er i listan som **webb** program typer. Det första bas-URL-värdet ska vara `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` och det andra bas-URL-värdet ska vara `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.
    >
    >Om du använder den här funktionen i Azure Kina ska det första bas-URL-värdet vara `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` och det andra bas-URL-värdet ska vara `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.
    >
    >Mer information finns i så här konfigurerar [du funktionen Live data (för hands version)](../azure-monitor/insights/container-insights-livedata-setup.md) för Azure Monitor för behållare och stegen för att konfigurera autentisering i avsnittet [Konfigurera AD-integrerad autentisering](../azure-monitor/insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication) .

    d. Välj **Registrera** när du är klar.

2. I det vänstra fönstret i Azure AD-programmet väljer du **API-behörigheter**och väljer sedan **+ Lägg till en behörighet**.

    a. Välj **Mina API: er**och välj sedan ditt Azure AD server-program som skapades i föregående steg, till exempel *AKSAzureADServer*.

    b. Välj **delegerade behörigheter**och markera sedan kryss rutan bredvid din Azure AD server-app.

    ![Konfigurera program behörigheter](media/aad-integration/select-api.png)

    c. Välj **Lägg till behörigheter**.

    d. Under **bevilja medgivande**väljer du **bevilja administrativt medgivande**. Den här knappen är inte tillgänglig om det aktuella kontot inte är en klient organisations administratör. När behörigheter beviljas visas följande meddelande i portalen:

    ![Avisering om lyckade behörigheter har beviljats](media/aad-integration/permissions-granted.png)

3. Välj **autentisering**i det vänstra fönstret i Azure AD-programmet.

    - Under **standard klient typ**väljer du **Ja** för att **behandla klienten som en offentlig klient**.

5. Notera program-ID: t i det vänstra fönstret i Azure AD-programmet. När du distribuerar ett Azure AD-aktiverat AKS-kluster kallas det här värdet för klient program-ID.

   ![Hämta program-ID: t](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>Hämta klient-ID

Hämta sedan ID: t för din Azure-klient. Det här värdet används när du skapar AKS-klustret.

Från Azure Portal väljer du **Azure Active Directory** > **Egenskaper** och noterar **katalog-ID: t**. När du skapar ett Azure AD-aktiverat AKS-kluster kallas det här värdet för klient-ID.

![Hämta Azure-klient-ID](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Distribuera AKS-klustret

Använd kommandot [AZ Group Create][az-group-create] för att skapa en resurs grupp för AKS-klustret.

```azurecli
az group create --name myResourceGroup --location eastus
```

Använd kommandot [AZ AKS Create][az-aks-create] för att distribuera AKS-klustret. Ersätt sedan värdena i följande exempel kommando. Använd de värden som samlas in när du skapade Azure AD-program för Server App-ID, app Secret, klientens app-ID och klient-ID.

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

## <a name="create-an-rbac-binding"></a>Skapa en RBAC-bindning

> [!NOTE]
> Kluster rollens bindnings namn är Skift läges känsligt.

Innan du använder ett Azure Active Directory-konto med ett AKS-kluster måste du skapa roll bindning eller kluster roll-bindning. Roller definierar behörigheterna som ska beviljas och bindningar tillämpar dem på önskade användare. De här tilldelningarna kan tillämpas på ett angivet namn område eller i hela klustret. Mer information finns i [använda RBAC-auktorisering][rbac-authorization].

Använd först kommandot [AZ AKS get-credentials][az-aks-get-credentials] med argumentet `--admin` för att logga in på klustret med administratörs åtkomst.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Skapa sedan ClusterRoleBinding för ett Azure AD-konto som du vill ge åtkomst till AKS-klustret. I följande exempel ges kontot fullständig åtkomst till alla namn områden i klustret:

- Om användaren som du beviljar RBAC-bindningen för finns i samma Azure AD-klient tilldelar du behörigheter baserat på User Principal Name (UPN). Gå vidare till steget för att skapa YAML-manifestet för ClusterRoleBinding.

- Om användaren finns i en annan Azure AD-klient frågar du efter och använder egenskapen **ObjectID** i stället. Om det behövs kan du hämta objectId för det nödvändiga användar kontot genom att använda kommandot [AZ AD User show][az-ad-user-show] . Ange User Principal Name (UPN) för det begärda kontot:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Skapa en fil, till exempel *RBAC-AAD-User. yaml*, och klistra sedan in följande innehåll. På den sista raden ersätter du **userPrincipalName_or_objectId** med UPN-eller objekt-ID. Valet beror på om användaren är samma Azure AD-klient eller inte.

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

Tillämpa bindningen med hjälp av kommandot [kubectl Apply][kubectl-apply] som visas i följande exempel:

```console
kubectl apply -f rbac-aad-user.yaml
```

En roll bindning kan också skapas för alla medlemmar i en Azure AD-grupp. Azure AD-grupper anges med hjälp av grupp objekt-ID: t, som du ser i följande exempel.

Skapa en fil, till exempel *RBAC-AAD-Group. yaml*, och klistra sedan in följande innehåll. Uppdatera grupp objekt-ID: t med ett från din Azure AD-klient:

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

Tillämpa bindningen med hjälp av kommandot [kubectl Apply][kubectl-apply] som visas i följande exempel:

```console
kubectl apply -f rbac-aad-group.yaml
```

Mer information om hur du skyddar ett Kubernetes-kluster med RBAC finns i [använda RBAC-auktorisering][rbac-authorization].

## <a name="access-the-cluster-with-azure-ad"></a>Få åtkomst till klustret med Azure AD

Hämta kontexten för den icke-administratör som användaren använder kommandot [AZ AKS get-credentials][az-aks-get-credentials] .

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

När du har kört kommandot `kubectl` uppmanas du att autentisera med hjälp av Azure. Följ anvisningarna på skärmen för att slutföra processen, som du ser i följande exempel:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

När processen har avslut ATS cachelagras autentiseringstoken. Du uppmanas bara att logga in igen när token upphör att gälla, eller så återskapas konfigurations filen för Kubernetes.

Om ett fel meddelande visas när du har loggat in, kontrollerar du följande kriterier:

```console
error: You must be logged in to the server (Unauthorized)
```


- Du definierade lämpligt objekt-ID eller UPN, beroende på om användar kontot finns i samma Azure AD-klient eller inte.
- Användaren är inte medlem i fler än 200 grupper.
- Hemligheten som definierats i program registreringen för servern matchar värdet som kon figurer ATS med hjälp av `--aad-server-app-secret`.

## <a name="next-steps"></a>Nästa steg

Om du vill använda Azure AD-användare och-grupper för att kontrol lera åtkomsten till kluster resurser, se [kontrol lera åtkomst till kluster resurser med hjälp av rollbaserad åtkomst kontroll och Azure AD-identiteter i AKS][azure-ad-rbac].

Mer information om hur du skyddar Kubernetes-kluster finns i [åtkomst-och identitets alternativ för AKS][rbac-authorization].

Mer information om identitets-och resurs kontroll finns i [metod tips för autentisering och auktorisering i AKS][operator-best-practices-identity].

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
