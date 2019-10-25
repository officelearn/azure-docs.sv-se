---
title: Azure AD B2C (Microsoft Authentication Library för .NET)
titleSuffix: Microsoft identity platform
description: Lär dig mer om att tänka på när du använder Azure AD B2C med Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a30f792a74ffc3aa983d84d902fa736a3f9b015
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802947"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Använda MSAL.NET för att logga in användare med sociala identiteter

Du kan använda MSAL.NET för att logga in användare med sociala identiteter genom att använda [Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c). Azure AD B2C är byggd runt begreppet principer. I MSAL.NET anger du en princip som översätter för att tillhandahålla en utfärdare.

- När du instansierar det offentliga klient programmet måste du ange principen i auktoritet.
- När du vill tillämpa en princip måste du anropa en åsidosättning av `AcquireTokenInteractive` som innehåller en `authority`-parameter.

Den här sidan är för MSAL 3. x. Om du är intresse rad av MSAL 2. x kan du se [Azure AD B2C information i MSAL 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Auktoritet för en Azure AD B2C klient och princip

Den myndighet som ska använda är `https://login.microsoftonline.com/tfp/{tenant}/{policyName}` där:

- `tenant` är namnet på Azure AD B2C klienten, 
- `policyName` namnet på den princip som ska användas (till exempel "b2c_1_susi" för inloggning/registrering).

Den aktuella vägledningen från Azure AD B2C är att använda `b2clogin.com` som myndighet. Till exempel `$"https://{your-tenant-name}.b2clogin.com/tfp/{your-tenant-ID}/{policyname}"`. Mer information finns i den här [dokumentationen](/azure/active-directory-b2c/b2clogin).

## <a name="instantiating-the-application"></a>Instansiera programmet

När du skapar programmet måste du ange utfärdaren.

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

Att hämta en token för ett Azure AD B2C skyddat API i ett offentligt klient program kräver att du använder åsidosättningarna med en utfärdare:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireToken(scopes, parentWindow)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .ExecuteAsync();
```

med:

- `policy` är en av de tidigare strängarna (till exempel `PolicySignUpSignIn`).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)` är en metod som söker efter ett konto för en specifik princip. Exempel:

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

Tillämpa en princip (du kan till exempel låta användaren redigera profilen eller återställa deras lösen ord) genom att anropa `AcquireTokenInteractive`. Om du använder dessa två principer använder du inte resultatet för returnerat token/autentisering.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Specialfall av EditProfile-och ResetPassword-principer

När du vill ge en upplevelse där slutanvändarna loggar in med en social identitet och sedan redigera profilen som du vill använda för att använda principen för Azure AD B2C EditProfile. Du kan göra detta genom att anropa `AcquireTokenInteractive` med den angivna behörigheten för principen och en prompt anges till `Prompt.NoPrompt` för att undvika att dialog rutan Val av konto visas (eftersom användaren redan är inloggad)

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
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Autentiseringsuppgifter för resurs ägar lösen ord (ROPC) med Azure AD B2C
Mer information om ROPC-flödet finns i den här [dokumentationen](v2-oauth-ropc.md).

Det här flödet **rekommenderas inte** eftersom ditt program som ber användaren om lösen ordet inte är säkert. Mer information om det här problemet finns i [den här artikeln](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Genom att använda användar namn/lösen ord ger du ett antal saker:
- kärn klienter av modern identitet: lösen ordet blir bevarat och spelas upp. Eftersom vi har det här begreppet en resurs hemlighet som kan fångas upp. Detta är inte kompatibelt med lösen ord.
- Användare som behöver göra MFA kan inte logga in (eftersom det inte finns någon interaktion).
- Användare kan inte utföra enkel inloggning.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Konfigurera ROPC-flödet i Azure AD B2C
Skapa ett nytt användar flöde i Azure AD B2C klient och välj Logga in **med ROPC**. Då aktive ras ROPC-principen för din klient. Mer information finns i [Konfigurera flödet för autentiseringsuppgifter för resurs ägar lösen ord](/azure/active-directory-b2c/configure-ropc) .

`IPublicClientApplication` innehåller en metod:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Den här metoden tar som parametrar:
- *Omfattningarna* som begär en åtkomsttoken för.
- Ett *användar namn*.
- Ett SecureString- *lösenord* för användaren.

Kom ihåg att använda den myndighet som innehåller ROPC-principen.

### <a name="limitations-of-the-ropc-flow"></a>Begränsningar för ROPC-flödet
 - ROPC-flödet **fungerar bara för lokala konton** (där du registrerar dig med Azure AD B2C med hjälp av ett e-postmeddelande eller användar namn). Det här flödet fungerar inte om federering till någon av de identitets leverantörer som stöds av Azure AD B2C (Facebook, Google osv.).
 - För närvarande finns det **ingen id_token som returneras från Azure AD B2C** när du IMPLEMENTERAr ROPC-flödet från MSAL. Det innebär att det inte går att skapa ett konto objekt, så i cachen kommer det inte att finnas något konto och ingen användare. AcquireTokenSilent-flödet fungerar inte i det här scenariot. ROPC visar dock inget användar gränssnitt, så det påverkar inte användar upplevelsen.

## <a name="google-auth-and-embedded-webview"></a>Google auth och Embedded webbvy

Om du är en Azure AD B2C utvecklare som använder Google som en identitetsprovider, kan du använda system webbläsaren som Google tillåter inte [autentisering från inbäddade webviews](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). `login.microsoftonline.com` är för närvarande en betrodd utfärdare med Google. Att använda den här utfärdaren fungerar med inbäddad webbvy. Att använda `b2clogin.com` är dock inte en betrodd utfärdare med Google, så användarna kan inte autentisera sig.

Vi kommer att tillhandahålla en uppdatering av wikin och det här [problemet](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) om saker ändras.

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Cachelagring med Azure AD B2C i MSAL.Net 

### <a name="known-issue-with-azure-ad-b2c"></a>Känt problem med Azure AD B2C

MSAL.Net stöder [token cache](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). Nyckeln för cachelagring av token baseras på de anspråk som returneras av identitets leverantören. För närvarande behöver MSAL.Net två anspråk för att bygga en token cache-nyckel:  
- `tid` som är Azure AD-klient-ID: t och 
- `preferred_username` 

Båda dessa anspråk saknas i många av de Azure AD B2C scenarierna. 

Kund effekten är att när du försöker visa fältet username får du "saknas från" token Response "som värdet? I så fall beror det på att Azure AD B2C inte returnerar ett värde i IdToken för preferred_username på grund av begränsningar med sociala konton och externa identitets leverantörer (IDP: er). Azure AD returnerar ett värde för preferred_username eftersom det vet vem användaren är, men för Azure AD B2C eftersom användaren kan logga in med ett lokalt konto, Facebook, Google, GitHub osv. det finns inget konsekvent värde för Azure AD B2C som används för preferred_username. För att avblockera MSAL från att ta bort cache-kompatibilitet med ADAL, beslutade vi att använda "saknas från token Response" i vårt slut när du hanterar Azure AD B2C-konton när IdToken returnerar inget för preferred_username. MSAL måste returnera ett värde för preferred_username för att bevara cache-kompatibiliteten mellan bibliotek.

### <a name="workarounds"></a>Lösningar

#### <a name="mitigation-for-the-missing-tenant-id"></a>Minskning av klient-ID som saknas

Den föreslagna lösningen är att använda [cachelagring efter princip](#acquire-a-token-to-apply-a-policy)

Du kan också använda `tid`-anspråk om du använder [anpassade principer för B2C](https://aka.ms/ief), eftersom det ger möjlighet att returnera ytterligare anspråk till programmet. Läs mer om [anspråks omvandling](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Minskning av "saknas från" token Response "
Ett alternativ är att använda anspråket "namn" som önskat användar namn. Processen beskrivs i den här [B2C dokument](../../active-directory-b2c/active-directory-b2c-reference-policies.md) -> "i kolumnen retur anspråk väljer du de anspråk som du vill ska returneras i de token som skickas tillbaka till programmet efter en lyckad profil redigerings upplevelse. Välj till exempel visnings namn, post nummer. "

## <a name="next-steps"></a>Nästa steg 

Mer information om hur du hämtar token interaktivt med MSAL.NET för Azure AD B2C program finns i följande exempel.

| Exempel | Plattform | Beskrivning|
|------ | -------- | -----------|
|[Active-Directory-B2C-Xamarin-Native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | En enkel Xamarin Forms-app som demonstrerar hur du använder MSAL.NET för att autentisera användare via Azure AD B2C och åtkomst till ett webb-API med de resulterande tokens.|
