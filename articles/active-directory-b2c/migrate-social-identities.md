---
title: Migrera användare med sociala identiteter
titleSuffix: Azure AD B2C
description: Granskning av grundläggande begrepp om migrering av användare med sociala identiteter i Azure AD B2C att använda Graph API.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2018
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 96bb4ef11b960a517054009f14f18b7af23fac14
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848842"
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Azure Active Directory B2C: migrera användare med sociala identiteter
När du planerar att migrera identitets leverantören till Azure AD B2C kan du också behöva migrera användare med sociala identiteter. Den här artikeln förklarar hur du migrerar befintliga sociala identiteter-konton, till exempel: Facebook-, LinkedIn-, Microsoft-och Google-konton till Azure AD B2C. Den här artikeln gäller även federerade identiteter, men migreringarna är mindre vanliga. I resten av den här artikeln bör du fundera på vad som gäller för sociala konton som även gäller för andra typer av federerade konton.

## <a name="prerequisites"></a>Krav
Den här artikeln är en fortsättning på artikeln om användarmigrering och fokuserar på migrering av sociala identiteter. Innan du börjar läser du [migrering av användare](user-migration.md).

## <a name="social-identities-migration-introduction"></a>Introduktion till migrering av sociala identiteter

* I Azure AD B2C lagras inloggnings namn för **lokala konton** (användar namn eller e-postadress) i `signInNames`-samlingen i användar posten. `signInNames` innehåller en eller flera `signInName` poster som anger inloggnings namnen för användaren. Varje inloggnings namn måste vara unikt för klienten.

* Identiteter för **sociala konton** lagras i `userIdentities`s samlingen. Posten anger `issuer` (identitets leverantörens namn), till exempel facebook.com och `issuerUserId`, som är ett unikt användar-ID för utfärdaren. Attributet `userIdentities` innehåller en eller flera UserIdentity-poster som anger typ av socialt konto och den unika användar identifieraren från den sociala identitets leverantören.

* **Kombinera lokalt konto med social identitet**. Som nämnts lagras inloggnings namn för lokalt konto och identiteter för sociala konton i olika attribut. `signInNames` används för lokalt konto, medan `userIdentities` används för sociala konton. Ett enda Azure AD B2C konto kan bara vara ett lokalt konto, endast socialt konto eller kombinera ett lokalt konto med en eller flera sociala identiteter i en användar post. Med det här beteendet kan du hantera ett enda konto, medan en användare kan logga in med de lokala kontots autentiseringsuppgift eller sociala identiteter.

* `UserIdentity`-information: innehåller information om identiteten för ett konto för social användare i en Azure AD B2C klient organisation:
  * `issuer` sträng representationen av identitets leverantören som utfärdade användar identifieraren, till exempel facebook.com.
  * `issuerUserId` den unika användar identifieraren som används av den sociala identitets leverantören i Base64-kodat format.

    ```JSON
    "userIdentities": [{
          "issuer": "Facebook.com",
          "issuerUserId": "MTIzNDU2Nzg5MA=="
      }
    ]
    ```

* Beroende på identitets leverantören är **utfärdarens användar-ID** ett unikt värde för en viss användare per program eller utvecklings konto. Konfigurera Azure AD B2C principen med samma program-ID som tidigare har tilldelats av den sociala leverantören eller något annat program inom samma utvecklings konto.

## <a name="use-graph-api-to-migrate-users"></a>Använd Graph API för att migrera användare
Du skapar Azure AD B2C användar kontot via [Graph API](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet).
För att kunna kommunicera med Graph API måste du först ha ett tjänst konto med administratörs behörighet. I Azure AD registrerar du ett program och autentisering till Azure AD. Programmets autentiseringsuppgifter är program-ID och program hemlighet. Programmet fungerar som själva, inte som en användare, för att anropa Graph API. Följ instruktionerna i steg 1 i artikeln om [användarmigrering](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-user-migration) .

## <a name="required-properties"></a>Nödvändiga egenskaper
I följande lista visas de egenskaper som krävs när du skapar en användare.
* **accountEnabled** – sant
* **DisplayName** – namnet som ska visas i användarens adress bok.
* **passwordProfile** – lösen ords profilen för användaren.

> [!NOTE]
> För sociala konton (utan autentiseringsuppgifter för lokalt konto) måste du fortfarande ange lösen ordet. Azure AD B2C ignorerar det lösen ord som du anger för sociala konton.

