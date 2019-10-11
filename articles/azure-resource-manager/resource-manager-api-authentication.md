---
title: Azure Active Directory autentisering och Resource Manager | Microsoft Docs
description: En utvecklares guide för autentisering med Azure Resource Manager API och Azure Active Directory för att integrera en app med andra Azure-prenumerationer.
author: dushyantgill
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: dugill
ms.openlocfilehash: 033f3ca9ca79903f884c625dc694b06a3e4fd04c
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263018"
---
# <a name="use-resource-manager-authentication-api-to-access-subscriptions"></a>Använd API för Resource Manager-autentisering för att få åtkomst till prenumerationer

Om du är en programutvecklare som behöver skapa en app som hanterar en kunds Azure-resurser visar den här artikeln hur du autentiserar med Azure Resource Manager API: er och får åtkomst till resurser i andra prenumerationer.

Din app kan komma åt Resource Manager-API: er på flera sätt:

1. **Användare + app-åtkomst**: för appar som har åtkomst till resurser för en inloggad användare. Den här metoden fungerar för appar, till exempel webbappar och kommando rads verktyg, som bara behandlar "interaktiv hantering" av Azure-resurser.
2. **Endast app-åtkomst**: för appar som kör daemon-tjänster och schemalagda jobb. Appens identitet beviljas direkt åtkomst till resurserna. Den här metoden fungerar för appar som behöver långsiktigt konsol löst åtkomst till Azure.

Den här artikeln innehåller steg-för-steg-instruktioner för att skapa en app som använder båda dessa metoder för auktorisering. Det visar hur du gör varje steg med REST API eller C#. Det kompletta ASP.NET MVC-programmet finns på [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

## <a name="what-the-web-app-does"></a>Vad webbappen gör

Webbappen:

1. Loggar – en Azure-användare.
2. Uppmanar användaren att bevilja webbappen åtkomst till Resource Manager.
3. Hämtar användarens + app-åtkomsttoken för åtkomst till Resource Manager.
4. Använder token (från steg 3) för att tilldela appens tjänstens huvud namn till en roll i prenumerationen. Det här steget ger appen långsiktig åtkomst till prenumerationen.
5. Hämtar endast app-åtkomsttoken.
6. Använder token (från steg 5) för att hantera resurser i prenumerationen via Resource Manager.

Här är webb programmets flöde.

