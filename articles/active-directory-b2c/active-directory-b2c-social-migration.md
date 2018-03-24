---
title: 'Azure Active Directory B2C: Migrera användare med sociala identiteter'
description: Diskutera grundbegrepp på migrering av användare med sociala identiteter i Azure AD B2C med Graph API
services: active-directory-b2c
documentationcenter: ''
author: davidmu
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/03/2018
ms.author: davidmu
ms.openlocfilehash: 76ed4dac40872bf6db07b26c5805a4db62dc9dfc
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Azure Active Directory B2C: Migrera användare med sociala identiteter
När du planerar att migrera din identitetsleverantör till Azure AD B2C kan du också behöva Migrera användare med sociala identiteter. Den här artikeln beskrivs hur du migrerar befintliga sociala identiteter konton, exempel: Facebook, LinkedIn, Microsoft och Google-konton till Azure AD B2C. Den här artikeln gäller även för federerade identiteter, men dessa migreringar är mindre vanliga.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln är en förlängning av användaren migrering artikeln och fokuserar på sociala identitet migrering. Innan du börjar läsa [användarmigrering](active-directory-b2c-user-migration.md).

## <a name="social-identities-migration-introduction"></a>Introduktion av sociala identiteter migrering

* I Azure AD B2C **lokala konton** inloggningsnamn (användarnamn eller e-postadress) lagras i den `signInNames` samling i användarposten. Den `signInNames` innehåller en eller flera `signInName` poster som anger inloggningsnamn för användaren. Varje inloggningsnamn måste vara unikt inom innehavaren.

* **Sociala konton** identiteter lagras i `userIdentities` samling. Posten anger den `issuer` (identity providernamn), till exempel facebook.com och `issuerUserId`, vilket är ett unikt ID för utfärdaren. Den `userIdentities` attributet innehåller en eller flera poster för användaridentiteten som anger typen av sociala och unikt användar-ID från sociala identitetsleverantören.

* **Kombinera lokalt konto med sociala identitet**. Som tidigare nämnts kan lagras lokalt konto inloggningsnamn och sociala konto identiteter i olika attribut. `signInNames` är används för lokala kontot, medan `userIdentities` för sociala konto. Ett enskilt konto i Azure AD B2C kan vara ett lokalt konto, sociala konto, eller kombinera ett lokalt konto med sociala identitet i en användarpost. Det här problemet kan du hantera ett enda konto när en användare kan logga in med det lokala kontot credential(s) eller med sociala identiteter.

* `UserIdentity` Typ - innehåller information om identiteten för ett sociala användarkonto i en Azure AD B2C-klient:
    * `issuer` Strängrepresentation av identitetsleverantören som utfärdade användar-ID, till exempel facebook.com.
    * `issuerUserId` Den unika användaridentifierare som används av sociala identitetsleverantören i base64-format.

    ```JSON
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
    ```

* Beroende på identitetsleverantören den **sociala användar-ID** är ett unikt värde för en viss användares `per application` eller utveckling konto. Konfigurera Azure AD B2C-princip med samma program-ID som tidigare har tilldelats av sociala providern. Ett annat program eller `within the same development account`.

## <a name="use-graph-api-to-migrate-users"></a>Använd Graph API för att migrera användare
Du skapar Azure AD B2C användarkonto via [Graph API](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet). För att kommunicera med Graph API måste måste du först ha ett tjänstkonto med administratörsbehörighet. I Azure AD kan registrera du ett program och autentisering till Azure AD. Autentiseringsuppgifterna som programmet är program-ID och Apphemligheten. Programmet fungerar som själva inte som en användare att anropa Graph API. Följ anvisningarna i steg 1 i [användarmigrering](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-user-migration#step-1-use-graph-api-to-migrate-users) artikel.

## <a name="required-properties"></a>Nödvändiga egenskaper
I följande lista visas de egenskaper som krävs när du skapar en användare.
* **accountEnabled** – SANT
* **displayName** -namnet som visas i adressboken för användaren.
* **passwordProfile** -profilen lösenord för användaren. 

> [!NOTE]
> För sociala konto endast (utan autentiseringsuppgifter för lokala konton) Ange du fortfarande lösenordet. Azure AD B2C ignorerar lösenordet som du anger för sociala konton.

* **userPrincipalName** -användarens huvudnamn (someuser@contoso.com). Användarens huvudnamn måste innehålla en av de verifierade domänerna för klienten. Om du vill ange UPN generera nya GUID-värde, sammanfoga med `@` och innehavarens namn.
* **mailNickname** -e postalias för användaren. Det här värdet kan vara samma ID som du använder för userPrincipalName. 
* **signInNames** -en eller flera SignInName-poster som anger inloggningsnamn för användaren. Varje inloggningsnamn måste vara unikt inom företaget/innehavaren. Sociala tjänstkontot kan kan den här egenskapen lämnas tomt.
* **userIdentities** -en eller flera poster för användaridentiteten som anger sociala kontotyp och unikt användar-ID från sociala identitetsleverantören.
* [valfritt] **otherMails** - för sociala konto endast användarens e-postadresser 

Mer information finns: [Graph API-referens](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser)

## <a name="migrate-social-account-only"></a>Migrera sociala konto (endast)
Att skapa, sociala konto utan lokala autentiseringsuppgifter. Skicka HTTPS POST-begäran till Graph API. Begärandetexten innehåller egenskaper för det sociala kontot till att skapa. Du måste ange de obligatoriska egenskaperna minimum. 


**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Skicka följande formulärdata: 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8",
    "signInNames": [],
    "creationType": null,
    "displayName": "Sara Bell",
    "givenName": "Sara",
    "surname": "Bell",
    "passwordProfile": {
        "password": "Test1234",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": null,
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ],
    "otherMails": ["sara@live.com"],
    "userPrincipalName": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8@tenant-name.onmicrosoft.com"
}
```
## <a name="migrate-social-account-with-local-account"></a>Migrera sociala konto med lokalt konto
Att skapa en kombinerad lokalt konto med sociala identiteter. Skicka HTTPS POST-begäran till Graph API. Begärandetexten innehåller egenskaper för det sociala kontot till att skapa. Du måste ange de obligatoriska egenskaperna minimum. 

**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Skicka följande formulär-data: 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "5164db16-3eee-4629-bfda-dcc3326790e9",
    "signInNames": [{
            "type": "emailAddress",
            "value": "david@contoso.com"
        }
    ],
    "creationType": "LocalAccount",
    "displayName": "David Hor",
    "givenName": "David",
    "surname": "Hor",
    "passwordProfile": {
        "password": "1234567",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": "DisablePasswordExpiration,DisableStrongPassword",
    "userIdentities": [{
            "issuer": "contoso.com",
            "issuerUserId": "ZGF2aWRAY29udG9zby5jb20="
        }
    ],
    "otherMails": [],
    "userPrincipalName": "5164db16-3eee-4629-bfda-dcc3326790e9@tenant-name.onmicrosoft.com"
}
```

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
### <a name="how-can-i-know-the-issuer-name"></a>Hur vet Utfärdarens namn?
Utfärdarens namn eller providernamn identitet har konfigurerats i principen. Om du inte vet det värde som anger i `issuer`, följer du proceduren:
1. Logga in med något av sociala konton
2. Kopiera från JWT-token i `sub` värde. Den `sub` innehåller vanligtvis användarens objekt-ID i Azure AD B2C. Eller från Azure-portalen öppnar du egenskaperna för användarens och kopiera objekt-ID.
3. Öppna [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net)
4. Logga in med din administratör. N
5. Kör följande GET-begäran. Ersätt userObjectId med det användar-ID som du kopierade. **HÄMTA** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Leta upp den `userIdentities` -element inuti JSON tillbaka från Azure AD B2C.
7. [Valfritt] Du kanske också vill avkoda den `issuerUserId` värde.

