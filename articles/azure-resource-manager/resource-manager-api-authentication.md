---
title: Azure Active Directory-autentisering och Resource Manager | Microsoft Docs
description: En utvecklare guide för att autentisering med Azure Resource Manager API och Azure Active Directory för att integrera en app med andra Azure-prenumerationer.
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
ms.date: 11/15/2017
ms.author: dugill
ms.openlocfilehash: 1dea8d173432b05a72de72e8b17db4c97ea7924d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359870"
---
# <a name="use-resource-manager-authentication-api-to-access-subscriptions"></a>Autentisering-Använd Resource Manager API för åtkomst-prenumerationer
## <a name="introduction"></a>Introduktion
Om du är programutvecklare som behövs för att skapa en app som hanterar Azure-resurser för en kund kan den här artikeln visar hur att autentisera med Azure Resource Manager API: er och få åtkomst till resurser i andra prenumerationer.

Appen har åtkomst till Resource Manager-API: er i på två sätt:

1. **Användar- + appåtkomst**: för appar som har åtkomst till resurser för en inloggad användare. Den här metoden fungerar för appar, till exempel webbprogram och kommandoradsverktyg som handlar om endast ”interaktiva management” Azure-resurser.
2. **Endast App-åtkomst**: för appar som körs daemon tjänster och schemalagda jobb. Appens identitet beviljas direkt åtkomst till resurserna. Den här metoden fungerar för appar som behöver långsiktiga fjärradministrerade (obevakad installation) åtkomst till Azure.

Den här artikeln innehåller stegvisa instruktioner för att skapa en app som använder båda metoderna auktorisering. Den visar hur du utför varje steg med REST API- eller C#. Fullständig ASP.NET MVC-program finns på [ https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense ](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

## <a name="what-the-web-app-does"></a>Webbprogrammet har
Webbprogrammet:

1. Loggar in en Azure-användare.
2. Fråga användare för att ge appen Webbåtkomst till Resource Manager.
3. Hämtar användar- + app åtkomst-token för åtkomst till Resource Manager.
4. Använder token (från steg 3) för att tilldela en roll i prenumerationen appens tjänstens huvudnamn. Det här steget ger åtkomst till långsiktig appen till prenumerationen.
5. Hämtar endast app-åtkomst-token.
6. Använder token (från steg 5) för att hantera resurserna i prenumerationen via Resource Manager.

Här är flödet av webbprogrammet.

