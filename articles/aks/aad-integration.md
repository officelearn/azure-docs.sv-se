---
title: Integrera Azure Active Directory med Azure Kubernetes Service
description: Så här att skapa kluster i Azure Active Directory-aktiverade Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/09/2018
ms.author: iainfou
ms.openlocfilehash: 0dced367f62ab97d62cd4b11758e13a05278442e
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56099266"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrera Azure Active Directory med Azure Kubernetes Service

Azure Kubernetes Service (AKS) kan konfigureras för att använda Azure Active Directory (AD) för autentisering av användare. I den här konfigurationen måste du logga in på ett AKS-kluster med hjälp av din Azure Active Directory-autentiseringstoken. Dessutom är klusteradministratörer kan konfigurera Kubernetes rollbaserad åtkomstkontroll (RBAC) baserat på en identitet eller directory medlemskap i användargrupper.

Den här artikeln visar hur du distribuerar förutsättningarna för AKS och Azure AD och sedan hur du distribuerar ett Azure AD-aktiverade-kluster och skapa en enkel RBAC-roll i AKS-klustret.

Följande begränsningar gäller:

- Azure AD kan bara aktiveras när du skapar en ny, RBAC-aktiverade kluster. Du kan inte aktivera Azure AD i ett befintligt AKS-kluster.
- *Gästen* användare i Azure AD, till exempel som om du använder en federerad inloggning från en annan katalog stöds inte.

## <a name="authentication-details"></a>Autentisering-information

Azure AD-autentisering har angetts för AKS-kluster med OpenID Connect. OpenID Connect är en Identitetslagret som bygger på OAuth 2.0-protokollet. Mer information om OpenID Connect finns i den [öppna ID connect dokumentation][open-id-connect].

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

  Välj **Done** (Klar).

7. Välj *Microsoft Graph* från listan över API: er, Välj **bevilja behörigheter**. Det här steget misslyckas om det aktuella kontot inte är en administratör.

  ![Ange program graph-behörigheter](media/aad-integration/grant-permissions.png)

  När behörigheterna har beviljats har, visas följande meddelande i portalen:

  ![Meddelande om lyckad behörigheter](media/aad-integration/permissions-granted.png)

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

  Välj **klar**

4. Välj din API-server i listan och välj sedan **bevilja behörigheter**:

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
az group create --name myResourceGroup --location eastus
```

Distribuera kluster med hjälp av den [az aks skapa] [ az-aks-create] kommando. Ersätt värdena i exemplet-kommandot nedan med de värden som samlas in när du skapar Azure AD-program.

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

## <a name="create-rbac-binding"></a>Skapa RBAC-bindning

Innan ett Azure Active Directory-konto kan användas med AKS-kluster, måste en bindning för rollen eller kluster roll-koppling som ska skapas. *Roller* definierar behörigheterna som ska tilldelas, och *bindningar* tillämpa dem till önskade användare. Dessa uppgifter kan tillämpas till ett visst namnområde eller över hela klustret. Mer information finns i [med RBAC-auktorisering][rbac-authorization].

Använd först den [aaz aks get-credentials] [ az-aks-get-credentials] med den `--admin` argumentet för inloggning till klustret med administratörsåtkomst.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Använd följande manifestet för att skapa en ClusterRoleBinding för ett Azure AD-konto. Det här exemplet ger konto fullständig åtkomst till alla namnområden i klustret. Skapa en fil som *rbac-aad-user.yaml*, och klistra in följande innehåll. Uppdatera användarnamnet med en från Azure AD-klienten:

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

När alla kubectl-kommandot har körts kan uppmanas du att autentisera med Azure. Följ den på skärmen instruktioner.

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.9.9
aks-nodepool1-79590246-1   Ready     agent     1h        v1.9.9
aks-nodepool1-79590246-2   Ready     agent     1h        v1.9.9
```

När du är klar cachelagras autentiseringstoken. Du är bara reprompted för inloggning när token har upphört att gälla eller Kubernetes-config-fil som skapas på nytt.

Om du ser ett meddelande om auktoriseringsfel efter inloggningen, kontrollera om:
1. Användaren du loggar in som är inte en gäst i Azure AD-instans (det är ofta fallet om du använder en federerad inloggning från en annan katalog).
2. Användaren är inte medlem i fler än 200.

```console
error: You must be logged in to the server (Unauthorized)
```

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du skyddar Kubernetes-kluster med hjälp av rollbaserad Åtkomstkontroll med den [med RBAC-auktorisering] [ rbac-authorization] dokumentation.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[rbac-authorization]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