> [!NOTE]
> Använd ett administratörskonto för B2C-klient som är lokala för B2C-klient. Namnsyntaxen konto är admin@tenant-name.onmicrosoft.com.

### <a name="is-it-possible-to-add-social-identity-to-an-existing-local-account"></a>Är det möjligt att lägga till sociala identitet till ett befintligt lokalt konto?
Ja. Du kan lägga till sociala identitet när det lokala kontot har skapats. Kör HTTPS PATCH-begäran. Ersätt userObjectId med det användar-ID som du vill uppdatera. 

**KORRIGERING** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Skicka följande formulär-data: 

```JSON
{
    "userIdentities": [
        {
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

### <a name="is-it-possible-to-add-multiple-social-identities"></a>Är det möjligt att lägga till flera sociala identiteter?
Ja. Du kan lägga till flera sociala identiteter för ett enskilt konto i Azure AD B2C. Kör HTTPS PATCH-begäran. Ersätt userObjectId med användar-ID. 

**KORRIGERING** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Skicka följande formulär-data: 

```JSON
{
    "userIdentities": [
        {
            "issuer": "google.com",
            "issuerUserId": "MjQzMjE2NTc4NTQ="
        },
        {
            "issuer": "facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

## <a name="optional-user-migration-application-sample"></a>[Valfritt] Användaren migrering programmet exempel
[Hämta och kör exempelappen V2](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). Exempelappen V2 använder en JSON-fil som innehåller dummy användardata, inklusive: lokalt konto, sociala konto och lokala & sociala identiteter i samma konto.  Redigera JSON-filen genom att öppna den `AADB2C.UserMigration.sln` Visual Studio-lösning. I den `AADB2C.UserMigration` projektet öppnar den `UsersData.json` filen. Filen innehåller en lista över enheter som användaren. Varje enhet som användaren har följande egenskaper:
* **signInName** - för lokala konton, e-postadress för att logga in
* **displayName** -användarens namn
* **Förnamn** -användarens förnamn
* **Efternamn** -användarens efternamn
* **lösenordet** för lokalt konto, användarens lösenord (kan vara tom)
* **utfärdaren** – för sociala konto, providernamn identitet
* **issuerUserId** - för sociala konto, den unika användaridentifierare som används av sociala identitetsleverantören. Värdet måste vara i klartext. Exempelappen kodar det här värdet till base64.
* **e-post** för sociala konto endast (inte kombinerade), användarens e-postadress

```JSON
{
  "userType": "emailAddress",
  "Users": [
    {
      // Local account only
      "signInName": "James@contoso.com",
      "displayName": "James Martin",
      "firstName": "James",
      "lastName": "Martin",
      "password": "Pass!w0rd"
    },
    {
      // Social account only
      "issuer": "Facebook.com",
      "issuerUserId": "1234567890",
      "email": "sara@contoso.com",
      "displayName": "Sara Bell",
      "firstName": "Sara",
      "lastName": "Bell"
    },
    {
      // Combine local account with social identity
      "signInName": "david@contoso.com",
      "issuer": "Facebook.com",
      "issuerUserId": "0987654321",
      "displayName": "David Hor",
      "firstName": "David",
      "lastName": "Hor",
      "password": "Pass!w0rd"
    }
  ]
}
```

> [!NOTE]
> Om du inte uppdaterar filen UsersData.json i exemplet med dina data, du kan logga in med de lokala kontoautentiseringsuppgifterna exempel men inte med sociala konto exemplen. Ange verkliga data för att migrera dina sociala konton.

Mer information hur du använder exempelappen finns [Azure Active Directory B2C: migrering av användare](active-directory-b2c-user-migration.md)
