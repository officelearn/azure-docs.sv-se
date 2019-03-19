---
title: Hur du delegerar användarregistrering och produktprenumeration användarprenumeration
description: Lär dig mer om att delegera användarregistrering och produktprenumeration användarprenumeration till en tredje part i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: c15dc83929aeaf6811f4d19bfca462abfacf4014
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57892463"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Hur du delegerar användarregistrering och produktprenumeration användarprenumeration
Delegering kan du använda din befintliga webbplats för hantering av utvecklare logga-i/registrering och din prenumeration på produkter till skillnad från med hjälp av inbyggda funktioner i developer-portalen. På så sätt kan din webbplats äger användardata och utföra verifiering av de här stegen i ett anpassat sätt.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegate-signin-up"> </a>Delegera developer inloggning och registrering
Om du vill delegera developer inloggning och registrering till din befintliga webbplats måste behöver du skapa en särskild delegeringsslutpunkt på webbplatsen som fungerar som startpunkt för en sådan begäran som initieras från utvecklarportalen API Management.

Sista arbetsflödet blir på följande sätt:

1. Developer klickar på länken logga in eller registrera dig på developer-portalen för API Management
2. Webbläsaren omdirigeras till slutpunkten för delegering
3. Delegeringsslutpunkt omdirigerar till i utbyte eller presenterar användargränssnitt där användaren uppmanas att logga in i eller registrera dig
4. Om åtgärden lyckades kan omdirigeras användaren tillbaka till API Management developer portalsidan de började från

Om du vill börja, begär vi första konfiguration API Management att dirigera via din delegeringsslutpunkt. I utgivarportalen för API Management, klickar du på **Security** och klicka sedan på den **delegering** fliken. Klicka på kryssrutan för att aktivera delegera inloggning och registrering.

![Sidan för delegering][api-management-delegation-signin-up]

* Bestäm vad URL: en för din särskild delegeringsslutpunkt ska och ange den i den **URL för Delegeringsslutpunkt** fält. 
* Ange en hemlighet som ska användas för att beräkna en signatur som du för att bekräfta att säkerställa att begäran verkligen kommer från Azure API Management inom delegering viktiga fältet verifiering. Du kan klicka på den **generera** så API Management slumpmässigt Generera en nyckel åt dig.

Nu måste du skapa den **delegeringsslutpunkt**. Det måste genomföra ett antal åtgärder:

1. Ta emot en begäran i följande format:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn & returnUrl = {URL: en för källsidan} & salt = {sträng} & sig = {sträng}*
   > 
   > 
   
    Frågeparametrar för att logga in eller logga in fall:
   
   * **åtgärden**: identifierar vilken typ av begäran om delegering är det - vara **inloggning från** i det här fallet
   * **returnUrl**: Webbadressen till sidan där användaren har klickat på på en logga in eller registrera dig
   * **salt**: en särskild salt Utdatasträngen som används för databehandling en hash för säkerhet
   * **sig**: ett beräknade security hash-värde som ska användas för jämförelse med din egen beräknad hash
2. Kontrollera att begäran kommer från Azure API Management (valfritt, men rekommenderas för säkerhet)
   
   * Beräkna en HMAC SHA512 hash för en sträng som baseras på den **returnUrl** och **salt** frågeparametrar ([exempelkoden nedan]):
     
     > HMAC (**salt** + ”\n” + **returnUrl**)
     > 
     > 
   * Jämför ovan beräknad hash till värdet för den **sig** frågeparameter. Om två hash-värdena matchar gå vidare till nästa steg, annars neka begäran.
3. Kontrollera att du tar emot en begäran om inloggning-i/logga-up: den **åtgärden** Frågeparametern anges till ”**inloggning från**”.
4. Presentera Användargränssnittet för att logga in eller registrera dig
5. Du måste skapa ett motsvarande konto för dem i API Management om användaren registrera dig. [Skapa en användare] med API Management REST API. Se till att du ställer in användar-ID till samma som den är i din användararkivet eller till ett ID som du kan hålla reda på när du gör.
6. När användaren har autentiserats:
   
   * [begära en single-sign-on (SSO) token] via API Management REST API
   * Lägg till en frågeparameter för returnUrl till URL: en för enkel inloggning som du har fått från API-anrop ovan:
     
     > Exempel: https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * omdirigera användaren till ovanstående producerade URL: en

