---
title: Migrera användare med sociala identiteter i Azure Active Directory B2C | Microsoft Docs
description: Diskutera grundläggande begrepp om migrering av användare med sociala identiteter till Azure AD B2C med Graph API.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 12b464d9b6bd09acb9c93ab1de0ba178f28a778a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316111"
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Azure Active Directory B2C: Migrera användare med sociala identiteter
När du planerar att migrera din identitetsprovider till Azure AD B2C kan du också behöva Migrera användare med sociala identiteter. Den här artikeln beskrivs hur du migrerar befintliga konton i sociala identiteter, till exempel: Facebook, LinkedIn, Microsoft och Google-konton till Azure AD B2C. Den här artikeln gäller även för federerade identiteter, men dessa migreringar är mindre vanliga.

## <a name="prerequisites"></a>Nödvändiga komponenter
Den här artikeln är en förlängning av användaren migrering artikeln och fokuserar på sociala migrering. Innan du börjar läser [användarmigrering](active-directory-b2c-user-migration.md).

## <a name="social-identities-migration-introduction"></a>Introduktion för migrering av sociala identiteter

* I Azure AD B2C **lokala konton** inloggningsnamn (användarnamn eller e-postadress) lagras i den `signInNames` samling i användarposten. Den `signInNames` innehåller en eller flera `signInName` poster som anger inloggningsnamn för användaren. Varje inloggningsnamn måste vara unikt inom klienten.

* **Sociala konton** identiteter lagras i `userIdentities` samling. Posten anger den `issuer` (namn på identitetsprovider), till exempel facebook.com och `issuerUserId`, som är unikt användar-ID för utgivaren. Den `userIdentities` attributet innehåller en eller flera UserIdentity-poster som anger typen sociala och unika användaridentifieraren från sociala identitetsprovidern.

* **Kombinera lokalt konto med sociala**. Som tidigare nämnts kan lagras lokalt konto inloggningsnamn och sociala kontoidentiteter i olika attribut. `signInNames` är används för lokalt konto, medan `userIdentities` för socialt konto. Ett enda Azure AD B2C-konto kan vara ett lokalt konto, socialt konto eller kombinera ett lokalt konto med sociala i en användarpost. Det här beteendet kan du hantera ett enda konto när en användare kan logga in med lokalt konto credential(s) eller med sociala identiteter.

* `UserIdentity` Typ - innehåller information om identiteten för en användare för socialt konto i en Azure AD B2C-klient:
  * `issuer` Den sträng som innehåller den identitetsprovider som utfärdade användaridentifierare, till exempel facebook.com.
  * `issuerUserId` Den unika användare-identifieraren som används av den sociala identitetsprovidern i base64-format.

    ```JSON
    "userIdentities": [{
          "issuer": "Facebook.com",
          "issuerUserId": "MTIzNDU2Nzg5MA=="
      }
    ]
    ```

* Beroende på identitetsprovidern, och den **sociala användar-ID** är ett unikt värde för en viss användare per program-eller utveckling. Konfigurera Azure AD B2C-princip med samma program-ID som tilldelades tidigare av sociala providern. Eller ett annat program inom samma konto för utveckling.

## <a name="use-graph-api-to-migrate-users"></a>Använda Graph API för att migrera användare
Du skapar Azure AD B2C-användarkonto via [Graph API](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet). För att kommunicera med Graph API kan ha du först ett tjänstkonto med administrativa privilegier. I Azure AD kan registrera du ett program och autentisering till Azure AD. Program-autentiseringsuppgifterna är program-ID och hemlighet för programmet. Programmet fungerar som själva inte som en användare att anropa Graph API. Följ instruktionerna i steg 1 i [användarmigrering](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-user-migration) artikeln.

## <a name="required-properties"></a>Nödvändiga egenskaper
I följande lista visas de egenskaper som krävs när du skapar en användare.
* **accountEnabled** - true
* **displayName** -namnet ska visas i adressboken för användaren.
* **passwordProfile** -lösenordsprofil för användaren. 

> [!NOTE]
> För socialt konto endast (utan autentiseringsuppgifterna för lokalt konto) Ange du fortfarande lösenordet. Azure AD B2C ignorerar det lösenord som du anger för konton i sociala medier.