* **userPrincipalName** – User Principal Name (someuser@contoso.com). User Principal Name måste innehålla en av de verifierade domänerna för klient organisationen. Om du vill ange UPN skapar du ett nytt GUID-värde, sammanfogar med `@` och klient namnet.
* **smek namn** – e-postaliaset för användaren. Det här värdet kan vara samma ID som du använder för userPrincipalName.
* **signInNames** – en eller flera SignInName-poster som anger användarens inloggnings namn. Varje inloggnings namn måste vara unikt för företaget/klienten. Den här egenskapen kan lämnas tom för endast sociala konton.
* **userIdentities** – en eller flera UserIdentity-poster som anger typ av socialt konto och den unika användar identifieraren från den sociala identitets leverantören.
* valfritt **otherMails** – endast för socialt konto, användarens e-postadresser

Mer information finns i: [Graph API referens](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser)

## <a name="migrate-social-account-only"></a>Migrera socialt konto (endast)
Om du bara vill skapa ett socialt konto, utan autentiseringsuppgifter för lokalt konto, skickar du en HTTPS POST-begäran till Graph API. Begär ande texten innehåller egenskaperna för det sociala konto användare som ska skapas. Du måste minst ange de egenskaper som krävs.


**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Skicka följande formulär – data:

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
## <a name="migrate-social-account-with-local-account"></a>Migrera socialt konto med lokalt konto
Om du vill skapa ett kombinerat lokalt konto med sociala identiteter skickar du en HTTPS POST-begäran till Graph API. Begär ande texten innehåller egenskaperna för det sociala konto användare som ska skapas inklusive inloggnings namnet för det lokala kontot. Du måste minst ange de egenskaper som krävs.

**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Skicka följande formulär – data:

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

## <a name="frequently-asked-questions"></a>Vanliga frågor
### <a name="how-can-i-know-the-issuer-name"></a>Hur kan jag känna till utfärdarens namn?
Utfärdarens namn, eller namnet på identitets leverantören, konfigureras i principen. Om du inte känner till värdet som ska anges i `issuer`följer du den här proceduren:
1. Logga in med ett av de sociala kontona
2. Kopiera `sub`-värdet från JWT-token. `sub` innehåller vanligt vis användarens objekt-ID i Azure AD B2C. Du kan också öppna användarens egenskaper och kopiera objekt-ID: t från Azure Portal.
3. Öppna [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net)
4. Logga in med din administratör.
5. Kör följande GET-begäran. Ersätt userObjectId med det användar-ID som du kopierade. **Hämta** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Leta upp `userIdentities`-elementet i JSON-returen från Azure AD B2C.
7. Valfritt Du kanske också vill avkoda `issuerUserId`-värdet.

> [!NOTE]
> Använd ett B2C klient administratörs konto som är lokalt för B2C-klienten. Konto namnets syntax är admin@tenant-name.onmicrosoft.com.

### <a name="is-it-possible-to-add-a-social-identity-to-an-existing-user"></a>Är det möjligt att lägga till en social identitet till en befintlig användare?
Ja. Du kan lägga till en social identitet efter att Azure AD B2C kontot har skapats (oavsett om det är ett lokalt eller socialt konto eller en kombination av detta). Kör en begäran om HTTPS-korrigering. Ersätt userObjectId med det användar-ID som du vill uppdatera.

**Korrigerings** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Skicka följande formulär – data:

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
Ja. Du kan lägga till flera sociala identiteter för ett enda Azure AD B2C konto. Kör HTTPS PATCH-begäran. Ersätt userObjectId med användar-ID: t.

**Korrigerings** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Skicka följande formulär – data:

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

## <a name="optional-user-migration-application-sample"></a>Valfritt Program exempel för migrering av användare
[Hämta och kör exempel programmet v2](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). Exempel App v2 använder en JSON-fil som innehåller dummy-användar data, inklusive: lokalt konto, socialt konto och lokala & sociala identiteter i ett enda konto.  Om du vill redigera JSON-filen öppnar du `AADB2C.UserMigration.sln` Visual Studio-lösningen. Öppna `UsersData.json`-filen i `AADB2C.UserMigration`-projektet. Filen innehåller en lista över användar enheter. Varje entitet för användare har följande egenskaper:
* **signInName** – för lokalt konto, e-postadress för inloggning
* **DisplayName** – användarens visnings namn
* **FirstName** – användarens förnamn
* **LastName** -användarens efter namn
* **lösen ord** Användarens lösen ord (kan vara tomt) för lokalt konto
* **utfärdare** – för socialt konto, identitets leverantörens namn
* **issuerUserId** – för socialt konto, den unika användar identifieraren som används av den sociala identitets leverantören. Värdet ska vara i klartext. Exempel appen kodar det här värdet till base64.
* **e-post** Användarens e-postadress endast för socialt konto (inte kombinerat)

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
> Om du inte uppdaterar UsersData. JSON-filen i exemplet med dina data kan du logga in med autentiseringsuppgifterna för det lokala kontot, men inte med exempel på sociala konton. Om du vill migrera dina sociala konton anger du verkliga data.

Mer information om hur du använder exempel appen finns [Azure Active Directory B2C: migrering av användare](user-migration.md)
