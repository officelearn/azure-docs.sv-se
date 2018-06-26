---
title: Integrera Azure Active Directory med Azure Kubernetes-tjänst
description: Så här skapar du Azure Active Directory-aktiverade Azure Kubernetes Service-kluster.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 6/17/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 7d157d50bbcd25edd9cd6693a71fb04535cbeb79
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937389"
---
# <a name="integrate-azure-active-directory-with-aks---preview"></a>Integrera Azure Active Directory med AKS - förhandsgranskning

Azure Kubernetes Service (AKS) kan konfigureras för att använda Azure Active Directory för autentisering av användare. I den här konfigurationen måste du logga in på ett Azure Kubernetes Service-kluster med hjälp av Azure Active Directory-autentiseringstoken. Dessutom kan klusteradministratörer konfigurera Kubernetes rollbaserad åtkomstkontroll baserat på en användare identitets- eller gruppmedlemskap.

Det här dokumentet beskriver skapa alla nödvändiga krav för AKS och Azure AD, distribuera ett Azure AD-aktiverade kluster och skapar en enkel RBAC-roll i AKS-klustret.

> [!IMPORTANT]
> Azure RBAC Kubernetes Service (AKS) och Azure AD-integrering är för närvarande i **preview**. Förhandsversioner görs tillgängliga för dig under förutsättning att du godkänner [kompletterande användningsvillkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).
>

## <a name="authentication-details"></a>Information om autentisering

Azure AD-autentisering har angetts för Azure Kubernetes kluster med OpenID Connect. OpenID Connect är en Identitetslagret som bygger på OAuth 2.0-protokollet. Mer information om OpenID Connect kan hittas i den [öppna ID connect dokumentationen][open-id-connect].

Från inuti klustret Kubernetes används Webhook Tokenautentisering för att verifiera autentiseringstoken. Webhook-tokenautentisering konfigureras och hanteras som en del av klustret AKS. Mer information om Webhook-tokenautentisering finns i den [webhook autentisering dokumentationen][kubernetes-webhook].

> [!NOTE]
> När du konfigurerar Azure AD för autentisering av AKS konfigureras två Azure AD-program. Den här åtgärden måste utföras av en administratör för Azure-klient.

## <a name="create-server-application"></a>Skapa serverprogram

Det första Azure AD-programmet används för att hämta en Azure AD medlemskap i användargrupper.

1. Välj **Azure Active Directory** > **Appregistreringar** > **Ny programregistrering**.

  Namnge programmet, Välj **webbapp / API** för programtyp, och ange något värde för URI: N formateras för **inloggnings-URL**. Välj **skapa** när du är klar.

  ![Skapa Azure AD-registrering](media/aad-integration/app-registration.png)

2. Välj **Manifest** och redigera den `groupMembershipClaims` värde till `"All"`.

  Spara uppdateringarna när du är klar.

  ![Uppdatera medlemskap för alla](media/aad-integration/edit-manifest.png)

3. Tillbaka på Azure AD-program väljer du **inställningar** > **nycklar**.

  Lägg till en nyckel beskrivning, väljer en tidsgräns för förfallodatum och väljer **spara**. Anteckna värdet för nyckeln. När distribuera en Azure AD aktiverat AKS klustret, det här värdet kallas den `Server application secret`.

  ![Hämta den privata nyckeln för programmet](media/aad-integration/application-key.png)

4. Gå tillbaka till Azure AD-program, Välj **inställningar** > **nödvändiga behörigheter** > **Lägg till**  >   **Välj en API** > **Microsoft Graph** > **Välj**.

  Under **PROGRAMBEHÖRIGHETER** Markera kryssrutan bredvid **läsa katalogdata**.

  ![Ange behörigheter för program-diagram](media/aad-integration/read-directory.png)

5. Under **DELEGERADE BEHÖRIGHETER**, markera kryssrutan bredvid **logga in och Läs användarprofil** och **läsa katalogdata**. Spara uppdateringarna gjort en gång.

  ![Ange behörigheter för program-diagram](media/aad-integration/delegated-permissions.png)

6. Välj **klar** och **bevilja med** att slutföra det här steget. Det här steget misslyckas om det aktuella kontot inte är en tenant admin.

  ![Ange behörigheter för program-diagram](media/aad-integration/grant-permissions.png)