* **userPrincipalName** -användarens huvudnamn (someuser@contoso.com). Användarens huvudnamn måste innehålla en av de verifierade domänerna för klienten. Om du vill ange UPN, generera nya GUID-värde, sammanfoga med `@` och namnet på din klientorganisation.
* **mailNickname** -e-postalias för användaren. Det här värdet kan vara samma ID som du använder för userPrincipalName. 
* **signInNames** -en eller flera SignInName-poster som anger inloggningsnamn för användaren. Varje inloggningsnamnet måste vara unikt inom företaget/klient. För socialt konto, kan den här egenskapen vara tom.
* **userIdentities** -en eller flera UserIdentity-poster som anger sociala kontotyp och unika användaridentifieraren från sociala identitetsprovidern.
* [valfritt] **otherMails** – för socialt konto endast användarens e-postadresser 

Mer information finns i: [Graph API-referens](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser)

## <a name="migrate-social-account-only"></a>Migrera sociala konto (endast)
Skapa sociala kontot, utan att autentiseringsuppgifterna för lokalt konto. Skicka HTTPS-POST-begäran till Graph API. Begärandetexten innehåller egenskaperna för det sociala kontot till att skapa. Du måste ange de nödvändiga egenskaperna för ett minimum. 


**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Skicka följande formulär-data: 

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
Att skapa ett lokalt konto för kombinerad med sociala identiteter. Skicka HTTPS-POST-begäran till Graph API. Begärandetexten innehåller egenskaperna för det sociala kontot till att skapa. Du måste ange de nödvändiga egenskaperna för ett minimum. 

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
### <a name="how-can-i-know-the-issuer-name"></a>Hur vet jag Utfärdarens namn?
Utfärdarens namn eller providernamn identitet har konfigurerats i principen. Om du inte vet värdet som ska anges i `issuer`, följ den här proceduren:
1. Logga in med ett socialt konto
2. Kopiera från JWT-token i `sub` värde. Den `sub` innehåller normalt användarens objekt-ID i Azure AD B2C. Eller från Azure-portalen, öppna egenskaperna för användarens och kopiera objekt-ID.
3. Öppna [Azure AD Graph-testaren](https://graphexplorer.azurewebsites.net)
4. Logga in med din administratör.
5. Kör följande GET-begäran. Ersätt userObjectId med det användar-ID som du kopierade. **HÄMTA** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Leta upp den `userIdentities` element i JSON-returnera från Azure AD B2C.
7. [Valfritt] Du kanske också vill avkoda den `issuerUserId` värde.

> [!NOTE]
> Använd ett administratörskonto för B2C-klient som är lokala för B2C-klient. Konto Namnsyntaxen är admin@tenant-name.onmicrosoft.com.

### <a name="is-it-possible-to-add-social-identity-to-an-existing-local-account"></a>Är det möjligt att lägga till sociala i ett befintligt lokalt konto?
Ja. Du kan lägga till sociala identitet när det lokala kontot har skapats. Kör HTTPS PATCH-begäran. Ersätt userObjectId med det användar-ID som du vill uppdatera. 

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

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
Ja. Du kan lägga till flera sociala identiteter för ett enda Azure AD B2C-konto. Kör HTTPS PATCH-begäran. Ersätt userObjectId med användar-ID. 

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

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

## <a name="optional-user-migration-application-sample"></a>[Valfritt] Exempel på användaren migrering för program
[Ladda ned och kör exempelappen V2](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). V2-exempelappen använder en JSON-fil som innehåller data för dummy-användare, inklusive: lokalt konto, sociala konto och lokal och sociala identiteter i samma konto.  Om du vill redigera JSON-filen, öppna den `AADB2C.UserMigration.sln` Visual Studio-lösning. I den `AADB2C.UserMigration` projektet öppnar den `UsersData.json` filen. Filen innehåller en lista över användarentiteter. Varje entitet har följande egenskaper:
* **signInName** – för lokalt konto, e-postadress för att logga in
* **displayName** -användarens visningsnamn
* **Förnamn** -användarens förnamn
* **Efternamn** -användarens efternamn
* **lösenord** för lokalt konto, användarens lösenord (kan vara tom)
* **utfärdaren** – för socialt konto, namnet på identitetsprovider
* **issuerUserId** – för socialt konto, unika användaridentifieraren används av den sociala identitetsprovidern. Värdet ska vara i klartext. Exempelappen kodar det här värdet till base64.
* **e-post** för socialt konto endast (inte kombinerade), användarens e-postadress

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
> Om du inte uppdaterar filen UsersData.json i exemplet med dina data, du kan logga in med autentiseringsuppgifterna för exemplet lokalt konto men inte med exemplen sociala kontot. Om du vill migrera dina konton i sociala medier, ger verkliga data.

Mer information hur du använder exempelappen finns i [Azure Active Directory B2C: Användarmigrering](active-directory-b2c-user-migration.md)
