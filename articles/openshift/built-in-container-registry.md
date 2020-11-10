---
title: Konfigurera inbyggt behållar register för Azure Red Hat OpenShift 4
description: Konfigurera inbyggt behållar register för Azure Red Hat OpenShift 4
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 917da58c7f5ac2294fc30cd385a4834fde3f5f0b
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414800"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Konfigurera inbyggt behållar register för Azure Red Hat OpenShift 4

I den här artikeln ska du konfigurera det inbyggda registret för behållar avbildningar för ett Azure Red Hat OpenShift-kluster (ARO) 4. Du lär dig följande:

> [!div class="checklist"]
> * Konfigurera Azure AD
> * Konfigurera OpenID Connect
> * Åtkomst till det inbyggda registret för behållar avbildningar

## <a name="prerequisites"></a>Förutsättningar

* Skapa ett kluster genom att följa stegen i [skapa ett Azure Red Hat OpenShift 4-kluster](/azure/openshift/tutorial-create-cluster). Se till att skapa klustret med `--pull-secret` argumentet till `az aro create` .  Detta krävs om du vill konfigurera Azure AD för inloggning, enligt vad som krävs i den här artikeln.
* Anslut till klustret genom att följa stegen i [ansluta till ett Azure Red Hat OpenShift 4-kluster](/azure/openshift/tutorial-connect-cluster).
   * Se till att följa stegen i "installera OpenShift CLI" eftersom vi använder `oc` kommandot senare i den här artikeln.
   * Anteckna kluster konsolens URL, som ser ut så här `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` . Värdena för `<random>` och `<region>` kommer att användas senare i den här artikeln.
   * Anteckna `kubeadmin` autentiseringsuppgifterna. De kommer att användas senare i den här artikeln.

## <a name="set-up-azure-active-directory"></a>Konfigurera Azure Active Directory

Azure Active Directory (Azure AD) implementerar OpenID Connect (OIDC). Med OIDC kan du använda Azure AD för att logga in på ARO-klustret. Följ stegen nedan för att konfigurera Azure AD.

1. Konfigurera en Azure AD-klient genom att följa stegen i [snabb start: Konfigurera en klient](/azure/active-directory/develop/quickstart-create-new-tenant). Ditt Azure-konto kanske redan har en klient. I så fall kan du hoppa över att skapa en om du har tillräcklig behörighet i klienten för att följa stegen. Notera ditt **klient-ID**. Det här värdet kommer att användas senare.
2. Skapa minst en Azure AD-användare genom att följa stegen i [Lägg till eller ta bort användare med hjälp av Azure Active Directory](/azure/active-directory/fundamentals/add-users-azure-active-directory). Du kan använda dessa konton eller ditt eget för att testa programmet. Anteckna e-postadresserna och lösen orden för dessa konton för inloggning.
3. Skapa en ny program registrering i din Azure AD-klient genom att följa stegen i [snabb start: registrera ett program med Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app). 
   1. Återkalla anteckningen i förutsättningarna för värdena för `<random>` och `<region>` . Använd de här värdena för att skapa en URI enligt följande formel:

      `https://oauth-openshift.apps.<random>.<region>.aroapp.io/oauth2callback/openid`
   1. När du når det steg som hanterar fältet **omdirigerings-URI** , anger du URI: n från föregående steg.
   1. Välj **Register** (Registrera).
   1. På sidan **Översikt** för appen noterar du värdet som visas för **program-ID (klient)** som visas här.
      :::image type="content" source="media/built-in-container-registry/azure-ad-app-overview-client-id.png" alt-text="Översikts sida för Azure AD-program.":::

4. Skapa en ny klient hemlighet. 
   1. I den nyligen skapade program registreringen väljer du **certifikat & hemligheter** i det vänstra navigerings fönstret.
   1. Välj **Ny klienthemlighet**.
   1. Ange **en beskrivning** och välj **Lägg till**.
   1. Spara undan det genererade **klient hemlighet** svärdet. Det här värdet kommer att användas senare i artikeln.

### <a name="add-openid-connect-identity-provider"></a>Lägg till OpenID Connect Identity Provider

ARO tillhandahåller ett inbyggt behållar register.  För att få åtkomst till den konfigurerar du en identitetsprovider (IDP) med Azure AD OIDC genom att följa dessa steg.

