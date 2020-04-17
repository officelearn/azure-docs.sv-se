---
title: Migrera till MSAL.NET
titleSuffix: Microsoft identity platform
description: Lär dig mer om skillnaderna mellan Microsoft Authentication Library for .NET (MSAL.NET) och Azure AD Authentication Library for .NET (ADAL.NET) och hur du migrerar till MSAL.NET.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: f389943d284c573312473f426048f8aadb79088e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533980"
---
# <a name="migrating-applications-to-msalnet"></a>Migrera program till MSAL.NET

Både Microsoft Authentication Library for .NET (MSAL.NET) och Azure AD Authentication Library for .NET (ADAL.NET) används för att autentisera Azure AD-entiteter och begära token från Azure AD. Hittills har de flesta utvecklare arbetat med Azure AD för utvecklarplattform (v1.0) för att autentisera Azure AD-identiteter (arbets- och skolkonton) genom att begära token med Hjälp av Azure AD Authentication Library (ADAL). Använda MSAL:

- Du kan autentisera en bredare uppsättning Microsoft-identiteter (Azure AD-identiteter och Microsoft-konton samt sociala och lokala konton via Azure AD B2C) när den använder slutpunkten för Microsoft-identitetsplattformen,
- användarna får den bästa upplevelsen för enkel inloggning.
- ditt program kan aktivera inkrementellt medgivande, och det är enklare att stödja villkorlig åtkomst
- du drar nytta av innovationen.

**MSAL.NET är nu det rekommenderade auth-biblioteket som ska användas med Microsofts identitetsplattform**. Inga nya funktioner kommer att implementeras på ADAL.NET. Insatserna är inriktade på att förbättra MSAL.

I den här artikeln beskrivs skillnaderna mellan Microsoft Authentication Library for .NET (MSAL.NET) och Azure AD Authentication Library for .NET (ADAL.NET) och hjälper dig att migrera till MSAL.

## <a name="differences-between-adal-and-msal-apps"></a>Skillnader mellan ADAL- och MSAL-appar

I de flesta fall vill du använda MSAL.NET och slutpunkten för Microsoft-identitetsplattformen, som är den senaste generationen microsoft-autentiseringsbibliotek. Med MSAL.NET får du token för användare som loggar in på ditt program med Azure AD (arbets- och skolkonton), Microsoft (personliga) konton (MSA) eller Azure AD B2C.

Om du redan är bekant med slutpunkten för Azure AD för utvecklare (v1.0) (och ADAL.NET) kanske du vill läsa [Vad är annorlunda med slutpunkten för Microsoft Identity Platform (v2.0).](active-directory-v2-compare.md)