Förutom den **inloggning från** igen, du kan också utföra kontohantering genom att följa de här stegen och med någon av följande åtgärder:

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**

Du måste skicka följande frågeparametrar för kontohanteringsåtgärder.

* **åtgärden**: identifierar vilken typ av begäran om delegering är det (ChangePassword, ChangeProfile eller CloseAccount)
* **användar-ID**: användar-id för kontot som ska hantera
* **salt**: en särskild salt Utdatasträngen som används för databehandling en hash för säkerhet
* **sig**: ett beräknade security hash-värde som ska användas för jämförelse med din egen beräknad hash

## <a name="delegate-product-subscription"> </a>Delegera produktprenumeration
Delegera produktprenumeration fungerar på samma sätt att delegera användare logga in/in. Sista arbetsflödet skulle vara följande:

1. Developer väljer en produkt i API Management developer-portalen och klickar på knappen prenumerera
2. Webbläsaren omdirigeras till slutpunkten för delegering
3. Delegeringsslutpunkt utför nödvändiga produkten prenumeration steg – det är upp till dig och kan medföra att omdirigera till en annan sida att begära faktureringsinformation, ställa fler frågor, eller att lagra informationen och inte kräver några åtgärder från användaren

Att aktivera funktionerna, på den **delegering** klickar du på **delegera produktprenumeration**.

Kontrollera delegeringsslutpunkt utför följande åtgärder:

1. Ta emot en begäran i följande format:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation= {operation} & productId = {product prenumerera} & användar-ID = {användare gör begäran} & salt = {sträng} & sig = {sträng}*
   > 
   > 
   
    Frågeparametrar för produkten prenumeration:
   
   * **åtgärden**: identifierar vilken typ av delegeringsbegäran. För produktprenumeration är förfrågningar de giltiga alternativen:
     * ”Prenumerera”: en begäran om att lägga till användaren till en viss produkt med angivna ID: T (se nedan)
     * ”Avbryt prenumeration”: en begäran om att avbryta prenumerationen en användare från en produkt
     * ”Förnya”: en begäran om att förnya en prenumeration (till exempel, som kan vara upphör att gälla)
   * **productId**: ID för användaren som begärde för att prenumerera på produkten
   * **användar-ID**: ID för användaren som begäran gjordes
   * **salt**: en särskild salt Utdatasträngen som används för databehandling en hash för säkerhet
   * **sig**: ett beräknade security hash-värde som ska användas för jämförelse med din egen beräknad hash
2. Kontrollera att begäran kommer från Azure API Management (valfritt, men rekommenderas för säkerhet)
   
   * Beräkna en HMAC SHA512 av en sträng som baseras på den **productId**, **userId**, och **salt** frågeparametrar:
     
     > HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * Jämför ovan beräknad hash till värdet för den **sig** frågeparameter. Om två hash-värdena matchar gå vidare till nästa steg, annars neka begäran.
3. Utföra produkten prenumeration bearbetningen baserat på vilken typ av åtgärd som efterfrågas i **åtgärden** – till exempel, fakturering, ytterligare frågor.
4. På har prenumererar användaren att produkten på din sida ska lägga till användaren till API Management-produkten av [anropa REST API för produktprenumeration].

## <a name="delegate-example-code"> </a> Exempelkod
Följande kodexempel visar hur du tar den *valideringsnyckel för delegering*, som har angetts på skärmen delegering på publisher-portalen för att skapa en HMAC, som sedan används för att verifiera signaturen, bevisa sin skicklighet giltigheten för det skickade returnUrl. Samma kod fungerar productId och användar-ID med små ändringar.

**C#kod för att generera hash för returnUrl**

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

**NodeJS-kod för att generera hash för returnUrl**

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

## <a name="next-steps"></a>Nästa steg
Mer information om delegering finns i följande video:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[begära en single-sign-on (SSO) token]: https://docs.microsoft.com/rest/api/apimanagement/User/GenerateSsoUrl
[Skapa en användare]: https://docs.microsoft.com/rest/api/apimanagement/user/createorupdate
[anropa REST API för produktprenumeration]: https://docs.microsoft.com/rest/api/apimanagement/productsubscriptions
[Next steps]: #next-steps
[exempelkoden nedan]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
