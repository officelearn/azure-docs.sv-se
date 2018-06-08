---
title: Hur du delegerar användarens registrering och produkt-prenumeration
description: Lär dig att delegera användarens registrering och produkten prenumeration till en tredje part i Azure API Management.
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
ms.openlocfilehash: 02c3a3d996fa253cf56e551a37e098639bf73533
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "32151951"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Hur du delegerar användarens registrering och produkt-prenumeration
Delegering kan du använda din befintliga webbplats för att hantera developer logga-i/sign-upp och din prenumeration på produkter i stället för med inbyggda funktioner i developer-portalen. Detta gör att din webbplats ska äga användardata och utföra valideringen av stegen i ett anpassat sätt.

## <a name="delegate-signin-up"> </a>Delegera developer logga in och registrering
Om du vill delegera developer inloggning och registrering till din befintliga webbplats, behöver du skapa en slutpunkt för särskilda delegering på webbplatsen som fungerar som en startpunkt för en sådan begäran som initieras från API Management developer-portalen.

Sista arbetsflödet är följande:

1. Utvecklare klickar på länken logga in eller registrera API Management developer-portalen
2. Webbläsaren omdirigeras till slutpunkten för delegering
3. Delegering slutpunkt i RETUR omdirigerar till eller visar Användargränssnittet be användaren att logga in i eller registrera
4. Om åtgärden lyckades kan omdirigeras användaren till API-hantering developer portalsidan de startas från

För att börja, begär vi första ställa in API-hantering att vidarebefordra via din slutpunkt för delegering. I publisher API Management-portalen klickar du på **säkerhet** och klicka sedan på den **delegering** fliken. Klicka på kryssrutan för att aktivera ombud inloggning & registrering.

![Delegering sida][api-management-delegation-signin-up]

* Bestäm vilka URL-Adressen för din slutpunkt för särskilda delegering tas och ange den i den **delegering slutpunkts-URL** fältet. 
* Ange en hemlighet som används för att beräkna en signatur som tillhandahålls för verifiering att säkerställa att begäran verkligen kommer från Azure API Management viktiga fältet delegering autentisering. Du kan klicka på den **generera** så API Management slumpmässigt Generera en nyckel för dig.

Nu måste du skapa den **delegering endpoint**. Det måste genomföra ett antal åtgärder:

1. Ta emot en begäran i följande format:
   
   > *http://www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL på sidan källa} & salt = {sträng} & sig = {sträng}*
   > 
   > 
   
    Fråga parametrar för inloggning / registrering fall:
   
   * **åtgärden**: identifierar vilken typ av begäran om delegering är det - vara **SignIn** i det här fallet
   * **returnUrl**: URL till sidan där användaren har klickat på en länk för inloggning eller registrering
   * **salt**: en särskild salt sträng som används för att beräkna en hash för säkerhet
   * **sig**: ett beräknade säkerhet hash-värde som ska användas för jämförelse med din egen beräknat hash-värde
2. Kontrollera att begäran kommer från Azure API Management (valfritt, men rekommenderas för säkerhet)
   
   * Beräkna en SHA512 HMAC hash för en sträng baserat på de **returnUrl** och **salt** fråga parametrar ([exempelkoden nedan]):
     
     > HMAC (**salt** + ”\n” + **returnUrl**)
     > 
     > 
   * Jämför ovan beräknade hash-värdet till värdet för den **sig** Frågeparametern. Om två hash-värdena matchar gå vidare till nästa steg, annars neka en begäran.
3. Kontrollera att du tar emot en begäran om inloggning-i/logga-up: den **åtgärden** frågeparameter sätts till ”**SignIn**”.
4. Ge användaren att logga in eller registrera UI
5. Om användaren har registrerat dig måste du skapa ett lämpligt för dem i API-hantering. [Skapa en användare] med API Management REST API. Se till att du har angett det användar-ID till samma i din användararkivet eller ett ID som du kan hålla reda på när du gör det.
6. När användaren har autentiserats:
   
   * [en enkel inloggning (SSO) token för begäran] via API Management REST API
   * Lägg till en frågeparameter returnUrl SSO-URL som du har fått från API-anrop ovan:
     
     > Till exempel https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * omdirigera användaren till ovanstående producerade URL