Du måste dock fortfarande använda ADAL.NET om ditt program behöver logga in användare med tidigare versioner av [Active Directory Federation Services (ADFS)](/windows-server/identity/active-directory-federation-services). Mer information finns i [Stöd för ADFS](https://aka.ms/msal-net-adfs-support).

Följande bild sammanfattar några av skillnaderna mellan ADAL.NET och ![MSAL.NET sida vid sida-kod](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>NuGet paket och namnområden

ADAL.NET förbrukas från [paketet Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet. namnområdet som ska `Microsoft.IdentityModel.Clients.ActiveDirectory`användas är .

Om du vill använda MSAL.NET måste du lägga till [Paketet Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet och använda `Microsoft.Identity.Client` namnområdet

### <a name="scopes-not-resources"></a>Scope inte resurser

ADAL.NET hämtar token för *resurser*, men MSAL.NET hämtar token för *scope*. Ett antal MSAL.NET AcquireToken-åsidosättningar kräver en parameter`IEnumerable<string> scopes`som kallas scope( ). Den här parametern är en enkel lista med strängar som deklarerar önskade behörigheter och resurser som begärs. Välkända scope är [Microsoft Graph omfattningar](/graph/permissions-reference).

Det är också möjligt i MSAL.NET att komma åt v1.0-resurser. Se information i [Scope för ett v1.0-program](#scopes-for-a-web-api-accepting-v10-tokens).

### <a name="core-classes"></a>Kärnklasser

- ADAL.NET använder [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) som representation av anslutningen till STS (Security Token Service) eller auktoriseringsservern, via en myndighet. Tvärtom är MSAL.NET utformad kring [klientprogram](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications). Det ger två `PublicClientApplication` separata klasser: och`ConfidentialClientApplication`

- Skaffa token: ADAL.NET och MSAL.NET har samma`AcquireTokenAsync` autentiseringsanrop `AcquireTokenSilentAsync` ( och för `AcquireTokenInteractive` ADAL.NET `AcquireTokenSilent` och och i MSAL.NET) men med olika parametrar som krävs. En skillnad är det faktum att du MSAL.NET inte längre behöver `ClientID` passera i din ansökan i varje AcquireTokenXX-samtal. I själva `ClientID` verket är det som`IPublicClientApplication` `IConfidentialClientApplication`endast en gång när man bygger ( eller ).

### <a name="iaccount-not-iuser"></a>IAccount inte IUser

ADAL.NET manipulerade användare. En användare är dock en människa eller en programvaruagent, men den kan inneha/äga/vara ansvarig för ett eller flera konton i Microsofts identitetssystem (flera Azure AD-konton, Azure AD B2C, Microsofts personliga konton).

MSAL.NET 2.x definierar nu begreppet konto (via IAccount-gränssnittet). Den här brytande ändringen ger rätt semantik: det faktum att samma användare kan ha flera konton, i olika Azure AD-kataloger. MSAL.NET ger också bättre information i gästscenarier, eftersom hemkontoinformation tillhandahålls.

Mer information om skillnaderna mellan IUser och IAccount finns [i MSAL.NET 2.x](https://aka.ms/msal-net-2-released).

### <a name="exceptions"></a>Undantag

#### <a name="interaction-required-exceptions"></a>Interaktion krävs undantag

MSAL.NET har tydligare undantag. Till exempel när tyst autentisering misslyckas i ADAL proceduren `user_interaction_required` är att fånga undantaget och leta efter felkoden:

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == "user_interaction_required")
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

Se information i [Det rekommenderade mönstret för att hämta en token](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) med ADAL.NET

Med hjälp av MSAL.NET `MsalUiRequiredException` fångar du som beskrivs i [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token).

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Hantering av undantag för anspråksutmaning

I ADAL.NET hanteras undantag för anspråksutmaning på följande sätt:

- `AdalClaimChallengeException`är ett undantag (som `AdalServiceException`härrör från) som genereras av tjänsten om en resurs kräver fler anspråk från användaren (till exempel tvåfaktorsautentisering). Medlemmen `Claims` innehåller något JSON-fragment med fordrar, som förväntas.
- Fortfarande i ADAL.NET, måste det offentliga klientprogrammet som `AcquireTokenInteractive` tar emot det här undantaget anropa åsidosättningen med en anspråksparameter. Denna åsidosättning av `AcquireTokenInteractive` inte ens försöka träffa cachen eftersom det inte är nödvändigt. Anledningen är att token i cacheminnet inte har `AdalClaimChallengeException` rätt anspråk (annars skulle en inte ha kastats). Därför finns det ingen anledning att titta på cachen. Observera att `ClaimChallengeException` kan tas emot i en WebAPI `AcquireTokenInteractive` gör OBO, medan behovet av att anropa i ett offentligt klientprogram som anropar det här webb-API:et.
- för mer information, inklusive prover se Hantering [AdalClaimChallengeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception)

I MSAL.NET hanteras undantag för anspråksutmaning på följande sätt:

- De `Claims` är dykt `MsalServiceException`upp i .
- Det finns `.WithClaim(claims)` en metod som `AcquireTokenInteractive` kan tillämpas på byggaren.

### <a name="supported-grants"></a>Bidrag som stöds

Alla bidrag stöds ännu inte i MSAL.NET och v2.0-slutpunkten. Följande är en sammanfattning som jämför ADAL.NET och MSAL. NET:s bidrag som stöds.

#### <a name="public-client-applications"></a>Program för offentliga klienter

Här är bidragen som stöds i ADAL.NET och MSAL.NET för stationära och mobila applikationer

Bevilja | ADAL.NET | MSAL.NET
----- |----- | -----
Interaktiv | [Interaktiv Auth](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [Skaffa token interaktivt i MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively)
Integrerad Windows-autentisering | [Integrerad autentisering i Windows (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Integrerad Windows-autentisering](msal-authentication-flows.md#integrated-windows-authentication)
Användarnamn / Lösenord | [Skaffa token med användarnamn och lösenord](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Autentisering av användarnamnslösenord](msal-authentication-flows.md#usernamepassword)
Flöde av enhetskod | [Enhetsprofil för enheter utan webbläsare](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Flöde av enhetskod](msal-authentication-flows.md#device-code)

#### <a name="confidential-client-applications"></a>Konfidentiella klientprogram

Här är bidragen som stöds i ADAL.NET och MSAL.NET för webbprogram, webb-API:er och daemonprogram:

Typ av app | Bevilja | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Webbapp, webb-API, demon | Klientautentiseringsuppgifter | [Klientautentiseringsflöden i ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [Klientautentiseringsflöden i MSAL.NET](msal-authentication-flows.md#client-credentials))
Webb-API | På uppdrag av | [Service till servicesamtal för användarens räkning med ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [På uppdrag av i MSAL.NET](msal-authentication-flows.md#on-behalf-of)
Webbapp | Auth-kod | [Skaffa token med auktoriseringskoder på webbappar med ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [Skaffa token med auktoriseringskoder på webbappar med A MSAL.NET](msal-authentication-flows.md#authorization-code)

### <a name="cache-persistence"></a>Cache persistens

ADAL.NET kan du utöka `TokenCache` klassen för att implementera önskad persistensfunktion på plattformar utan en säker lagring (.NET Framework och .NET core) med hjälp av `BeforeAccess`och `BeforeWrite` metoder. Mer information finns i Serialisering av [tokencache i ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization).

MSAL.NET gör tokencachen till en sluten klass och tar bort möjligheten att utöka den. Därför måste implementeringen av tokencachen vara i form av en hjälpklass som interagerar med den förseglade tokencachen. Den här interaktionen beskrivs i [Serialisering av tokencachen i MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization).

## <a name="signification-of-the-common-authority"></a>Undertecknare av den gemensamma myndigheten

I v1.0, om `https://login.microsoftonline.com/common` du använder myndigheten, tillåter du användare att logga in med alla AAD-konton (för alla organisationer). Se [Myndighetsvalidering i ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

Om du `https://login.microsoftonline.com/common` använder myndigheten i v2.0 tillåter du användare att logga in med någon AAD-organisation eller ett personligt Microsoft-konto (MSA). Om du MSAL.NET vill begränsa inloggningen till ett AAD-konto (samma beteende som med `https://login.microsoftonline.com/organizations`ADAL.NET) måste du använda. Mer information finns `authority` i parametern i [det offentliga klientprogrammet](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication).

## <a name="v10-and-v20-tokens"></a>v1.0- och v2.0-tokens

Det finns två versioner av token:
- v1.0-token
- v2.0-token

V1.0-slutpunkten (används av ADAL) avger endast v1.0-token.

Men v2.0-slutpunkten (som används av MSAL) avger den version av token som webb-API:et accepterar. En egenskap för webb-API:ets programmanifest gör det möjligt för utvecklare att välja vilken version av token som ska accepteras. Se `accessTokenAcceptedVersion` i referensdokumentationen [för programmanifestet.](reference-app-manifest.md)

Mer information om v1.0- och v2.0-token finns i [Azure Active Directory-åtkomsttoken](access-tokens.md)

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Scope för ett webb-API som accepterar v1.0-token

OAuth2-behörigheter är behörighetsscope som ett v1.0-webb-API-program (resurs) exponerar för klientprogram. Dessa behörighetsscope kan beviljas klientprogram under medgivandet. Se avsnittet om oauth2Permissions i [Azure Active Directory-programmanifest .](active-directory-application-manifest.md)

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Scope för att begära åtkomst till specifika OAuth2-behörigheter för ett v1.0-program

Om du vill hämta token för ett program som accepterar v1.0-token https://graph.microsoft.com)(till exempel Microsoft `scopes` Graph API, vilket är , måste du skapa genom att sammanfoga en önskad resursidentifierare med en önskad OAuth2-behörighet för den resursen.

Om du till exempel vill komma åt ett v1.0-webb-API `ResourceId`som App ID URI är i namnet på användaren vill du använda:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

Om du vill läsa och skriva med MSAL.NET Azure Activehttps://graph.microsoft.com/)Directory med Hjälp av Microsoft Graph API ( skulle du skapa en lista över scope som i följande kodavsnitt:

```csharp
ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Varning: Om du har ett eller två snedstreck i det omfång som motsvarar ett v1.0-webb-API

Om du vill skriva det scope som motsvararhttps://management.core.windows.net/)Azure Resource Manager API ( måste du begära följande omfång (notera de två snedstrecken)

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Detta beror på att Resource Manager API förväntar`aud`sig ett snedstreck i sin målgrupp anspråk ( ), och sedan finns det ett snedstreck för att skilja API-namnet från omfånget.

Logiken som används av Azure AD är följande:
- För ADAL-slutpunkt (v1.0) med en v1.0-åtkomsttoken (den enda möjliga), aud=resurs
- För MSAL (v2.0-slutpunkt) som ber en åtkomsttoken för en resurs som accepterar v2.0-token, aud=resource. Appid
- För MSAL (v2.0-slutpunkt) som ber en åtkomsttoken för en resurs som accepterar en v1.0-åtkomsttoken (vilket är fallet ovan), tolkar Azure AD önskad målgrupp från det begärda omfånget genom att ta allt före det sista snedstrecket och använda den som resursidentifierare. Om https:\//database.windows.net förväntar sig enhttps://database.windows.net/målgrupp av " ", måste du\/därför begära ett scope på https: /database.windows.net//.default. Se även nummer #[747:](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)Resource url s avslutande snedstreck utelämnas, vilket orsakade SQL Auth fel #747


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Scope för att begära åtkomst till alla behörigheter för ett v1.0-program

Om du till exempel vill hämta en token för alla statiska scope för ett v1.0-program måste man använda

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>Scope att begära när det gäller klientautentiseringsflöde / daemon app

När det gäller klientautentiseringsflöde skulle omfattningen för att `/.default`passera också vara . Det här scopet berättar för Azure AD: "alla behörigheter på appnivå som administratören har samtyckt till i programregistreringen.

## <a name="adal-to-msal-migration"></a>ADAL till MSAL-migrering

I ADAL.NET v2. X, var uppdateringstokensna exponerade så att du kan utveckla lösningar kring `AcquireTokenByRefreshToken` användningen av dessa tokens genom att cachelagring dem och använda de metoder som tillhandahålls av ADAL 2.x.
Några av dessa lösningar användes i scenarier som:
* Tidskrävande tjänster som utför åtgärder, inklusive att uppdatera instrumentpaneler för användarnas räkning, medan användarna inte längre är anslutna.
* WebFarm-scenarier för att göra det möjligt för klienten att föra RT till webbtjänsten (cachelagring görs på klientsidan, krypterad cookie och inte serversidan)

MSAL.NET exponerar inte uppdateringstoken, av säkerhetsskäl: MSAL hanterar uppfriskande token åt dig.

Lyckligtvis har MSAL.NET nu ett API som gör att du kan migrera dina tidigare uppdateringstoken (som förvärvats med ADAL) `IConfidentialClientApplication`till:

```csharp
/// <summary>
/// Acquires an access token from an existing refresh token and stores it and the refresh token into
/// the application user token cache, where it will be available for further AcquireTokenSilent calls.
/// This method can be used in migration to MSAL from ADAL v2 and in various integration
/// scenarios where you have a RefreshToken available.
/// (see https://aka.ms/msal-net-migration-adal2-msal2)
/// </summary>
/// <param name="scopes">Scope to request from the token endpoint.
/// Setting this to null or empty will request an access token, refresh token and ID token with default scopes</param>
/// <param name="refreshToken">The refresh token from ADAL 2.x</param>
IByRefreshToken.AcquireTokenByRefreshToken(IEnumerable<string> scopes, string refreshToken);
```

Med den här metoden kan du ange den tidigare använda uppdateringstoken tillsammans med eventuella scope (resurser) som du önskar. Uppdateringstoken kommer att bytas ut mot en ny och cachelagras till ditt program.

Eftersom den här metoden är avsedd för scenarier som inte är `IConfidentialClientApplication` typiska, `IByRefreshToken`är den inte lätt åtkomlig utan att först kasta den till .

Det här kodavsnittet visar en del migreringskod i ett konfidentiellt klientprogram. `GetCachedRefreshTokenForSignedInUser`hämta uppdateringstoken som lagrades i viss lagring av en tidigare version av programmet som används för att utnyttja ADAL 2.x. `GetTokenCacheForSignedInUser`deserialiserar en cache för den inloggade användaren (eftersom konfidentiella klientprogram bör ha en cache per användare).

```csharp
TokenCache userCache = GetTokenCacheForSignedInUser();
string rt = GetCachedRefreshTokenForSignedInUser();

IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(clientId)
 .WithAuthority(Authority)
 .WithRedirectUri(RedirectUri)
 .WithClientSecret(ClientSecret)
 .Build();
IByRefreshToken appRt = app as IByRefreshToken;

AuthenticationResult result = await appRt.AcquireTokenByRefreshToken(null, rt)
                                         .ExecuteAsync()
                                         .ConfigureAwait(false);
```

Du kommer att se en åtkomsttoken och ID-token som returneras i ditt AuthenticationResult medan din nya uppdateringstoken lagras i cacheminnet.

Du kan också använda den här metoden för olika integrationsscenarier där du har en uppdateringstoken tillgänglig.

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om scope i [Scopes, behörigheter och samtycke i slutpunkten för Microsoft-identitetsplattform](v2-permissions-and-consent.md)