1. Logga in på webb konsolen OpenShift från din webbläsare som `kubeadmin` .  Det här är samma procedur som används när du utför stegen i [Självstudier: Anslut till ett Azure Red Hat OpenShift 4-kluster](/azure/openshift/tutorial-connect-cluster).
1. I det vänstra navigerings fönstret väljer du inställningar för **administrations**  >  **kluster**.
1. I mitten av sidan väljer du **global konfiguration**.
1. Hitta **OAuth** i kolumnen **konfigurations resurs** i tabellen och markera den.
1. Under **identitets leverantörer** väljer du **Lägg till** och väljer **OpenID Anslut**.
1. Ange **namn** som **OpenID**.  Det här värdet kan redan fyllas i.
1. Ange **klient-ID** och **klient hemlighet** som värden från föregående steg.
1. Följ det här steget för att hitta värdet för **utfärdaren URL**.
   1. Ersätt **\<tenant-id>** med den som sparades under avsnittet **konfigureras Azure Active Directory** i URL: en `https://login.microsoftonline.com/<tenant-id>/v2.0/.well-known/openid-configuration` .
   1. Öppna webb adressen i samma webbläsare som du använde för att interagera med Azure Portal.
   1. Kopiera värdet för egenskaps **utfärdaren** i den RETURNERAde JSON-texten och klistra in den i text rutan med namnet **Issuer-URL**.  **Utfärdarens** värde ser ut ungefär så här `https://login.microsoftonline.com/44p4o433-2q55-474q-on88-4on94469o74n/v2.0` .
1. Gå till slutet av sidan och välj **Lägg till**.
   :::image type="content" source="media/built-in-container-registry/openid-connect-identity-provider.png" alt-text="OpenID Anslut i OpenShift.":::
1. Logga ut från OpenShift-webbkonsolen genom att välja knappen **Kube: admin** överst till höger i webbläsarfönstret och välja **Logga ut**.

### <a name="access-the-built-in-container-image-registry"></a>Åtkomst till det inbyggda registret för behållar avbildningar

Följande instruktioner ger åtkomst till det inbyggda registret.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>Definiera Azure AD-användaren som administratör

1. Logga in på webb konsolen OpenShift från webbläsaren med autentiseringsuppgifterna för en Azure AD-användare.

   1. Använd en InPrivate-, Incognito-eller motsvarande webb läsar fönster funktion för att logga in i-konsolen.
   1. Fönstret ser annorlunda ut efter det att OIDC har Aktiver ATS.
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="OpenID Connect-aktiverat inloggnings fönster.":::
   1. Välj **OpenID**

   > [!NOTE]
   > Anteckna användar namnet och lösen ordet som du använder för att logga in här. Det här användar namnet och lösen ordet fungerar som administratör för andra åtgärder i den här och andra artiklar.
1. Logga in med OpenShift CLI med hjälp av följande steg.  Den här processen kallas för diskussion `oc login` .
   1. I den högra delen av webb konsolen expanderar du snabb menyn för den inloggade användaren och väljer sedan **Kopiera inloggnings kommando**.
   1. Logga in på ett nytt flik-fönster med samma användare om det behövs.
   1. Välj **visnings-token**.
   1. Kopiera värdet i listan nedan **Logga in med denna token** till Urklipp och kör det i ett gränssnitt, som visas här.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

1. Kör `oc whoami` i-konsolen och anteckna utdata som **\<aad-user>** .  Vi använder det här värdet senare i artikeln.
1. Logga ut från OpenShift-webbkonsolen. Välj knappen högst upp till höger i webbläsarfönstret som är märkt som **\<aad-user>** och välj **Logga ut**.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>Ge Azure AD-användaren nödvändiga roller för register interaktion

1. Logga in på webb konsolen OpenShift från webbläsaren med hjälp av `kubeadmin` autentiseringsuppgifterna.
1. Logga in på OpenShift CLI med token för `kubeadmin` genom att följa stegen `oc login` ovan, men gör det när du har loggat in på webb konsolen med `kubeadmin` .
1. Kör följande kommandon för att aktivera åtkomst till det inbyggda registret för **AAD-användaren**.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   ```

   Utdata bör se ut ungefär så här.

   ```bash
   Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge
   ```

   Utdata bör se ut ungefär så här.

   ```bash
   config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>
   ```

   Utdata bör se ut ungefär så här.

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   ```

   Utdata bör se ut ungefär så här.

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

#### <a name="obtain-the-container-registry-url"></a>Hämta container Registry-URL: en

Använd `oc get route` kommandot som visas bredvid Hämta behållarens register-URL.

```bash
# Note: the value of "Container Registry URL" in the output is the fully qualified registry name.
HOST=$(oc get route default-route --template='{{ .spec.host }}')
echo "Container Registry URL: $HOST"
```

   > [!NOTE]
   > Observera konsolens utdata från **container Registry-URL**. Den kommer att användas som fullständigt register namn för den här guiden och efterföljande.

## <a name="next-steps"></a>Nästa steg

Använd det inbyggda registret för behållar avbildningar genom att distribuera ett program i OpenShift.  För Java-program följer du instruktions guiden för att [distribuera ett Java-program med öppen frihet/WebSphere-frihet på ett Azure Red Hat OpenShift 4-kluster](howto-deploy-java-liberty-app.md).
