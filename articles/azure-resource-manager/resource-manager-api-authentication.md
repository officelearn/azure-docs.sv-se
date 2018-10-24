---
title: Azure Active Directory-autentisering och Resource Manager | Microsoft Docs
description: Utvecklarens guide till autentisering med Azure Resource Manager API och Azure Active Directory för att integrera en app med andra Azure-prenumerationer.
services: azure-resource-manager,active-directory
documentationcenter: na
author: dushyantgill
manager: timlt
editor: tysonn
ms.assetid: 17b2b40d-bf42-4c7d-9a88-9938409c5088
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2018
ms.author: dugill
ms.openlocfilehash: 69127702a7d8e7027e78a8e04a4e8e1bc3e36b65
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956348"
---
# <a name="use-resource-manager-authentication-api-to-access-subscriptions"></a>Använda Resource Manager-autentisering-API för att få åtkomst till prenumerationer
## <a name="introduction"></a>Introduktion
Om du är programutvecklare som vill skapa en app som hanterar en kunds Azure-resurser, den här artikeln visar hur att autentisera med Azure Resource Manager API: er och få åtkomst till resurser i andra prenumerationer.

Din app har åtkomst till Resource Manager-API: er i par olika sätt:

1. **Användare + appåtkomst**: för appar som får åtkomst till resurser åt en inloggad användare. Den här metoden fungerar för appar, till exempel web apps och kommandoradsverktyg som bry dig om endast ”interaktiva” Azure-resurser.
2. **Endast appen**: för appar som kör daemon-tjänster och schemalagda jobb. Appens identitet beviljas direkt åtkomst till resurserna. Den här metoden fungerar för appar som behöver långsiktig fjärradministrering (obevakad) åtkomst till Azure.

Den här artikeln innehåller stegvisa instruktioner för att skapa en app som använder båda metoderna auktorisering. Den visar hur du utför varje steg med REST API eller C#. Hela ASP.NET MVC-appen är tillgänglig på [ https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense ](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

## <a name="what-the-web-app-does"></a>Vad appen gör
Webbapp:

1. Loggar in en Azure-användare.
2. Ombeds användaren att ge web appåtkomst till Resource Manager.
3. Hämtar användare + app-åtkomsttoken för åtkomst till Resource Manager.
4. Använder token (från steg 3) för att tilldela en roll i prenumerationen appens tjänstens huvudnamn. Det här steget ger åtkomst till långsiktig appen till prenumerationen.
5. Hämtar endast appen token.
6. Använder token (från steg 5) för att hantera resurser i prenumerationen via Resource Manager.

Här är flödet av webbprogrammet.