![Flöde för Resource Manager-autentisering](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Som användare anger du prenumerations-ID för den prenumeration som du vill använda:

![ange prenumerations-ID](./media/resource-manager-api-authentication/sample-ux-1.png)

Välj det konto som ska användas för inloggning.

![Välj konto](./media/resource-manager-api-authentication/sample-ux-2.png)

Ange dina autentiseringsuppgifter.

![Ange autentiseringsuppgifter](./media/resource-manager-api-authentication/sample-ux-3.png)

Ge appen åtkomst till dina Azure-prenumerationer:

![Bevilja åtkomst](./media/resource-manager-api-authentication/sample-ux-4.png)

Hantera dina anslutna prenumerationer:

![Anslut prenumeration](./media/resource-manager-api-authentication/sample-ux-7.png)

## <a name="register-application"></a>Registrera program
Innan du börjar koda registrerar du din webbapp med Azure Active Directory (AD). Appens registrering skapar en central identitet för din app i Azure AD. Den innehåller grundläggande information om ditt program som OAuth-klient-ID, svars-URL: er och autentiseringsuppgifter som programmet använder för att autentisera och komma åt Azure Resource Manager API: er. App-registreringen registrerar också de olika delegerade behörigheter som programmet behöver för att få åtkomst till Microsoft API: er för användaren.

Information om hur du registrerar din app finns i [snabb start: registrera ett program med Microsoft Identity Platform](../active-directory/develop/quickstart-register-app.md). Ge appen ett namn och välj **konton i valfri organisations katalog** för de konto typer som stöds. För omdirigerings-URL anger du en domän som är kopplad till din Azure Active Directory.

Om du vill logga in som AD-program behöver du program-ID: t och en hemlighet. Program-ID visas i översikten för programmet. Information om hur du skapar hemliga och begär API-behörigheter finns i [snabb start: Konfigurera ett klient program för åtkomst till webb-API: er](../active-directory/develop/quickstart-configure-app-access-web-apis.md). Ange en ny klient hemlighet. För API-behörigheter väljer du **Azure Service Management**. Välj **delegerade behörigheter** och **user_impersonation**.

### <a name="optional-configuration---certificate-credential"></a>Valfri konfiguration – autentiseringsuppgifter för certifikat
Azure AD har även stöd för certifikat uppgifter för program: du skapar ett självsignerat certifikat, behåller den privata nyckeln och lägger till den offentliga nyckeln i din Azure AD-programregistrering. För autentisering skickar programmet en liten nytto Last till Azure AD som är signerad med din privata nyckel, och Azure AD validerar signaturen med den offentliga nyckel som du har registrerat.

Information om hur du skapar en AD-App med ett certifikat finns i [använda Azure PowerShell för att skapa ett tjänst objekt för att få åtkomst till resurser](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority) eller [använda Azure CLI för att skapa ett tjänst objekt för att få åtkomst till resurser](resource-group-authenticate-service-principal-cli.md).

## <a name="get-tenant-id-from-subscription-id"></a>Hämta klient-ID från prenumerations-ID
Om du vill begära en token som kan användas för att anropa Resource Manager måste ditt program veta klient-ID: t för den Azure AD-klient som är värd för Azure-prenumerationen. De flesta användare vet sina prenumerations-ID: n, men de känner kanske inte till sina klient-ID: n för Azure Active Directory. Fråga användaren om prenumerations-ID för att hämta användarens klient-ID. Ange prenumerations-ID när du skickar en begäran om prenumerationen:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

Begäran Miss lyckas eftersom användaren inte har loggat in ännu, men du kan hämta klient-ID: t från svaret. I detta undantag hämtar du klient-ID: t från värdet svars huvud för **www-autentisera**. Den här implementeringen visas i [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) -metoden.

## <a name="get-user--app-access-token"></a>Hämta användare + app-åtkomsttoken
Ditt program omdirigerar användaren till Azure AD med en OAuth 2,0-auktorisering för att autentisera användarens autentiseringsuppgifter och få tillbaka en auktoriseringskod. Appen använder auktoriseringskod för att hämta en åtkomsttoken för Resource Manager. Metoden [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) skapar auktoriseringsbegäran.

Den här artikeln visar REST API begär Anden om att autentisera användaren. Du kan också använda hjälp bibliotek för att autentisera i din kod. Mer information om dessa bibliotek finns i [Azure Active Directory Authentication libraries](../active-directory/active-directory-authentication-libraries.md). Vägledning om hur du integrerar identitets hantering i ett program finns i [Azure Active Directory Developer ' s guide](../active-directory/develop/v1-overview.md).

### <a name="auth-request-oauth-20"></a>Auth-begäran (OAuth 2,0)
Skicka en begäran om öppen ID Connect/OAuth 2.0-auktorisering till Azure AD-Auktoriserans slut punkt:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

De frågeparametrar som är tillgängliga för den här begäran beskrivs i artikeln om att [begära en auktoriseringskod](../active-directory/develop/v1-protocols-oauth-code.md#request-an-authorization-code) .

I följande exempel visas hur du begär OAuth 2.0-auktorisering:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD autentiserar användaren och, om det behövs, uppmanas användaren att bevilja behörighet till appen. Den returnerar auktoriseringskod till svars-URL: en för ditt program. Beroende på den begärda response_mode skickar Azure AD antingen tillbaka data i frågesträngen eller som post-data.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Auth-begäran (öppen ID Connect)
Om du inte bara vill komma åt Azure Resource Manager för användaren, utan även tillåta användaren att logga in i ditt program med hjälp av deras Azure AD-konto, utfärdar du en begäran om öppen ID Connect-auktorisering. Med öppen ID Connect får ditt program också en id_token från Azure AD som din app kan använda för att logga in användaren.

De frågeparametrar som är tillgängliga för den här begäran beskrivs i artikeln [Skicka inloggnings förfrågan](../active-directory/develop/v1-protocols-openid-connect-code.md#send-the-sign-in-request) .

Ett exempel på Open ID Connect-begäran är:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD autentiserar användaren och, om det behövs, uppmanas användaren att bevilja behörighet till appen. Den returnerar auktoriseringskod till svars-URL: en för ditt program. Beroende på den begärda response_mode skickar Azure AD antingen tillbaka data i frågesträngen eller som post-data.

Ett exempel på Open ID Connect-svar är:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Token-begäran (OAuth 2.0 Code Grant-flöde)
Nu när programmet har tagit emot auktoriseringskod från Azure AD är det dags att hämta åtkomsttoken för Azure Resource Manager.  Publicera en token för OAuth 2.0-kod beviljande till Azure AD-token-slutpunkt:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

De frågeparametrar som är tillgängliga för den här begäran beskrivs i artikeln om att [använda auktoriseringskod](../active-directory/develop/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) .

I följande exempel visas en begäran om Code Grant-token med autentiseringsuppgifter för lösen ord:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

När du arbetar med autentiseringsuppgifter för certifikatet skapar du en JSON Web Token (JWT) och signerar (RSA SHA256) med hjälp av den privata nyckeln för ditt programs certifikats autentiseringsuppgifter. Att skapa denna token visas i [flödet för klientens autentiseringsuppgifter](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#second-case-access-token-request-with-a-certificate).

Se avsnittet [Öppna ID Connect](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) för information om klientautentisering.

I följande exempel visas en begäran om Code Grant-token med autentiseringsuppgifter för certifikat:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Ett exempel svar för kod beviljande token:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Hantera token Grant-svar
Ett lyckat token-svar innehåller åtkomsttoken (användare + app) för Azure Resource Manager. Programmet använder denna åtkomsttoken för att få åtkomst till Resource Manager för användaren. Livs längden för de åtkomsttoken som utfärdas av Azure AD är en timme. Det är osannolikt att ditt webb program behöver förnya åtkomsttoken (användare + app). Om du behöver förnya åtkomsttoken använder du den uppdateringstoken som programmet tar emot i svaret från token. Publicera en OAuth 2.0-token-begäran till Azure AD-token-slutpunkten:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

De parametrar som ska användas med uppdaterings förfrågan beskrivs i [Uppdatera åtkomsttoken](../active-directory/develop/v1-protocols-oauth-code.md#refreshing-the-access-tokens).

I följande exempel visas hur du använder Refresh-token:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Även om Refresh-token kan användas för att hämta nya åtkomsttoken för Azure Resource Manager, är de inte lämpliga för offline-åtkomst av ditt program. Giltighets tiden för uppdateringstoken är begränsad och uppdateringstoken binds till användaren. Om användaren lämnar organisationen förlorar det program som använder uppdateringstoken för att få åtkomst. Den här metoden lämpar sig inte för program som används av team för att hantera sina Azure-resurser.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Kontrol lera om användaren kan tilldela åtkomst till prenumerationen
Ditt program har nu en token för att få åtkomst till Azure Resource Manager för användaren. Nästa steg är att ansluta din app till prenumerationen. När du har anslutit kan appen hantera dessa prenumerationer även om användaren inte är närvarande (långsiktig offline-åtkomst).

För varje prenumeration som du vill ansluta anropar du [Resource Manager List Permissions](https://docs.microsoft.com/rest/api/authorization/permissions) API för att avgöra om användaren har åtkomst hanterings rättigheter för prenumerationen.

[UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) -metoden för ASP.NET MVC-exempelprogrammet implementerar det här anropet.

I följande exempel visas hur du begär en användares behörigheter för en prenumeration. 83cfe939-2402-4581-b761-4f59b0a041e4 är prenumerationens ID.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Ett exempel på svaret att hämta användarens behörigheter för prenumerationen är:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

API: erna för behörigheter returnerar flera behörigheter. Varje behörighet består av tillåtna åtgärder (**åtgärder**) och otillåtna åtgärder (**notactions**). Om det finns en åtgärd i tillåtna åtgärder för alla behörigheter och inte finns i de otillåtna åtgärderna för den behörigheten, tillåts användaren utföra åtgärden. **Microsoft. Authorization/RoleAssignments/Write** är den åtgärd som beviljar åtkomst hanterings rättigheter. Ditt program måste parsa behörighets resultatet för att söka efter en regex-matchning för den här åtgärds strängen i **åtgärder** och **notactions** för varje behörighet.

## <a name="get-app-only-access-token"></a>Hämta endast app-åtkomsttoken
Nu vet du om användaren kan tilldela åtkomst till Azure-prenumerationen. Nästa steg är:

1. Tilldela lämplig RBAC-roll till programmets identitet i prenumerationen.
2. Verifiera åtkomst tilldelningen genom att fråga efter programmets behörighet för prenumerationen eller genom att använda Resource Manager med endast app-token.
3. Registrera anslutningen i dina program "anslutna prenumerationer"-data strukturen – behåller prenumerationens ID.

Låt oss titta närmare på det första steget. Om du vill tilldela en lämplig RBAC-roll till programmets identitet måste du bestämma:

* Objekt-ID för programmets identitet i användarens Azure Active Directory
* Identifieraren för den RBAC-roll som ditt program kräver för prenumerationen

När ditt program autentiserar en användare från en Azure AD skapas ett tjänst objekt för ditt program i Azure AD. Azure tillåter att RBAC-roller tilldelas till tjänstens huvud namn för att ge direkt åtkomst till motsvarande program på Azure-resurser. Den här åtgärden är precis vad du vill göra. Fråga Azure AD-Graph API för att fastställa identifieraren för tjänstens huvud namn för ditt program i den inloggade användarens Azure AD.

Du har bara en åtkomsttoken för Azure Resource Manager – du behöver en ny åtkomsttoken för att anropa Azure AD-Graph API. Alla program i Azure AD har behörighet att skicka frågor till sitt eget tjänst huvud objekt, så att en åtkomsttoken för appar bara är tillräcklig.

<a id="app-azure-ad-graph" />

### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Hämta endast app-åtkomsttoken för Azure AD Graph API

Om du vill autentisera din app och hämta en token till Azure AD Graph API utfärdar du en OAuth 2.0 Flow-Tokenbegäran till Azure AD token-slutpunkt (**https: \//login. microsoftonline. com/{directory_domain_name}/OAuth2/token**).

[GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) -metoden för exempel programmet ASP.NET MVC hämtar en app-only-token för Graph API med hjälp av Active Directory-AUTENTISERINGSBIBLIOTEK för .net.

De frågesträngar som är tillgängliga för den här begäran beskrivs i artikeln [begär en](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#request-an-access-token) åtkomsttoken.

En exempel förfrågan om beviljande token för klientens autentiseringsuppgifter:

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Ett exempel på svar på klientens Credential Grant-token:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Hämta ObjectId för application service-huvudobjektet i användare Azure AD
Använd nu endast appens åtkomsttoken för att skicka frågor till [Azure AD Graph-tjänstens huvud namns](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) -API för att fastställa objekt-ID: t för programmets tjänst objekt i katalogen.

[GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) -metoden för exempel programmet ASP.NET MVC implementerar det här anropet.

I följande exempel visas hur du begär ett programs tjänst objekt. a0448380-c346-4f9f-b897-c18733de9394 är programmets klient-ID.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

I följande exempel visas ett svar på begäran för ett programs tjänstens huvud namn

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Hämta Azure RBAC-roll-ID
Om du vill tilldela en lämplig RBAC-roll till tjänstens huvud namn måste du bestämma Azure RBAC-rollens identifierare.

Den högra RBAC-rollen för ditt program:

* Om programmet bara övervakar prenumerationen utan att göra några ändringar, krävs endast läsar behörighet för prenumerationen. Tilldela rollen **läsare** .
* Om ditt program hanterar Azure prenumerationen, skapar/ändrar/tar bort entiteter, kräver det en av deltagar behörigheterna.
  * Om du vill hantera en viss typ av resurs tilldelar du resursens specifika deltagar roller (virtuell dator deltagare, Virtual Network deltagare, lagrings konto deltagare osv.)
  * Om du vill hantera en resurs typ tilldelar du **deltagar** rollen.

Roll tilldelningen för ditt program är synlig för användarna, så välj minst behörighet som krävs.

Anropa [Resource Manager-rollens definitions-API](https://docs.microsoft.com/rest/api/authorization/roledefinitions) för att visa alla Azure RBAC-roller och iterera sedan över resultatet för att hitta roll definitionen efter namn.

[GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) -metoden för ASP.NET MVC-exempelprogrammet implementerar det här anropet.

Följande exempel på begäran visar hur du hämtar Azure RBAC-roll-ID. 09cbd307-aa71-4aca-b346-5f253e6e3ebb är prenumerationens ID.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

Svaret har följande format:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Du behöver inte anropa det här API: et kontinuerligt. När du har bestämt det välkända GUID för roll definitionen kan du skapa roll Definitions-ID: t som:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Här är identifierarna för vanliga inbyggda roller:

| Roll | LED |
| --- | --- |
| Läsare |acdd72a7-3385-48ef-bd42-f606fba81ae7 |
| Deltagare |b24988ac-6180-42a0-ab88-20f7382dd24c |
| Virtuell dator deltagare |d73bb868-a0df-4d4d-bd69-98a00b01fccb |
| Virtual Network deltagare |b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
| Lagrings konto deltagare |86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
| Webbplats deltagare |de139f84-1756-47ae-9be6-808fbbe84772 |
| Webb Plans deltagare |2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
| SQL Server deltagare |6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
| SQL DB-deltagare |9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |

### <a name="assign-rbac-role-to-application"></a>Tilldela RBAC-rollen till programmet
Du har allt du behöver för att tilldela en lämplig RBAC-roll till tjänstens huvud namn med hjälp av [Resource Manager Skapa roll tilldelnings](https://docs.microsoft.com/rest/api/authorization/roleassignments) -API.

[GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) -metoden för ASP.NET MVC-exempelprogrammet implementerar det här anropet.

En exempel förfrågan om att tilldela RBAC-rollen till programmet:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

I begäran används följande värden:

| GUID | Beskrivning |
| --- | --- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb |ID för prenumerationen |
| c3097b31-7309-4c59-b4e3-770f8406bad2 |objekt-ID för tjänstens huvud namn för programmet |
| b24988ac-6180-42a0-ab88-20f7382dd24c |ID för deltagar rollen |
| 4f87261d-2816-465d-8311-70a27558df4c |ett nytt GUID som skapats för den nya roll tilldelningen |

Svaret har följande format:

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Hämta endast app-åtkomsttoken för Azure Resource Manager
Verifiera att appen kan komma åt prenumerationen genom att utföra en test aktivitet i prenumerationen med en app-only-token.

Om du vill hämta en app-only-åtkomsttoken följer du anvisningarna från avsnittet [Hämta app-only-token för Azure AD Graph API](#app-azure-ad-graph), med ett annat värde för resurs parametern:

    https://management.core.windows.net/

[ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) -metoden för exempel programmet ASP.NET MVC hämtar en app-only-token för Azure Resource Manager med hjälp av Active Directory-autentiseringsbibliotek för .net.

#### <a name="get-applications-permissions-on-subscription"></a>Hämta programmets behörigheter för prenumerationen
Om du vill kontrol lera att programmet har åtkomst till en Azure-prenumeration kan du också anropa [Resource Manager-behörighetens](https://docs.microsoft.com/rest/api/authorization/permissions) API. Den här metoden liknar hur du har fastställt om användaren har åtkomst hanterings rättigheter för prenumerationen. Men den här gången anropar du Permissions-API: et med den app-only-åtkomsttoken som du fick i föregående steg.

[ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) -metoden för ASP.NET MVC-exempelprogrammet implementerar det här anropet.

## <a name="manage-connected-subscriptions"></a>Hantera anslutna prenumerationer
När lämplig RBAC-roll tilldelas till ditt programs tjänst huvud namn i prenumerationen kan ditt program fortsätta att övervaka/hantera den med hjälp av appar-endast åtkomsttoken för Azure Resource Manager.

Om en prenumerations ägare tar bort roll tilldelningen för ditt program med hjälp av portalen eller kommando rads verktygen kan programmet inte längre komma åt den prenumerationen. I så fall bör du meddela användaren att anslutningen till prenumerationen har brutits utanför programmet och ge dem möjlighet att "reparera" anslutningen. "Reparera" skulle återskapa den roll tilldelning som togs bort offline.

Precis som du aktiverade användaren för att ansluta prenumerationer till ditt program måste du tillåta att användaren kopplar från prenumerationer. Från en åtkomst hanterings plats innebär från koppling innebär borttagning av den roll tilldelning som programmets tjänst objekt har på prenumerationen. Valfritt tillstånd i programmet för prenumerationen kan också tas bort.
Endast användare med behörigheten åtkomst hantering för prenumerationen kan koppla från prenumerationen.

[RevokeRoleFromServicePrincipalOnSubscription-metoden](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) för ASP.NET MVC-exempelprogrammet implementerar det här anropet.

Det är IT-användare kan nu enkelt ansluta och hantera sina Azure-prenumerationer med ditt program.
