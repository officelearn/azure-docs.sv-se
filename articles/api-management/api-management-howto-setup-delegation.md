---
title: Så här delegerar du användarregistrering och produktprenumeration
description: Lär dig hur du delegerar användarregistrering och produktprenumeration till en tredje part i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: a69babdf2fffb4cb9d963f1806f3c85755e50294
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454355"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Så här delegerar du användarregistrering och produktprenumeration

Med delegering kan du använda din befintliga webbplats för att hantera utvecklarens inloggning/registrering och prenumeration på produkter, i stället för att använda de inbyggda funktionerna i utvecklarportalen. Det gör att din webbplats att äga användardata och utföra valideringen av dessa steg på ett anpassat sätt.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegating-developer-sign-in-and-sign-up"></a><a name="delegate-signin-up"> </a>Delegera in- och registrering av utvecklare

Om du vill delegera utvecklare, logga in och registrera dig på din befintliga webbplats måste du skapa en särskild delegeringsslutpunkt på webbplatsen. Den måste fungera som startpunkt för en sådan begäran som initieras från API Management-utvecklarportalen.

Det slutliga arbetsflödet kommer att vara följande:

1. Utvecklaren klickar på inloggnings- eller registreringslänken på utvecklarportalen för API Management
2. Webbläsaren omdirigeras till slutpunkten för delegering
3. Delegeringslutpunkt i returomdirigeringar till eller presenterar användargränssnittet där användaren uppmanas att logga in eller registrera sig
4. När den lyckas omdirigeras användaren tillbaka till api management-utvecklarportalsidan som de startade från

Till att börja med ska vi först konfigurera API Management för att dirigera begäranden via dendelegeringsslutpunkten. Sök efter **säkerhet** i API Management-resursen i Azure-portalen och klicka sedan på objektet **Delegering.** Klicka i kryssrutan om du vill aktivera "Delegera inloggning & registrera dig".

![Sidan Delegering][api-management-delegation-signin-up]

* Bestäm vilken URL för den särskilda delegeringsslutpunkten ska vara och ange den i **url-fältet För delegering.** 
* Ange en hemlighet som ska användas för att beräkna en signatur som du har angett för verifiering för att säkerställa att begäran verkligen kommer från Azure API Management i fältet Delegeringsautentisering. Du kan klicka på **generera** knappen för att få API Management slumpmässigt generera en nyckel för dig.

Nu måste du skapa **delegeringslutpunkten**. Den måste utföra ett antal åtgärder:

1. Ta emot en begäran i följande formulär:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL för källsidan}&salt={string}&sig={string}*
   > 
   > 
   
    Frågeparametrar för inloggnings- och registreringsfodralet:
   
   * **åtgärd**: identifierar vilken typ av delegeringsbegäran det är - det kan bara vara **Inloggning** i det här fallet
   * **returnUrl**: webbadressen till den sida där användaren klickade på en inloggning eller registrera dig länk
   * **salt**: en speciell saltsträng som används för att beräkna en säkerhetshh
   * **sig**: en beräknad säkerhet hash som ska användas för jämförelse med din egen beräknade hash
2. Kontrollera att begäran kommer från Azure API Management (valfritt men rekommenderas starkt för säkerhet)
   
   * Beräkna en HMAC-SHA512 hash av en sträng baserat på **parametrarna returnUrl** och **salt** fråga[(exempelkod som anges nedan):]
     
     > HMAC(**salt** + '\n' + **returnUrl**)
     > 
     > 
   * Jämför den ovan beräknade hash-värdet med värdet för **parametern sig query.** Om de två hasharmatchningen går du vidare till nästa steg och nekar annars begäran.
3. Kontrollera att du får en begäran om inloggning/registrering: åtgärdsfrågeparametern ställs in på "**SignIn**". **operation**
4. Presentera användargränssnittet för att logga in eller registrera sig
5. Om användaren registrerar sig måste du skapa ett motsvarande konto för dem i API Management. [Skapa en användare] med REST-API:et för API-hantering. När du gör det, se till att du ställer in användar-ID till samma värde som i din användarbutik eller till ett ID som du kan hålla reda på.
6. När användaren har autentiserats:
   
   * [begära en SSO-token (Single-sign-on)] via API Management REST API
   * lägga till en returnUrl-frågeparameter till SSO-URL:en som du har fått från API-anropet ovan:
     
     > till exempel,https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * omdirigera användaren till ovanstående producerade WEBBADRESS