Förutom den **SignIn** igen, du kan också utföra kontohantering genom att följa de här stegen och genom att använda någon av följande åtgärder:

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**

Du måste överföra följande frågeparametrar för kontohanteringsåtgärder.

* **åtgärden**: identifierar vilken typ av begäran om delegering (ChangePassword, ChangeProfile eller CloseAccount)
* **användar-ID**: användar-id för kontot som ska hantera
* **salt**: en särskild salt sträng som används för att beräkna en hash för säkerhet
* **sig**: ett beräknade säkerhet hash-värde som ska användas för jämförelse med din egen beräknat hash-värde

## <a name="delegate-product-subscription"> </a>Delegera produkten prenumeration
Delegera produkten prenumeration fungerar på liknande sätt att delegera användaren logga in/ut. Sista arbetsflödet skulle vara följande:

1. Välj en produkt i API Management developer-portalen för utvecklare och klickar på knappen prenumerera
2. Webbläsaren omdirigeras till slutpunkten för delegering
3. Delegering endpoint utför nödvändiga produkten prenumeration steg – detta är och kan medföra att omdirigera till en annan sida för att begära faktureringsinformation ställa ytterligare frågor eller att lagra informationen och inte kräver någon åtgärd från användarens sida

Aktivera funktionen på den **delegering** klickar **delegera produkten prenumeration**.

Kontrollera sedan delegering slutpunkten utför följande åtgärder:

1. Ta emot en begäran i följande format:
   
   > *http://www.yourwebsite.com/apimdelegation?operation={operation}&productId={product prenumerera på} & userId = {användare som gjort begäran} & salt = {sträng} & sig = {sträng}*
   > 
   > 
   
    Frågeparametrar för produkten prenumeration fall:
   
   * **åtgärden**: identifierar vilken typ av begäran om delegering. För produkten prenumeration är begäranden giltiga alternativ:
     * ”Prenumerera”: en begäran om att prenumerera på en viss produkt med användaren angett ID (se nedan)
     * ”Avbryta prenumerationen”: en begäran om att avbryta prenumerationen en användare från en produkt
     * ”Förnya”: en begäran om att förnya en prenumeration (till exempel som kan ut)
   * **productId**: ID för den produkt som användaren begärde för att prenumerera på
   * **användar-ID**: ID för den användare som begäran gjordes
   * **salt**: en särskild salt sträng som används för att beräkna en hash för säkerhet
   * **sig**: ett beräknade säkerhet hash-värde som ska användas för jämförelse med din egen beräknat hash-värde
2. Kontrollera att begäran kommer från Azure API Management (valfritt, men rekommenderas för säkerhet)
   
   * Beräkna en HMAC SHA512 av en sträng som baseras på den **productId**, ** userId, och **salt** fråga parametrar:
     
     > HMAC (**salt** + ”\n” + **productId** + ”\n” + **userId**)
     > 
     > 
   * Jämför ovan beräknade hash-värdet till värdet för den **sig** Frågeparametern. Om två hash-värdena matchar gå vidare till nästa steg, annars neka en begäran.
3. Utföra någon prenumeration bearbetning baserat på vilken typ av åtgärd som angavs i **åtgärden** – till exempel fakturering, ytterligare frågor, osv.
4. Prenumerera på har prenumerera användaren att produkten på din sida, användaren till API Management-produkten av [anropa REST-API för produkten prenumeration].

## <a name="delegate-example-code"> </a> Exempelkod
Följande kodexempel visar hur du tar den *delegering valideringsnyckel*, som har angetts på skärmen delegering i publisher-portal för att skapa en HMAC, som sedan används för att verifiera signaturen, vilket bevisar att giltigheten för den överförda returnUrl. Samma kod fungerar för productId och användar-ID med små ändringar.

**C#-kod för att generera hash för returnUrl**

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
[en enkel inloggning (SSO) token för begäran]: https://docs.microsoft.com/rest/api/apimanagement/User/GenerateSsoUrl
[Skapa en användare]: https://docs.microsoft.com/rest/api/apimanagement/user/createorupdate
[anropa REST-API för produkten prenumeration]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Next steps]: #next-steps
[exempelkoden nedan]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
