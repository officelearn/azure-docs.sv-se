---
title: Integrera Azure Active Directory med Azure Kubernetes Service
description: Hur du skapar Azure Active Directory-aktiverade Azure Kubernetes Service-kluster.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 6/17/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 48f27a00f12c14b21edbc3d5ae7cc068e7297e7d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427483"
---
# <a name="integrate-azure-active-directory-with-aks---preview"></a>Integrera Azure Active Directory med AKS - förhandsversion

Azure Kubernetes Service (AKS) kan konfigureras för att använda Azure Active Directory för autentisering av användare. I den här konfigurationen måste du logga in på ett Azure Kubernetes Service-kluster med din Azure Active Directory-autentiseringstoken. Dessutom är klusteradministratörer kan konfigurera Kubernetes rollbaserad åtkomstkontroll utifrån en identitet eller directory medlemskap i användargrupper.

Det här dokumentet beskriver skapa alla nödvändiga krav för AKS och Azure AD, distribuerar ett Azure AD-aktiverade-kluster och skapa en enkel RBAC-roll i AKS-kluster. Observera att befintliga icke-RBAC aktiverat AKS-kluster för närvarande inte kan uppdateras för att använda RBAC.

> [!IMPORTANT]
> Azure Kubernetes Service (AKS) RBAC och Azure AD-integrering är för närvarande i **förhandsversion**. Förhandsversioner görs tillgängliga för dig under förutsättning att du godkänner [kompletterande användningsvillkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).
>

## <a name="authentication-details"></a>Autentisering-information

Azure AD-autentisering har angetts för Azure Kubernetes-kluster med OpenID Connect. OpenID Connect är en Identitetslagret som bygger på OAuth 2.0-protokollet. Mer information om OpenID Connect finns i den [öppna ID connect dokumentation][open-id-connect].

Från inuti Kubernetes-klustret används Webhook Tokenautentisering för att verifiera autentiseringstoken. Webhook-tokenautentisering konfigureras och hanteras som en del av AKS-klustret. Mer information om Webhook-tokenautentisering finns i den [webhook authentication dokumentation][kubernetes-webhook].

> [!NOTE]
> När du konfigurerar Azure AD för AKS-autentisering kan konfigureras två Azure AD-program. Den här åtgärden måste utföras av en administratör för Azure-klient.

## <a name="create-server-application"></a>Skapa serverprogram

Första Azure AD-programmet används för att hämta en Azure AD medlemskap i användargrupper.

1. Välj **Azure Active Directory** > **Appregistreringar** > **Ny programregistrering**.

  Ge programmet ett namn, Välj **webbapp / API** som programtyp, och ange något formaterad URI-värde för **inloggnings-URL**. Välj **skapa** när du är klar.

  ![Skapa Azure AD-registrering](media/aad-integration/app-registration.png)

2. Välj **Manifest** och redigera den `groupMembershipClaims` värde att `"All"`.

  Spara uppdateringarna när du är klar.

  ![Uppdatera medlemskap för alla](media/aad-integration/edit-manifest.png)

3. Tillbaka på Azure AD-programmet väljer **inställningar** > **nycklar**.

  Lägg till en nyckelbeskrivning, väljer en tidsgräns för förfallodatum och väljer **spara**. Anteckna värdet för nyckeln. När distribuera en Azure AD aktiverats AKS-kluster kan det här värdet kallas den `Server application secret`.

  ![Hämta den privata nyckeln för program](media/aad-integration/application-key.png)

4. Gå tillbaka till Azure AD-programmet väljer **inställningar** > **nödvändiga behörigheter** > **Lägg till**  >   **Välj en API** > **Microsoft Graph** > **Välj**.

  ![Välj graph API](media/aad-integration/graph-api.png)

5. Under **PROGRAMBEHÖRIGHETER** sätt en bock bredvid **läsa katalogdata**.

  ![Ange program graph-behörigheter](media/aad-integration/read-directory.png)

6. Under **DELEGERADE BEHÖRIGHETER**, markera kryssrutan bredvid **logga in och läsa användarprofil** och **läsa katalogdata**. Spara uppdateringarna när klar.

  ![Ange program graph-behörigheter](media/aad-integration/delegated-permissions.png)

7. Välj **klar**, Välj *Microsoft Graph* från listan över API: er, Välj **bevilja behörigheter**. Det här steget misslyckas om det aktuella kontot inte är en administratör.

  ![Ange program graph-behörigheter](media/aad-integration/grant-permissions.png)