7. Återgå till programmet och anteckna den **program-ID**. När du distribuerar ett Azure AD-aktiverade AKS kluster kan det här värdet kallas den `Server application ID`.

  ![Hämta program-ID](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Skapa klientprogrammet

Andra Azure AD-programmet används när du loggar in med Kubernetes CLI (kubectl.)

1. Välj **Azure Active Directory** > **Appregistreringar** > **Ny programregistrering**.

  Namnge programmet, Välj **interna** för programtyp, och ange något värde för URI: N formateras för **omdirigerings-URI**. Välj **skapa** när du är klar.

  ![Skapa AAD-registrering](media/aad-integration/app-registration-client.png)

2. Välj Azure AD-program **inställningar** > **nödvändiga behörigheter** > **Lägg till** > **väljer en API** och Sök efter namnet på programmet skapas i det sista steget i det här dokumentet.

  ![Konfigurera behörigheter för program](media/aad-integration/select-api.png)

3. Markera kryssrutan bredvid de program och klicka på **Välj**.

  ![Välj programslutpunkten för AKS AAD-server](media/aad-integration/select-server-app.png)

4. Välj **klar** och **bevilja med** att slutföra det här steget.

  ![Bevilja behörigheter](media/aad-integration/grant-permissions-client.png)

5. Tillbaka på AD-program anteckna den **program-ID**. När du distribuerar ett Azure AD-aktiverade AKS kluster kan det här värdet kallas den `Client application ID`.

  ![Hämta program-ID](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>Hämta klientorganisations-ID

Slutligen hämta ID för din Azure-klient. Det här värdet används också när du distribuerar AKS klustret.

Välj den Azure-portalen **Azure Active Directory** > **egenskaper** och anteckna den **katalog-ID**. När du distribuerar ett Azure AD-aktiverade AKS kluster kan det här värdet kallas den `Tenant ID`.

![Hämta Azure-klient-ID](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Distribuera kluster

Använd den [az gruppen skapa] [ az-group-create] kommando för att skapa en resursgrupp för AKS klustret.

```azurecli
az group create --name myAKSCluster --location eastus
```

Distribuera kluster med hjälp av den [az aks skapa] [ az-aks-create] kommando. Ersätt värdena i Exempelkommando nedan med de värden som samlas in när du skapar Azure AD-program.

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --generate-ssh-keys --enable-rbac \
  --aad-server-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

## <a name="create-rbac-binding"></a>Skapa RBAC-bindning

Innan ett Azure Active Directory-konto kan användas med AKS kluster, måste en bindning för rollen eller kluster roll bindning skapas.

Använd först den [az aks get-autentiseringsuppgifter] [ az-aks-get-credentials] kommandot med de `--admin` argumentet att logga in på klustret med administratörsåtkomst.

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster --admin
```

Använd följande manifestet för att skapa en ClusterRoleBinding för en Azure AD-kontot. Uppdatera användarnamnet med en från Azure AD-klienten. Det här exemplet ger kontot fullständig åtkomst till alla namnområden i klustret.

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
  name: "user@contoso.com"
```

En bindning för rollen kan även skapas för alla medlemmar i en Azure AD-grupp. Följande manifestet ger alla medlemmar i den `kubernetes-admin` gruppen administratörsåtkomst till klustret.

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
   name: "kubernetes-admin"
```

Mer information om hur du skyddar ett Kubernetes kluster med RBAC finns [med RBAC-auktorisering][rbac-authorization].

## <a name="access-cluster-with-azure-ad"></a>Åtkomst till klustret med Azure AD

Sedan hämtar kontexten för en icke-användare med hjälp av den [az aks get-autentiseringsuppgifter] [ az-aks-get-credentials] kommando.

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster
```

När du har kört alla kommandon kubectl, uppmanas du att autentisera med Azure. Följ den på skärmen instruktioner.

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-42032720-0   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-1   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-2   Ready     agent     1h        v1.9.6
```

När du är klar, cachelagras autentiseringstoken. Du är bara reprompted att logga in om token har upphört att gälla eller konfigurationsfilen Kubernetes skapas på nytt.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur du skyddar Kubernetes kluster med RBAC med den [med RBAC-auktorisering] [ rbac-authorization] dokumentation.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[rbac-authorization]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]: ../active-directory/develop/active-directory-protocols-openid-connect-code.md