Förutom **SignIn-åtgärden** kan du även utföra kontohantering genom att följa föregående steg och använda någon av följande åtgärder:

* **ÄndraPassword**
* **ChangeProfile**
* **CloseAccount**

Du måste skicka följande frågeparametrar för kontohanteringsåtgärder.

* **åtgärd**: identifierar vilken typ av delegeringsbegäran det är (ChangePassword, ChangeProfile eller CloseAccount)
* **userId**: användarens ID för kontot för att hantera
* **salt**: en speciell saltsträng som används för att beräkna en säkerhetshh
* **sig**: en beräknad säkerhet hash som ska användas för jämförelse med din egen beräknade hash

## <a name="delegating-product-subscription"></a><a name="delegate-product-subscription"> </a>Delegera produktprenumeration
Delegera produktprenumeration fungerar på samma sätt att delegera användarloggning i/upp. Det slutliga arbetsflödet skulle vara följande:

1. Utvecklaren väljer en produkt i utvecklarportalen för API Management och klickar på knappen Prenumerera.
2. Webbläsaren omdirigeras till slutpunkten för delegering.
3. Slutpunkten för delegering utför nödvändiga produktprenumerationssteg. Det är upp till dig att utforma stegen. De kan omfatta omdirigering till en annan sida för att begära faktureringsinformation, ställa ytterligare frågor eller helt enkelt lagra informationen och inte kräva någon användaråtgärd.

Om du vill aktivera funktionen klickar du på **Delegera produktprenumeration**på sidan **Delegering.**

Kontrollera sedan att delegeringsslutpunkten gör följande:

1. Ta emot en begäran i följande formulär:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation={operation}&productId={product to subscribe to}&userId={user making request}&salt={string}&sig={string}*
   >
   
    Frågeparametrar för produktprenumerationsärelet:
   
   * **åtgärd**: identifierar vilken typ av delegeringsbegäran den är. För produktprenumerationsförfrågningar är de giltiga alternativen:
     * "Prenumerera": en begäran om att prenumerera användaren på en viss produkt med medföljande ID (se nedan)
     * "Avsluta prenumeration": en begäran om att avsluta prenumerationen på en användare från en produkt
     * "Förnya": en begäran om att förnya en prenumeration (till exempel som kan löpa ut)
   * **productId**: ID för den produkt som användaren begärde att prenumerera på
   * **subscriptionId**: på *Avsluta prenumeration* och *förnya* - ID för produktprenumerationen
   * **userId**: användarens ID som begäran görs för
   * **salt**: en speciell saltsträng som används för att beräkna en säkerhetshh
   * **sig**: en beräknad säkerhet hash som ska användas för jämförelse med din egen beräknade hash

2. Kontrollera att begäran kommer från Azure API Management (valfritt men rekommenderas starkt för säkerhet)
   
   * Beräkna en HMAC-SHA512 för en sträng baserat på parametrarna **productId**, **userId**och **salt** query:
     
     > HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * Jämför den ovan beräknade hash-värdet med värdet för **parametern sig query.** Om de två hasharmatchningen går du vidare till nästa steg och nekar annars begäran.
3. Bearbeta produktprenumeration baserat på vilken typ av åtgärd som begärs i **drift** - till exempel fakturering, ytterligare frågor, etc.
4. När du har prenumererat på användaren på produkten på din sida prenumererar du användaren på API Management-produkten genom att [anropa REST API för prenumerationer].

## <a name="example-code"></a><a name="delegate-example-code"> </a> Exempelkod

Dessa kodexempel visar hur du:

* Ta *delegeringsvalideringsnyckeln*, som anges på skärmen Delegering på utgivarportalen
* Skapa en HMAC, som sedan används för att validera signaturen, som bevisar giltigheten av den skickade returnUrl.

Samma kod fungerar för productId och userId med liten modifiering.

**C# kod för att generera hash av returnUrl**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**NodeJS-kod för att generera hash av returnUrl**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

> [!IMPORTANT]
> Du måste [publicera om utvecklarportalen](api-management-howto-developer-portal-customize.md#publish) för att delegeringsändringarna ska börja gälla.

## <a name="next-steps"></a>Nästa steg
Mer information om delegering finns i följande video:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[begära en SSO-token (Single-sign-on)]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/User/GenerateSsoUrl
[skapa en användare]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user/createorupdate
[anropa REST API för prenumerationer]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription/createorupdate
[Next steps]: #next-steps
[exempelkod som anges nedan]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
