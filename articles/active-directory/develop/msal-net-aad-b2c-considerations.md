---
title: Azure AD B2C (Microsoft Authentication Library för .NET) | Azure
description: Läs mer om specifika överväganden när du använder Azure AD B2C med Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c608518a9eb80d807297f010778ae452c0f61f5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075782"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Använd MSAL.NET för att logga in användare med sociala identiteter

Du kan använda MSAL.NET för att logga in användare med sociala identiteter med hjälp av [Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c). Azure AD B2C är uppbyggd kring begreppet principer. I MSAL.NET innebär ange en princip att tillhandahålla en utfärdare.

- När du instansiera offentliga klientprogrammet måste du ange vilken princip myndighet.
- När du vill tillämpa en princip måste du anropa en åsidosättning av `AcquireTokenInteractive` som innehåller en `authority` parametern.

Den här sidan är för MSAL 3.x. Om du är intresserad av MSAL 2.x kan se [Azure AD B2C som anges i MSAL 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Utfärdaren av ett Azure AD B2C-klient och en princip

Behörighet att använda är `https://login.microsoftonline.com/tfp/{tenant}/{policyName}` där:

- `tenant` är namnet på Azure AD B2C-klient 
- `policyName` namnet på principen ska tillämpas (till exempel ”b2c_1_susi” för att logga-i/registrering).

Den aktuella vägledningen från Azure AD B2C är att använda `b2clogin.com` som utfärdare. Till exempel `$"https://{your-tenant-name}.b2clogin.com/tfp/{your-tenant-ID}/{policyname}"`. Mer information finns i den här [dokumentation](/azure/active-directory-b2c/b2clogin).

## <a name="instantiating-the-application"></a>Instansiera programmet

När du skapar programmet som du behöver ge behörighet.

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://fabrikamb2c.b2clogin.com/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>Hämta en token för att tillämpa en princip

Skaffa en token för en Azure AD B2C måste skyddade API i en offentlig klientprogram du använda åsidosättningar med en utfärdare:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireToken(scopes, parentWindow)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .ExecuteAsync();
```

med:

- `policy` som en av de föregående strängarna (till exempel `PolicySignUpSignIn`).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)` är en metod som söker efter ett konto för en viss princip. Exempel:

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

Tillämpa en princip (till exempel så att användaren redigera sin profil eller återställa sina lösenord) för närvarande är klar genom att anropa `AcquireTokenInteractive`. När det gäller de här två principer du inte använder den returnerade token / autentisering resulterar.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Specialfall principer för EditProfile och ResetPassword

När du vill skapa en upplevelse där dina slutanvändare logga in med en sociala identitet och sedan redigera sin profil som du vill tillämpa principen för Azure AD B2C EditProfile. Sätt att göra detta på är att anropa `AcquireTokenInteractive` med specifik behörighet för principen och en fråga som angetts till `Prompt.NoPrompt` att undvika dialogrutan för val av konto som ska visas (som användaren är redan inloggad)

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
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Resursen ägare lösenordsinformation (ROPC) med Azure AD B2C
Mer information om ROPC flödet finns i denna [dokumentation](v2-oauth-ropc.md).

Det här flödet är **rekommenderas inte** eftersom ditt program som en användare ombeds ange sina lösenord inte är säker. Mer information om det här problemet finns i [i den här artikeln](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Genom att använda användarnamn/lösenord kan du ge upp ett antal saker:
- viktiga innehavare av moderna identitet: lösenord hämtar fiskas återupprepas. Eftersom vi har detta begrepp av en resurs-hemlighet som kan fångas. Det här är inte kompatibel med lösenordslös.
- Användare som behöver du MFA kan inte logga in (eftersom det finns inga åtgärder från).
- Användare kommer inte att kunna enkel inloggning.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Konfigurera ROPC flödet i Azure AD B2C
Skapa ett nytt användarflöde i din Azure AD B2C-klient och välj **logga in med ROPC**. Detta aktiverar principen ROPC för din klient. Se [konfigurera lösenordsinformation för resurs-ägare flow](/azure/active-directory-b2c/configure-ropc) för mer information.

`IPublicClientApplication` innehåller en metod:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Den här metoden tar som parametrar:
- Den *scope* begär en åtkomsttoken för.
- En *användarnamn*.
- En SecureString *lösenord* för användaren.

Kom ihåg att använda utfärdaren som innehåller ROPC principen.

### <a name="limitations-of-the-ropc-flow"></a>Begränsningar av ROPC flödet
 - Flödet ROPC **fungerar endast för lokala konton** (där du registrerar med Azure AD B2C med hjälp av en e-postadress eller användarnamn). Det här flödet fungerar inte om federering till någon av de identitetsprovidrar som stöds av Azure AD B2C (Facebook, Google, osv.).
 - Det finns för närvarande, **inga id_token har returnerats från Azure AD B2C** när du implementerar ROPC flödet från MSAL. Det innebär att inte går att skapa ett kontoobjekt, så i cacheminnet, det är inget konto utan att någon användare. AcquireTokenSilent flödet fungerar inte i det här scenariot. Dock ROPC visar inte ett gränssnitt, så det kommer utan att användarupplevelsen påverkas.

## <a name="google-auth-and-embedded-webview"></a>Google-autentisering och inbäddade Webview

Om du är en Azure AD B2C-utvecklare med hjälp av Google som identitetsprovider vi recommand du använder system-webbläsare som inte tillåter att Google [autentisering från sig av inbäddade Webbvyer](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). För närvarande `login.microsoftonline.com` är en betrodd utfärdare med Google. Med den här utfärdaren fungerar med inbäddade webbvy. Men med `b2clogin.com` är inte en betrodd utfärdare med Google, så användarna inte kommer att kunna autentisera.

Vi tillhandahåller en uppdatering i wiki och detta [problemet](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) ändrar saker.

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Cachelagring med Azure AD B2C i MSAL.Net 

### <a name="known-issue-with-azure-ad-b2c"></a>Kända problem med Azure AD B2C

MSAL.Net stöder en [token cache](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). Den token som cachelagring nyckel baseras på anspråk som returneras av identitetsleverantören. MSAL.Net måste för närvarande två anspråk för att skapa en token-cache-nyckel:  
- `tid` vilket är Azure AD-klient-ID och 
- `preferred_username` 

Båda dessa anspråk saknas i många scenarier för Azure AD B2C. 

Kund-effekten är att när du försöker visa fältet för användarnamn, får du ”saknas från svaret som token” som värde? I så fall beror det på att Azure AD B2C inte returnerar något värde i IdToken för preferred_username på grund av begränsningar med konton i sociala medier och externa identitetsleverantörer (IDP: er). Azure AD returnerar ett värde för preferred_username eftersom den vet vem användaren är, men för Azure AD B2C, eftersom användaren kan logga in med ett lokalt konto, Facebook, Google, GitHub, m.m. det är inte ett konsekvent värde för Azure AD B2C ska användas för preferred_username. Om du vill avblockera MSAL från lansera cache kompatibilitet med ADAL, beslutat att använda ”saknas från svaret som token” hos oss när du hanterar Azure AD B2C-konton när IdToken returnerar något för preferred_username. MSAL måste returnera ett värde för preferred_username att bibehålla kompatibilitet för cache i bibliotek.

### <a name="workarounds"></a>Lösningar

#### <a name="mitigation-for-the-missing-tenant-id"></a>Lösning för klient-ID saknas

Föreslagen lösning är att använda den [cachelagring av princip](#acquire-a-token-to-apply-a-policy)

Du kan också använda den `tid` anspråk, om du använder den [B2C anpassade principer](https://aka.ms/ief), eftersom den innehåller funktioner för att returnera ytterligare anspråk till programmet. Mer information om [anspråkstransformering](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Hur ”saknas från svaret som token”
Ett alternativ är att använda ”name”-anspråk som önskade användarnamnet. Processen beskrivs i det här [B2C doc](/azure/active-directory-b2c/active-directory-b2c-reference-policies#frequently-asked-questions) -> ”i kolumnen returnerar anspråk Välj anspråk som du vill ska returneras i de auktoriseringstoken som skickas tillbaka till programmet efter en slutförd redigering av profil. Till exempel välja visningsnamn, postnummer ”.

## <a name="next-steps"></a>Nästa steg 

Mer information om att hämta token interaktivt med MSAL.NET för Azure AD B2C-program finns i följande exempel.

| Exempel | Plattform | Beskrivning|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Android-Xamarin, UWP | En enkel Xamarin Forms-app som visar hur du använder MSAL.NET att autentisera användare via Azure AD B2C och få åtkomst till ett webb-API med resulterande token.|
