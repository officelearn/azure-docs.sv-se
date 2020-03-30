---
title: Azure AD B2C (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om specifika överväganden när du använder Azure AD B2C med Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2019
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 697b4bc8e3a25085ac6f7d600ea2227dd30a6624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262820"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Använda MSAL.NET för att logga in användare med sociala identiteter

Du kan använda MSAL.NET för att logga in användare med sociala identiteter med hjälp av [Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c). Azure AD B2C är uppbyggd kring begreppet principer. I MSAL.NET översätts att ge en myndighet om du anger en princip.

- När du instansierar det offentliga klientprogrammet måste du ange principen i auktoriteten.
- När du vill tillämpa en princip måste du `AcquireTokenInteractive` anropa `authority` en åsidosättning av att innehålla en parameter.

Den här sidan är avsedd för MSAL 3.x. Om du är intresserad av MSAL 2.x läser du [Azure AD B2C-detaljer i MSAL 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Myndighet för en Azure AD B2C-klient och princip

Den myndighet som `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}` skall användas är när

- `azureADB2CHostname`är namnet på Azure AD B2C-klienten `{your-tenant-name}.b2clogin.com`plus värden (till exempel),
- `tenant`är det fullständiga namnet på Azure AD B2C-klienten (till `{your-tenant-name}.onmicrosoft.com`exempel) eller GUID för klienten, 
- `policyName`namnet på den princip eller det användarflöde som ska tillämpas (till exempel "b2c_1_susi" för registrering/inloggning).

Mer information om Azure AD B2C-myndigheterna finns i den här [dokumentationen](/azure/active-directory-b2c/b2clogin).

## <a name="instantiating-the-application"></a>Instansiera programmet

När du skapar programmet måste du ange myndigheten.

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string AzureADB2CHostname = "fabrikamb2c.b2clogin.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://{AzureADB2CHostname}/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>Skaffa en token för att tillämpa en princip

Om du hämtar en token för ett Azure AD B2C-skyddat API i ett offentligt klientprogram måste du använda åsidosättningarna med en behörighet:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireTokenInteractive(scopes)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .WithParentActivityOrWindow(ParentActivityOrWindow)
                                            .ExecuteAsync();
```

med:

- `policy`är en av de tidigare `PolicySignUpSignIn`strängarna (till exempel).
- `ParentActivityOrWindow`krävs för Android (aktiviteten) och valfritt för andra plattformar som stöder det överordnade användargränssnittet, till exempel fönster i Windows och UIViewController i iOS. Se mer information [här i dialogrutan Användargränssnitt](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)`är en metod som hittar ett konto för en viss princip. Ett exempel:

  ```csharp
  private IAccount GetAccountByPolicy(IEnumerable<IAccount> accounts, string policy)
  {
   foreach (var account in accounts)
   {
    string userIdentifier = account.HomeAccountId.ObjectId.Split('.')[0];
    if (userIdentifier.EndsWith(policy.ToLower()))
     return account;
   }
   return null;
  }
  ```

Att tillämpa en princip eller ett användarflöde (till exempel låta slutanvändaren redigera `AcquireTokenInteractive`sin profil eller återställa sitt lösenord) görs för närvarande genom att ringa . När det gäller dessa två principer använder du inte det returnerade token/autentiseringsresultatet.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Specialfall av EditProfile och ResetPassword-principer

När du vill ge en upplevelse där dina slutanvändare loggar in med en social identitet och sedan redigerar sin profil, vill du tillämpa azure AD B2C Edit Profile-principen. Sättet att göra detta `AcquireTokenInteractive` är genom att ringa med den specifika `Prompt.NoPrompt` behörigheten för den principen och en fråga för att förhindra att dialogrutan för kontoval visas (eftersom användaren redan är inloggad och har en aktiv cookie-session).

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
 IEnumerable<IAccount> accounts = await app.GetAccountsAsync();
 try
 {
  var authResult = await app.AcquireToken(scopes:App.ApiScopes)
                               .WithAccount(GetUserByPolicy(accounts, App.PolicyEditProfile)),
                               .WithPrompt(Prompt.NoPrompt),
                               .WithB2CAuthority(App.AuthorityEditProfile)
                               .ExecuteAsync();
  DisplayBasicTokenInfo(authResult);
 }
 catch
 {
  . . .
 }
}
```
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Autentiseringsuppgifter för resursägare (ROPC) med Azure AD B2C
Mer information om ROPC-flödet finns i den här [dokumentationen](v2-oauth-ropc.md).

Det här flödet **rekommenderas inte** eftersom ditt program som ber en användare om deras lösenord inte är säkert. Mer information om det här problemet finns i [den här artikeln](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Genom att använda användarnamn /lösenord, ger du upp ett antal saker:
- Centrala grundsatser för modern identitet: lösenord blir fiskas, spelas upp igen. Eftersom vi har detta begrepp om en aktie hemlighet som kan avlyssnas. Detta är inkompatibelt med lösenordslös.
- Användare som behöver göra MFA kommer inte att kunna logga in (eftersom det inte finns någon interaktion).
- Användarna kan inte göra enkel inloggning.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Konfigurera ROPC-flödet i Azure AD B2C
Skapa ett nytt användarflöde i din Azure AD B2C-klient och välj **Logga in med ROPC**. Detta aktiverar ROPC-principen för din klient. Mer information finns i Konfigurera flödet för lösenord för [resursägare.](/azure/active-directory-b2c/configure-ropc)

`IPublicClientApplication`innehåller en metod:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Den här metoden tar som parametrar:
- *Scopen* för att begära en åtkomsttoken för.
- Ett *användarnamn*.
- Ett *SecureString-lösenord* för användaren.

Kom ihåg att använda den myndighet som innehåller ROPC-principen.

### <a name="limitations-of-the-ropc-flow"></a>Begränsningar av ROPC-flödet
 - ROPC-flödet **fungerar bara för lokala konton** (där du registrerar dig med Azure AD B2C med hjälp av ett e-postmeddelande eller användarnamn). Det här flödet fungerar inte om det matas till någon av de identitetsleverantörer som stöds av Azure AD B2C (Facebook, Google osv.).

## <a name="google-auth-and-embedded-webview"></a>Google Auth och inbäddad webview

Om du är en Azure AD B2C-utvecklare som använder Google som identitetsleverantör rekommenderar vi att du använder systembläddraren, eftersom Google inte tillåter [autentisering från inbäddade webbvyer](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). För `login.microsoftonline.com` närvarande är en betrodd myndighet med Google. Med hjälp av den här behörigheten kommer att arbeta med inbäddade webview. Men `b2clogin.com` att använda är inte en betrodd myndighet med Google, så användarna kommer inte att kunna autentisera.

Vi kommer att tillhandahålla en uppdatering av det här [problemet](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) om saker och ting ändras.

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Cachelagring med Azure AD B2C i MSAL.Net 

### <a name="known-issue-with-azure-ad-b2c"></a>Känt problem med Azure AD B2C

MSAL.Net stöder en [tokencache](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). Tokencacheing-nyckeln baseras på de anspråk som returneras av identitetsprovidern. För närvarande behöver MSAL.Net två anspråk för att skapa en tokencachenyckel:  
- `tid`som är Azure AD-klient-ID och 
- `preferred_username` 

Båda dessa anspråk saknas i många av Azure AD B2C-scenarierna. 

Kundpåverkan är att när du försöker visa användarnamnsfältet, får du "Saknas från tokensvaret" som värde? Om så är fallet beror detta på att Azure AD B2C inte returnerar ett värde i IdToken för preferred_username på grund av begränsningar med sociala konton och externa identitetsleverantörer (IdPs). Azure AD returnerar ett värde för preferred_username eftersom den vet vem användaren är, men för Azure AD B2C, eftersom användaren kan logga in med ett lokalt konto, Facebook, Google, GitHub, etc. det finns inte ett konsekvent värde för Azure AD B2C att använda för preferred_username. För att avblockera MSAL från att distribuera cachekompatibilitet med ADAL bestämde vi oss för att använda "Saknas från tokensvaret" i vår ände när du hanterar Azure AD B2C-kontona när IdToken inte returnerar något för preferred_username. MSAL måste returnera ett värde för preferred_username för att upprätthålla cachekompatibilitet mellan bibliotek.

### <a name="workarounds"></a>Provisoriska lösningar

#### <a name="mitigation-for-the-missing-tenant-id"></a>Begränsning av klient-ID saknas

Den föreslagna lösningen är att använda [cachelagring efter princip](#acquire-a-token-to-apply-a-policy)

Alternativt kan du använda `tid` anspråket om du använder [de anpassade principerna för B2C,](https://aka.ms/ief)eftersom det ger möjlighet att returnera ytterligare anspråk till programmet. Om du vill veta mer om [anspråkstransformation](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Begränsning för "Saknas från tokensvaret"
Ett alternativ är att använda "namn"-anspråket som önskat användarnamn. Processen nämns i detta [B2C doc->](../../active-directory-b2c/user-flow-overview.md) "I kolumnen Returanspråk väljer du de anspråk som du vill returnera i auktoriseringstoken som skickas tillbaka till ditt program efter en lyckad profilredigeringsupplevelse. Välj till exempel Visningsnamn, Postnummer."

## <a name="next-steps"></a>Nästa steg 

Mer information om hur du hämtar token interaktivt med MSAL.NET för Azure AD B2C-program finns i följande exempel.

| Exempel | Plattform | Beskrivning|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | En enkel Xamarin Forms-app som visar hur du använder MSAL.NET för att autentisera användare via Azure AD B2C och komma åt ett webb-API med de resulterande tokens.|