![Autentiseringsflödet för Resource Manager](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Som en användare kan ange du prenumerations-ID för den prenumeration som du vill använda:

![Ange prenumerations-ID](./media/resource-manager-api-authentication/sample-ux-1.png)

Välj kontot som ska användas för att logga in.

![Välj konto](./media/resource-manager-api-authentication/sample-ux-2.png)

Ange dina autentiseringsuppgifter.

![Ange autentiseringsuppgifter](./media/resource-manager-api-authentication/sample-ux-3.png)

Ge appen åtkomst till dina Azure-prenumerationer:

![Bevilja åtkomst](./media/resource-manager-api-authentication/sample-ux-4.png)

Hantera dina anslutna prenumerationer:

![Anslut prenumeration](./media/resource-manager-api-authentication/sample-ux-7.png)

## <a name="register-application"></a>Registrera program
Innan du börjar skriva kod kan du registrera din webbapp med Azure Active Directory (AD). Appregistreringen skapar en central identitet för din app i Azure AD. Den innehåller grundläggande information om ditt program som OAuth klient-ID, svars-URL och autentiseringsuppgifter som används för att autentisera och få åtkomst till Azure Resource Manager API: er i ditt program. Appregistreringen innehåller också information om olika delegerade behörigheter som programmet behöver vid åtkomst till Microsoft APIs användarens räkning.

Eftersom din app har åtkomst till andra prenumeration, måste du konfigurera det som ett program med flera innehavare. Ange en domän som är associerade med Azure Active Directory om du vill skicka verifieringen. Logga in på portalen om du vill se de domäner som är associerade med Azure Active Directory.

I följande exempel visar hur du registrerar appen med hjälp av Azure PowerShell. Du måste ha den senaste versionen (augusti 2016) av Azure PowerShell för att kommandot ska fungera.

```azurepowershell-interactive
$app = New-AzureRmADApplication -DisplayName "{app name}" -HomePage "https://{your domain}/{app name}" -IdentifierUris "https://{your domain}/{app name}" -Password "{your password}" -AvailableToOtherTenants $true
```

Om du vill logga in som AD-programmet, behöver du program-ID och lösenord. Om du vill se det program-ID som returnerades från föregående kommando använder du:

```azurepowershell-interactive
$app.ApplicationId
```

I följande exempel visar hur du registrerar appen med hjälp av Azure CLI.

```azurecli-interactive
az ad app create --display-name {app name} --homepage https://{your domain}/{app name} --identifier-uris https://{your domain}/{app name} --password {your password} --available-to-other-tenants true
```

Det inkluderar AppId som du behöver vid autentisering som programmet i resultaten.

### <a name="optional-configuration---certificate-credential"></a>Valfri konfiguration - autentiseringsuppgifter för certifikat
Azure AD stöder också autentiseringsuppgifter för certifikat för program: du skapar ett självsignerat certifikat, hålla den privata nyckeln och Lägg till den offentliga nyckeln i din registrering för Azure AD-program. Programmet skickar en liten nyttolast till Azure AD som signerats med din privata nyckel för autentisering och Azure AD verifierar signaturen med hjälp av den offentliga nyckeln som du registrerat.

Information om hur du skapar en AD-app med ett certifikat finns i [med Azure PowerShell för att skapa ett huvudnamn för tjänsten för resursåtkomst](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority) eller [används Azure CLI för att skapa ett huvudnamn för tjänsten för resursåtkomst](resource-group-authenticate-service-principal-cli.md) .

## <a name="get-tenant-id-from-subscription-id"></a>Hämta klient-ID från prenumerations-ID
Programmet måste veta klient-ID för Azure AD-klient som är värd för den Azure-prenumerationen om du vill begära en token som kan användas för att anropa Resource Manager. Mest sannolikt vet användarna sina prenumerations-ID, men de kan inte vet ID: N för klientorganisationen för Azure Active Directory. För att hämta användarens klient-ID, be användaren ange prenumerations-ID. Ange den prenumerationen när du skickar en begäran om prenumerationen med ID:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

Begäran misslyckas eftersom användaren inte har loggat in ännu, men du kan hämta klient-ID från svaret. I det här undantaget hämta klient-ID från svaret huvudets värde för **WWW-autentisera**. Du ser den här implementeringen i den [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) metod.

## <a name="get-user--app-access-token"></a>Hämta användare + app åtkomst-token
Programmet omdirigeras användaren till Azure AD med en OAuth 2.0 godkänna begäran – autentisera användarens autentiseringsuppgifter och få tillbaka en auktoriseringskod. Programmet använder Auktoriseringskoden för att hämta en åtkomsttoken för Resource Manager. Den [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) metoden skapar begäran om godkännande.

Den här artikeln visar REST API-begäranden för att autentisera användaren. Du kan också använda helper-bibliotek för att utföra autentisering i din kod. Läs mer om dessa bibliotek [Azure Active Directory Authentication Libraries](../active-directory/active-directory-authentication-libraries.md). Anvisningar för att integrera Identitetshantering i ett program finns i [Utvecklarhandbok för Azure Active Directory](../active-directory/develop/v1-overview.md).

### <a name="auth-request-oauth-20"></a>Auth-begäranden (OAuth 2.0)
Göra en öppna ID Connect/OAuth2.0 auktorisera begäran till slutpunkten för auktorisering av Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

Frågesträngparametrarna som är tillgängliga för den här begäran beskrivs i den [begär en auktoriseringskod](../active-directory/develop/v1-protocols-oauth-code.md#request-an-authorization-code) artikeln.

I följande exempel visas hur du begär OAuth2.0 auktorisering:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD autentiserar användaren och, om det behövs, uppmanar användaren att bevilja behörighet till appen. Den returnerar Auktoriseringskoden till svars-URL för ditt program. Beroende på den begärda response_mode, Azure AD antingen skickar tillbaka data i frågesträngen eller som postdata.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Auth-begäranden (Öppna ID Connect)
Om du inte bara vill komma åt Azure Resource Manager å användarens vägnar, men även tillåta användaren att logga in på ditt program med sina Azure AD-konto, göra en öppna ID Connect auktorisera begäran. Med öppna ID Connect får program också en id_token från Azure AD som din app kan använda för att logga in användaren.

Frågesträngparametrarna som är tillgängliga för den här begäran beskrivs i den [skicka begäran inloggning](../active-directory/develop/v1-protocols-openid-connect-code.md#send-the-sign-in-request) artikeln.

En exempel öppna ID Connect-begäran är:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD autentiserar användaren och, om det behövs, uppmanar användaren att bevilja behörighet till appen. Den returnerar Auktoriseringskoden till svars-URL för ditt program. Beroende på den begärda response_mode, Azure AD antingen skickar tillbaka data i frågesträngen eller som postdata.

Ett exempel öppna ID Connect svar är:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Tokenbegäran (OAuth2.0 kod ge Flow)
Nu när ditt program har tagit emot Auktoriseringskoden från Azure AD, är det dags att hämta den åtkomsttoken för Azure Resource Manager.  Efter en OAuth2.0 kod bevilja Token för begäran till tokenslutpunkten för Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Frågesträngparametrarna som är tillgängliga för den här begäran beskrivs i den [använder Auktoriseringskoden](../active-directory/develop/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) artikeln.

I följande exempel visas en begäran om kod bevilja token med autentiseringsuppgifter för lösenord:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

När du arbetar med autentiseringsuppgifter för certifikat, skapa en JSON Web Token (JWT) och logga (RSA-SHA256) med hjälp av den privata nyckeln för ditt programs certifikat autentiseringsuppgift. Att skapa denna token visas i den [credential klientflödet](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#second-case-access-token-request-with a-certificate).  Referenser finns i den [Active Directory Authentication Library (.NET) kod](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs) logga klienten Assertion JWT-token.

Se den [öppna ID Connect spec](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) mer information om klientautentisering.

I följande exempel visas en begäran om kod bevilja token med autentiseringsuppgifter för certifikat:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Ett exempel på ett svar för kod bevilja token:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Hantera kod bevilja token svar
Ett lyckat svar för token innehåller (användare + app) åtkomsttoken för Azure Resource Manager. Programmet använder åtkomsttoken för att få åtkomst till Resource Manager användarens räkning. Livslängden för åtkomsttoken som utfärdas av Azure AD är en timme. Det är inte troligt att ditt webbprogram måste förnya (användare + app) åtkomst-token. Om den behöver förnya åtkomsttoken kan du använda uppdateringstoken som programmet tar emot i token-svaret. Efter en OAuth2.0 Token för begäran till tokenslutpunkten för Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Parametrar som ska användas med uppdateringsbegäran om beskrivs i [uppdatera åtkomsttoken](../active-directory/develop/v1-protocols-oauth-code.md#refreshing-the-access-tokens).

I följande exempel visas hur du använder uppdateringen token:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Även om uppdateringstoken kan användas för att hämta nya åtkomsttoken för Azure Resource Manager, är de inte lämpligt för offline-åtkomst av programmet. Uppdatera token-livslängd är begränsat och uppdateringstoken som är kopplade till användaren. Om användaren lämnar organisationen, förlorar åtkomst till programmet med uppdateringstoken. Den här metoden är inte lämpliga för program som används av team för att hantera sina Azure-resurser.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Kontrollera om användaren kan ge åtkomst till prenumerationen
Ditt program nu har en token för att få åtkomst till Azure Resource Manager användarens räkning. Nästa steg är att ansluta din app till prenumerationen. När du anslutit din app kan hantera dessa prenumerationer även om användaren inte finns (långsiktig offline-åtkomst).

Varje prenumeration att ansluta kan anropa den [Resource Manager listbehörigheter](https://docs.microsoft.com/rest/api/authorization/permissions) API för att avgöra om användaren har hantering av åtkomsträttigheter för prenumerationen.

Den [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) metod för ASP.NET MVC-exempelapp implementerar det här anropet.

I följande exempel visas hur du begär en användares behörigheter för en prenumeration. 83cfe939-2402-4581-b761-4f59b0a041e4 är ID för prenumerationen.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Ett exempel på svaret att hämta användarens behörigheter i prenumerationen är:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

Behörigheterna som API: et returnerar flera behörigheter. Varje behörighet består av tillåtna åtgärder (**åtgärder**) och otillåtna åtgärder (**notactions**). Om en åtgärd är finns i tillåtna åtgärder av vilken behörighet som helst och finns inte i otillåtna åtgärderna för den behörigheten, tillåts användaren att utföra åtgärden. **Microsoft.Authorization/RoleAssignments/Write** är den åtgärd som beviljar åtkomst till rights management. Programmet måste tolka resultatet behörighet att söka efter en regex-matchning på den här åtgärden strängen i den **åtgärder** och **notactions** av varje behörighet.

## <a name="get-app-only-access-token"></a>Hämta app-begränsad åtkomsttoken
Nu vet du om användaren kan ge åtkomst till Azure-prenumeration. Nästa steg är:

1. Tilldela lämpliga RBAC-roll till programmets identitet för prenumerationen.
2. Kontrollera åtkomst tilldelningen genom att fråga om programmets behörighet för den aktuella prenumerationen eller genom att gå till Resource Manager med endast app-token.
3. Registrera anslutningen i ditt program ”anslutna prenumerationer” datastruktur - spara ID för prenumerationen.

Låt oss titta närmare på det första steget. Om du vill tilldela rollen RBAC programmets identitet, måste du bestämma:

* Objekt-ID för applikationsidentitet i användarens Azure Active Directory
* Identifierare för RBAC-rollen som programmet behöver för prenumerationen

När programmet autentiseras en användare från en Azure AD skapar ett huvudnamnsobjekt för tjänsten för ditt program i Azure AD. Azure kan RBAC-roller som ska tilldelas till tjänstens huvudnamn för att ge direktåtkomst till motsvarande program på Azure-resurser. Den här åtgärden är precis vad du vill göra. Frågan Azure AD Graph API för att identifiera ID för tjänstens huvudnamn i ditt program under den inloggade användaren är Azure AD.

Du bara har en åtkomsttoken för Azure Resource Manager - du behöver en ny åtkomsttoken att anropa Azure AD Graph API. Varje program i Azure AD har behörighet att fråga en egen tjänstens huvudnamnsobjekt så att en app-begränsad åtkomsttoken är tillräckliga.

<a id="app-azure-ad-graph" />

### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Hämta app-begränsad åtkomsttoken för Azure AD Graph API
För att autentisera din app och hämta en token till Azure AD Graph API, utfärda en Tokenbegäran för klientens autentiseringsuppgifter bevilja OAuth2.0-flöde till Azure AD-token-slutpunkten (**https://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token**).

Den [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) metod i ASP.net MVC-exempelprogrammet hämtar en app-begränsad åtkomst-token för Graph API med Active Directory Authentication Library för .NET.

Frågesträngparametrarna som är tillgängliga för den här begäran beskrivs i den [begär en åtkomsttoken](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#request-an-access-token) artikeln.

En exempelbegäran om autentiseringsuppgifter för klienten bevilja token:

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Ett exempel på ett svar för autentiseringsuppgifter för klienten bevilja token:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Hämta ObjectId för programmet tjänstens huvudnamn i användarens Azure AD
Nu kan använda app-begränsad åtkomsttoken till frågan i [tjänsthuvudnamn i Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) API för att fastställa objekt-ID för programmets tjänstens huvudnamn i katalogen.

Den [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) metod i ASP.net MVC-exempelprogrammet implementerar det här anropet.

I följande exempel visas hur du begär ett programs tjänstens huvudnamn. a0448380-c346-4f9f-b897-c18733de9394 är klient-ID för programmet.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

I följande exempel visas ett svar på begäran för ett program tjänsten huvudnamn

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Hämta ID för Azure RBAC-roll
Om du vill tilldela lämpliga RBAC-roll till tjänstens huvudnamn, måste du bestämma identifierare för Azure RBAC-rollen.

Den rätta RBAC-rollen för ditt program:

* Om ditt program bara övervakar prenumerationen, utan ändringar, kräver endast läsare behörigheter i prenumerationen. Tilldela den **läsare** roll.
* Om ditt program hanterar Azure-prenumeration, skapa/Ändra/ta bort entiteter, kräver en av behörigheterna som deltagare.
  * För att hantera en viss typ av resurs, tilldela de resursspecifika deltagarroller (Virtual Machine-deltagare, Virtual Network-deltagare, Lagringskontodeltagare osv.)
  * För att hantera någon resurstyp, tilldela den **deltagare** roll.

Rolltilldelning för ditt program är synliga för användare, så Välj krävs för lägsta behörighet.

Anropa den [rolldefinition för Resource Manager API](https://docs.microsoft.com/rest/api/authorization/roledefinitions) att lista alla Azure RBAC-roller och sök sedan iterera över resultatet att hitta önskad rolldefinitionen efter namn.

Den [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) metod för ASP.net MVC-exempelapp implementerar det här anropet.

I följande exempel begäran visar hur du hämtar identifierare för Azure RBAC-rollen. 09cbd307-aa71-4aca-b346-5f253e6e3ebb är ID för prenumerationen.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

Svaret är i följande format:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Du behöver inte att anropa detta API med jämna mellanrum. När du har bestämt välkända GUID för rolldefinitionen, kan du skapa rolldefinitions-ID som:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Här följer identifierarna som används ofta inbyggda roller:

| Roll | GUID |
| --- | --- |
| Läsare |acdd72a7-3385-48EF-bd42-f606fba81ae7 |
| Deltagare |b24988ac-6180-42A0-ab88-20f7382dd24c |
| Virtuell datordeltagare |d73bb868-a0df-4d4d-bd69-98a00b01fccb |
| Virtual Network-deltagare |b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
| Lagringskontodeltagare |86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
| Webbplatsdeltagare |de139f84-1756-47ae-9be6-808fbbe84772 |
| Webbplan-deltagare |2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
| SQL Server-deltagare |6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
| SQL DB-deltagare |9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |

### <a name="assign-rbac-role-to-application"></a>Tilldela RBAC-roll till program
Du har allt du behöver för att tilldela lämpliga RBAC-roll till tjänstens huvudnamn med hjälp av den [Resource Manager skapa rolltilldelning](https://docs.microsoft.com/rest/api/authorization/roleassignments) API.

Den [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) metod för ASP.net MVC-exempelapp implementerar det här anropet.

En exempel-begäran för att tilldela RBAC-roll till program:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

I begäran används följande värden:

| GUID | Beskrivning |
| --- | --- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb |ID för prenumerationen |
| c3097b31-7309-4c59-b4e3-770f8406bad2 |objekt-ID för tjänstens huvudnamn för programmet |
| acdd72a7-3385-48EF-bd42-f606fba81ae7 |ID för läsarrollen |
| 4f87261d-2816-465D-8311-70a27558df4c |ett nytt guid som skapats för ny rolltilldelning |

Svaret är i följande format:

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Hämta app-begränsad åtkomsttoken för Azure Resource Manager
Om du vill verifiera appen har önskad öppna för prenumerationen genom att utföra en test-uppgift för prenumerationen med hjälp av en appspecifika-token.

Om du vill få ett app-begränsad åtkomsttoken, följer du instruktionerna från avsnittet [hämta app-begränsad åtkomsttoken för Azure AD Graph API](#app-azure-ad-graph), med ett annat värde för resursparametern:

    https://management.core.windows.net/

Den [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) metod i ASP.NET MVC-exempelprogrammet hämtar en app-begränsad åtkomst-token för Azure Resource Manager med Active Directory Authentication Library för .net.

#### <a name="get-applications-permissions-on-subscription"></a>Få programmets behörighet för prenumeration
Om du vill kontrollera att ditt program har att lägga till på en Azure-prenumeration kan du även anropa den [Resource Manager-behörigheter](https://docs.microsoft.com/rest/api/authorization/permissions) API. Den här metoden liknar hur du bestämt om användaren har behörighet för åtkomsthantering för prenumerationen. Den här tiden kan anropa dock behörigheter API med app-begränsad åtkomsttoken som du fick i föregående steg.

Den [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) metod för ASP.NET MVC-exempelapp implementerar det här anropet.

## <a name="manage-connected-subscriptions"></a>Hantera anslutna prenumerationer
När rätt RBAC-roll tilldelas till ditt programs tjänstens huvudnamn för prenumerationen, kan ditt program hålla övervakning/hantera den med hjälp av app-begränsad åtkomsttoken för Azure Resource Manager.

Om en prenumerationsägare tar bort rolltilldelning för ditt program med hjälp av portalen eller kommandoradsverktyg, ditt program inte längre komma åt den aktuella prenumerationen. I så fall bör du meddela användaren om att prenumerationen anslutningen bröts från utanför programmet och ge dem ett alternativ för att ”reparera” anslutningen. ”Reparera” skulle återskapa den rolltilldelning som offline har tagits bort.

Precis som du har aktiverat att användaren ska ansluta prenumerationer till ditt program måste du tillåta användare att koppla från prenumerationer för. Koppla från innebär att ta bort den rolltilldelning som programmets tjänstens huvudnamn har i prenumerationen från en access management-synvinkel. Du kan också kan några tillstånd i programmet för prenumerationen tas bort för.
Endast användare med hantering av åtkomstbehörighet för prenumerationen kan du koppla från prenumerationen.

Den [RevokeRoleFromServicePrincipalOnSubscription metoden](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) av ASP.net MVC-exempelappen implementerar det här anropet.

Det var allt – användare kan nu enkelt ansluta och hantera sina Azure-prenumerationer med ditt program.