8. Gå tillbaka till programmet och anteckna den **program-ID**. När du distribuerar ett Azure AD-aktiverade AKS-kluster kan det här värdet kallas den `Server application ID`.

  ![Hämta program-ID](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Skapa klientprogram

Andra Azure AD-programmet används när du loggar in med Kubernetes CLI (kubectl).

1. Välj **Azure Active Directory** > **Appregistreringar** > **Ny programregistrering**.

  Ge programmet ett namn, Välj **interna** som programtyp, och ange något formaterad URI-värde för **omdirigerings-URI**. Välj **skapa** när du är klar.

  ![Skapa AAD-registrering](media/aad-integration/app-registration-client.png)

2. Azure AD-programmet väljer **inställningar** > **nödvändiga behörigheter** > **Lägg till** > **väljer en API: et** och Sök efter namnet på serverprogram som skapats i det sista steget i det här dokumentet.

  ![Konfigurera behörigheter för programmet](media/aad-integration/select-api.png)

3. Markera kryssrutan bredvid den och klicka på **Välj**.

  ![Välj program för AKS AAD serverslutpunkt](media/aad-integration/select-server-app.png)

4. Välj **klar** och **bevilja behörigheter** att slutföra det här steget.

  ![Bevilja behörigheter](media/aad-integration/grant-permissions-client.png)

5. Tillbaka på AD-program, anteckna den **program-ID**. När du distribuerar ett Azure AD-aktiverade AKS-kluster kan det här värdet kallas den `Client application ID`.

  ![Hämta program-ID](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>Hämta klientorganisations-ID

Slutligen hämta ID för din Azure-klient. Det här värdet används också när du distribuerar AKS-kluster.

Azure-portalen väljer du **Azure Active Directory** > **egenskaper** och anteckna den **katalog-ID**. När du distribuerar ett Azure AD-aktiverade AKS-kluster kan det här värdet kallas den `Tenant ID`.

![Hämta Azure-klient-ID](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Distribuera kluster

Använd den [az gruppen skapa] [ az-group-create] kommando för att skapa en resursgrupp för AKS-klustret.

```azurecli
az group create --name myAKSCluster --location eastus
```

Distribuera kluster med hjälp av den [az aks skapa] [ az-aks-create] kommando. Ersätt värdena i exemplet-kommandot nedan med de värden som samlas in när du skapar Azure AD-program.

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --generate-ssh-keys --enable-rbac \
  --aad-server-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

## <a name="create-rbac-binding"></a>Skapa RBAC-bindning

Innan ett Azure Active Directory-konto kan användas med AKS-kluster, måste en bindning för rollen eller kluster roll-koppling som ska skapas.

Använd först den [aaz aks get-credentials] [ az-aks-get-credentials] med den `--admin` argumentet för inloggning till klustret med administratörsåtkomst.

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster --admin
```

Använd följande manifestet för att skapa en ClusterRoleBinding för ett Azure AD-konto. Uppdatera användarnamnet med en från Azure AD-klienten. Det här exemplet ger konto fullständig åtkomst till alla namnområden i klustret.

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

En bindning för rollen kan även skapas för alla medlemmar i en Azure AD-grupp. Azure AD-grupper har angetts med grupp-objekt-ID.

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

Läs mer om hur du skyddar ett Kubernetes-kluster med RBAC [med RBAC-auktorisering][rbac-authorization].

## <a name="access-cluster-with-azure-ad"></a>Åtkomst till klustret med Azure AD

Hämta sedan kontexten för en icke-användare med hjälp av den [aaz aks get-credentials] [ az-aks-get-credentials] kommando.

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster
```

När alla kubectl-kommandot har körts kan uppmanas du att autentisera med Azure. Följ den på skärmen instruktioner.

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-42032720-0   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-1   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-2   Ready     agent     1h        v1.9.6
```

När du är klar cachelagras autentiseringstoken. Du är bara reprompted för inloggning när token har upphört att gälla eller Kubernetes-config-fil som skapas på nytt.

Om du ser ett meddelande om auktoriseringsfel när inloggningen, kontrollerar du att användaren du loggar in som är inte en gäst i Azure AD (det är ofta fallet om du använder en federerad inloggning från en annan katalog).
```console
error: You must be logged in to the server (Unauthorized)
```


## <a name="next-steps"></a>Nästa steg

Läs mer om hur du skyddar Kubernetes-kluster med hjälp av rollbaserad Åtkomstkontroll med den [med RBAC-auktorisering] [ rbac-authorization] dokumentation.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[rbac-authorization]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]: ../active-directory/develop/active-directory-protocols-openid-connect-code.md