![Autentiseringsflödet för hanteraren för filserverresurser](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Som användare kan ange du prenumerations-ID för den prenumeration som du vill använda:

![Ange prenumerations-ID](./media/resource-manager-api-authentication/sample-ux-1.png)

Välj kontot som ska användas för att logga in.

![Välj konto](./media/resource-manager-api-authentication/sample-ux-2.png)

Ange dina autentiseringsuppgifter.

![Ange autentiseringsuppgifter](./media/resource-manager-api-authentication/sample-ux-3.png)

Ge appen åtkomst till dina Azure-prenumerationer:

![Bevilja åtkomst](./media/resource-manager-api-authentication/sample-ux-4.png)

Hantera dina anslutna prenumerationer:

![Ansluta prenumeration](./media/resource-manager-api-authentication/sample-ux-7.png)

## <a name="register-application"></a>Registrera program
Innan du börjar kodning måste du registrera ditt webbprogram med Azure Active Directory (AD). App-registreringen skapas en central identitet för din app i Azure AD. Den innehåller grundläggande information om programmet som OAuth-klient-ID, svars-URL: er och autentiseringsuppgifter som används för att autentisera och Azure Resource Manager API: er i ditt program. Registreringen av appen även information om de olika delegerade behörigheter som programmet behöver få åtkomst till Microsoft APIs för användarens räkning.

Eftersom din app har åtkomst till andra prenumerationer, måste du konfigurera den som ett program med flera innehavare. Ange en domän som är associerade med Azure Active Directory om du vill skicka validering. Logga in på portalen om du vill se de domäner som är associerade med Azure Active Directory.

I följande exempel visar hur du registrerar appen med hjälp av Azure PowerShell. Du måste ha den senaste versionen (augusti 2016) av Azure PowerShell för att kommandot ska fungera.

    $app = New-AzureRmADApplication -DisplayName "{app name}" -HomePage "https://{your domain}/{app name}" -IdentifierUris "https://{your domain}/{app name}" -Password "{your password}" -AvailableToOtherTenants $true

Du måste logga in som AD-program, program-ID och lösenord. Om du vill se det program-ID som returnerades från föregående kommando använder du:

    $app.ApplicationId

I följande exempel visar hur du registrerar appen med hjälp av Azure CLI.

    azure ad app create --name {app name} --home-page https://{your domain}/{app name} --identifier-uris https://{your domain}/{app name} --password {your password} --available true

AppId som du behöver vid autentisering som programmet inkluderar resultaten.

### <a name="optional-configuration---certificate-credential"></a>Valfri konfiguration - autentiseringsuppgifter för certifikat
Azure AD stöder också certifikat autentiseringsuppgifter för program: skapa ett självsignerat certifikat, hålla den privata nyckeln och lägga till den offentliga nyckeln i din Azure AD-appregistrering. Programmet skickar en liten nyttolast till Azure AD som signerats med din privata nyckel för autentisering, och Azure AD verifierar signaturen med offentlig nyckel som du har registrerat.

Information om hur du skapar en AD-app med ett certifikat finns i [Använd Azure PowerShell för att skapa ett huvudnamn för tjänsten att komma åt resurser](resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority) eller [Använd Azure CLI för att skapa ett huvudnamn för tjänsten att komma åt resurser](resource-group-authenticate-service-principal-cli.md).

## <a name="get-tenant-id-from-subscription-id"></a>Hämta klient-ID från prenumerations-ID
Programmet måste veta klient-ID för Azure AD-klient som är värd för Azure-prenumeration om du vill begära en token som kan användas för att anropa Resource Manager. Troligen användarna känner prenumerationen ID: N, men vet de inte klienten ID: N för Azure Active Directory. Be användaren för att hämta användarens klient-ID för prenumerations-ID. Ange den prenumerationen-ID när du skickar en begäran om prenumerationen:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

Begäran misslyckas eftersom användaren inte har inloggad ännu, men du kan hämta klient-ID från svaret. Hämta klient-ID i det här undantaget från huvudvärde svar för **WWW-autentisera**. Du ser den här implementeringen i den [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) metod.

## <a name="get-user--app-access-token"></a>Hämta användar- + app åtkomst-token
Programmet omdirigeras användaren till Azure AD med en OAuth 2.0 godkänna begäran - att autentisera användarens autentiseringsuppgifter och få tillbaka en auktoriseringskod. Programmet använder Auktoriseringskoden för att hämta ett åtkomsttoken för Resource Manager. Den [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) metoden skapar auktoriseringsbegäran.

Den här artikeln visar REST API-begäranden att autentisera användaren. Du kan också använda helper-bibliotek för att utföra autentisering i koden. Mer information om dessa bibliotek finns [Azure Active Directory-Autentiseringsbibliotek](../active-directory/active-directory-authentication-libraries.md). Anvisningar om att integrera Identitetshantering i ett program finns [Utvecklarhandbok för Azure Active Directory](../active-directory/active-directory-developers-guide.md).

### <a name="auth-request-oauth-20"></a>Auth-begäranden (OAuth 2.0)
Utfärda en öppna ID Connect/OAuth2.0 godkänna begäran till slutpunkten för auktorisering av Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

Frågan string-parametrar som är tillgängliga för denna begäran beskrivs i den [begär en Auktoriseringskoden](../active-directory/develop/active-directory-protocols-oauth-code.md#request-an-authorization-code) artikel.

I följande exempel visas hur du begär OAuth2.0 tillstånd:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD autentiserar användaren och, om det behövs, ombeds användaren att ge behörighet till appen. Den returnerar Auktoriseringskoden till Reply-URL: en för programmet. Beroende på den begärda response_mode Azure AD antingen skickar tillbaka data i frågesträngen eller som postdata.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Auth-begäranden (Öppna ID Connect)
Om du inte bara vill komma åt Azure Resource Manager för användarens räkning, men även tillåta användaren att logga in på ditt program som använder sina Azure AD-konton, göra en öppna ID Connect auktorisera begäran. Med öppna ID Connect får program också en id_token från Azure AD som din app använder för att logga in användaren.

Frågan string-parametrar som är tillgängliga för denna begäran beskrivs i den [skicka begäran om inloggning](../active-directory/develop/active-directory-protocols-openid-connect-code.md#send-the-sign-in-request) artikel.

En exempel öppna ID Connect-begäran är:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD autentiserar användaren och, om det behövs, ombeds användaren att ge behörighet till appen. Den returnerar Auktoriseringskoden till Reply-URL: en för programmet. Beroende på den begärda response_mode Azure AD antingen skickar tillbaka data i frågesträngen eller som postdata.

Är ett exempel öppna ID Connect svar:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Tokenbegäran (OAuth2.0 kod bevilja flöda)
Nu när ditt program har fått Auktoriseringskoden från Azure AD, är det dags att hämta den åtkomsttoken för Azure Resource Manager.  Efter en OAuth2.0 kod bevilja Token begäran till tokenslutpunkten för Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Frågan string-parametrar som är tillgängliga för denna begäran beskrivs i den [använder Auktoriseringskoden](../active-directory/develop/active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) artikel.

I följande exempel visas en begäran om koden bevilja token med lösenord autentiseringsuppgifter:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

När du arbetar med autentiseringsuppgifter för certifikat, skapa en JSON-Webbtoken (JWT) och logga (RSA SHA256) med hjälp av den privata nyckeln för certifikatet autentiseringsuppgifterna för ditt program. Anspråkstyper för token som visas i [JWT-token anspråk](../active-directory/develop/active-directory-protocols-oauth-code.md#jwt-token-claims). Referenser finns i [Active Directory Authentication Library (.NET) kod](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs) att signera klienten Assertion JWT-token.

Finns det [öppna ID Connect spec](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) information om klientautentisering.

I följande exempel visas en begäran om koden bevilja token med autentiseringsuppgifter för certifikat:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Ett exempelsvar för kod bevilja token:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Hantera koden bevilja token svar
Ett lyckat svar för token innehåller (användare + app) åtkomst-token för Azure Resource Manager. Programmet använder åtkomsttoken att få åtkomst till Resource Manager för användarens räkning. Livslängden för åtkomst-token som utfärdas av Azure AD är en timme. Det är inte troligt att ditt webbprogram måste förnya (användare + app) åtkomst-token. Använd den uppdateringstoken som programmet tar emot den token som svar om den behöver förnya den åtkomst-token. Efter en OAuth2.0 Token begäran till tokenslutpunkten för Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Parametrar som ska användas med uppdateringsbegäran om beskrivs i [uppdatera åtkomsttoken](../active-directory/develop/active-directory-protocols-oauth-code.md#refreshing-the-access-tokens).

I följande exempel visas hur du använder uppdateringen token:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Även om uppdaterings-tokens kan användas för att hämta ny åtkomsttoken för Azure Resource Manager, är de inte lämplig för offline-åtkomst av ditt program. Uppdatera token livstid är begränsat och uppdateringstoken som är bundna till användaren. Om användaren lämnar organisationen, förlorat det program som använder uppdateringstoken åtkomst. Den här metoden är inte lämplig för program som används av grupper för att hantera sina Azure-resurser.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Kontrollera om användaren kan ge behörighet till prenumeration
Ditt program nu har en token för åtkomst till Azure Resource Manager för användarens räkning. Nästa steg är att ansluta din app till prenumerationen. När du ansluter kan din app kan hantera dessa prenumerationer även när användaren inte finns (långsiktiga offlineåtkomst).

Varje prenumeration att ansluta anropa den [Resource Manager listbehörigheter](https://docs.microsoft.com/rest/api/authorization/permissions) API för att avgöra om användaren har behörighet för hantering för prenumerationen.

Den [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) metod för ASP.NET MVC-exempelapp implementerar det här anropet.

I följande exempel visas hur du begär en användares behörigheter för en prenumeration. 83cfe939-2402-4581-b761-4f59b0a041e4 är ID för prenumerationen.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Det är ett exempel på svaret att hämta användarens behörigheter på prenumeration:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

Behörigheter API returnerar flera behörigheter. Varje behörighet består av tillåtna åtgärder (**åtgärder**) och otillåtna åtgärder (**notactions**). Om en åtgärd i tillåtna åtgärder för vilken behörighet och det finns inte i otillåtna åtgärder för den behörigheten, användaren tillåts att utföra åtgärden. **Microsoft.Authorization/RoleAssignments/Write** är den åtgärd som beviljar åtkomst rights management. Programmet måste tolka resultatet behörigheter att leta efter en matchning för regex på den här åtgärden strängen i den **åtgärder** och **notactions** för varje behörighet.

## <a name="get-app-only-access-token"></a>Hämta endast app-åtkomst-token
Nu vet du om användaren kan ge behörighet till Azure-prenumerationen. Nästa steg är:

1. Tilldela rollen RBAC till programmets identitet i prenumerationen.
2. Verifiera åtkomst tilldelningen genom att fråga om programmets tillstånd på prenumerationen eller genom att öppna Resource Manager med endast app-token.
3. Registrera anslutningen i ditt program ”anslutna prenumerationer” datastruktur - beständighet ID för prenumerationen.

Nu ska vi titta närmare på det första steget. Du måste bestämma om du vill tilldela rollen RBAC till programmets identitet.

* Objekt-ID för programmets identitet i användarens Azure Active Directory
* Identifierare för rollen RBAC som krävs för ditt program för prenumerationen

När programmet autentiserar en användare från en Azure AD, skapas ett huvudnamn service-objekt för tillämpningsprogrammet i den Azure AD. Azure kan RBAC-roller som ska tilldelas till tjänstens huvudnamn för att ge direktåtkomst till motsvarande program på Azure-resurser. Den här åtgärden är exakt vad du vill göra. Frågan Azure AD Graph API för att identifiera identifierare för tjänstens huvudnamn för programmet i den inloggade användaren har Azure AD.

Du bara ha en åtkomst-token för Azure Resource Manager - du behöver en ny åtkomsttoken att anropa Azure AD Graph API. Alla program i Azure AD har behörighet att fråga ett eget huvudnamn serviceobjektet, så att endast app-åtkomst-token är tillräckliga.

<a id="app-azure-ad-graph" />

### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Hämta endast app-åtkomst-token för Azure AD Graph API
För att autentisera din app och hämta en token för Azure AD Graph API, utfärda en tokenbegäran av flödet klientens autentiseringsuppgifter bevilja OAuth2.0 till Azure AD-token för slutpunkt (**https://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token**).

Den [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) metod i ASP.net MVC-exempelprogram hämtar endast app-åtkomst-token för Graph API med Active Directory-Autentiseringsbibliotek för .NET.

Frågan string-parametrar som är tillgängliga för denna begäran beskrivs i den [begära en åtkomst-Token](../active-directory/develop/active-directory-protocols-oauth-service-to-service.md#request-an-access-token) artikel.

En exempelbegäran för klientreferensen bevilja token:

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Ett exempelsvar för klientreferensen bevilja token:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Hämta ObjectId för programmet tjänstens huvudnamn i användaren Azure AD
Nu kan använda endast app-åtkomst-token till frågan i [Azure AD Graph tjänstens huvudnamn](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) API för att fastställa objekt-ID för programmets tjänstens huvudnamn i katalogen.

Den [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) metod i ASP.net MVC-exempelprogram implementerar det här anropet.

I följande exempel visas hur du begär ett program tjänstens huvudnamn. a0448380-c346-4f9f-b897-c18733de9394 är klient-ID för programmet.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

I följande exempel visas ett svar på begäran för ett program service principal

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Hämta Azure RBAC roll-ID
Om du vill tilldela rollen RBAC till tjänstens huvudnamn, måste du bestämma identifierare för Azure RBAC-rollen.

Rätt RBAC roll för ditt program:

* Om ditt program övervakar endast prenumerationen, utan ändringar, kräver endast läsare behörigheter i prenumerationen. Tilldela den **Reader** roll.
* Om ditt program hanterar Azure-prenumeration, skapa/Ändra/ta bort enheter kräver en av deltagare behörigheter.
  * Om du vill hantera en viss typ av resurs, tilldela deltagare resursspecifika roller (Virtual Machine-deltagare, Virtual Network-deltagare, Storage-konto deltagare, etc.)
  * För att hantera alla resurstyp, tilldela den **deltagare** roll.

Rolltilldelning för ditt program är synliga för användare, så du väljer minst krävs behörighet.

Anropa den [rolldefinitionen för Resource Manager API](https://docs.microsoft.com/rest/api/authorization/roledefinitions) Rada upp alla Azure RBAC-roller och sök sedan iterera över resultatet till önskade rolldefinitionen Sök efter namn.

Den [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) metod för ASP.net MVC-exempelapp implementerar det här anropet.

I följande exempel begäran visar hur du hämtar Azure RBAC Rollidentifierare. 09cbd307-aa71-4aca-b346-5f253e6e3ebb är ID för prenumerationen.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

Svaret är i följande format:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Du behöver inte anropa denna API kontinuerligt. När du har bestämt välkända GUID för rolldefinitionen kan du skapa Definitions-ID för rollen som:

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

### <a name="assign-rbac-role-to-application"></a>Tilldela RBAC roll till programmet
Du har allt du behöver för att tilldela rollen RBAC till tjänstens huvudnamn med hjälp av den [Resource Manager skapa rolltilldelning](https://docs.microsoft.com/rest/api/authorization/roleassignments) API.

Den [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) metod för ASP.net MVC-exempelapp implementerar det här anropet.

En exempelbegäran att tilldela programmet RBAC roll:

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

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Hämta endast app-åtkomsttoken för Azure Resource Manager
Om du vill validera appen har önskad åt på prenumerationen, utför en test-aktivitet på prenumerationen med en app-only-token.

Följ anvisningarna för att få en endast app-åtkomst-token från avsnittet [Hämta endast app-åtkomst-token för Azure AD Graph API](#app-azure-ad-graph), med ett annat värde för resursparametern:

    https://management.core.windows.net/

Den [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) metod för exempelprogrammet ASP.NET MVC hämtar endast app-åtkomst-token för Azure Resource Manager med hjälp av Active Directory-Autentiseringsbibliotek för .net.

#### <a name="get-applications-permissions-on-subscription"></a>Hämta programmets behörigheter för prenumerationen
Om du vill kontrollera att programmet har åtkomst på en Azure-prenumeration kan du också anropa den [Resource Manager behörigheter](https://docs.microsoft.com/rest/api/authorization/permissions) API. Den här metoden liknar hur du bestämma om användaren har behörighet för åtkomsthantering för prenumerationen. Den här tiden kan anropa dock behörigheter API med endast app-åtkomst-token som du fick i föregående steg.

Den [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) metod för ASP.NET MVC-exempelapp implementerar det här anropet.

## <a name="manage-connected-subscriptions"></a>Hantera anslutna prenumerationer
När rollen RBAC tilldelas till programmets tjänstens huvudnamn på prenumerationen kan tillämpningsprogrammet hålla övervakning/hantera den med hjälp av endast app-åtkomst-token för Azure Resource Manager.

Om en prenumeration ägare tar bort rolltilldelningen i ditt program med hjälp av portalen eller kommandoradsverktyg, ditt program inte längre komma åt den prenumerationen. I så fall bör du meddela användaren om att anslutningen med prenumerationen fjärrdisken bröts från utanför programmet och ge dem en alternativet ”Reparera” anslutningen. ”Reparera” skulle återskapa rolltilldelning som tagits offline.

Precis som du har aktiverat att användaren ska ansluta prenumerationer för ditt program måste du tillåta användare att koppla från prenumerationer för. Koppla från innebär att ta bort rolltilldelningen med programmets tjänstens huvudnamn på prenumerationen från en access management-synvinkel. Du kan också kan några tillstånd i programmet för prenumerationen tas bort för.
Endast användare med behörighet för hantering på prenumerationen kan du koppla från prenumerationen.

Den [RevokeRoleFromServicePrincipalOnSubscription metoden](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) för ASP.net MVC exempelapp implementerar det här anropet.

Det - användare kan nu enkelt ansluta och hantera sina Azure-prenumerationer med ditt program.
