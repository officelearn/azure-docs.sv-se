---
title: Så här använder du e-posttjänsten SendGrid (Node.js) | Microsoft-dokument
description: Läs om hur du skickar e-post med SendGrid-e-posttjänsten på Azure. Kodexempel som skrivits med API:et för nod.js.
services: ''
documentationcenter: nodejs
author: erikre
manager: wpickett
editor: ''
ms.assetid: cac444b4-26b0-45ea-9c3d-eca28d57dacb
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 01/05/2016
ms.author: erikre
ms.openlocfilehash: f2d653441598a47986913d525057672eed24b435
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60931725"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Så här skickar du e-post med SendGrid från Node.js

Den här guiden visar hur du utför vanliga programmeringsuppgifter med SendGrid-e-posttjänsten på Azure. Exemplen skrivs med API:et för nod.js. Scenarierna omfattar **att skapa e-post,** **skicka e-post,** **lägga till bifogade filer,** **använda filter**och **uppdatera egenskaper**. Mer information om SendGrid och skicka e-post finns i avsnittet [Nästa steg.](#next-steps)

## <a name="what-is-the-sendgrid-email-service"></a>Vad är SendGrids e-posttjänst?

SendGrid är en [molnbaserad e-posttjänst] som ger tillförlitlig [transaktionsbaserad e-postleverans,]skalbarhet och realtidsanalys tillsammans med flexibla API:er som gör anpassad integrering enkel. Vanliga SendGrid-användningsscenarier är:

* Skicka kvitton till kunder automatiskt
* Administrera distributionslistor för att skicka kunder månatliga e-flygblad och specialerbjudanden
* Samla in mätvärden i realtid för saker som blockerad e-post och kundens svarstider
* Generera rapporter för att identifiera trender
* Vidarebefordra kundfrågor
* E-postmeddelanden från ditt program

Mer information finns [https://sendgrid.com](https://sendgrid.com)i .

## <a name="create-a-sendgrid-account"></a>Skapa ett SendGrid-konto

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>Referera till sendgrid-nod.js-modulen

SendGrid-modulen för Node.js kan installeras via nodpakethanteraren (npm) med hjälp av följande kommando:

```bash
npm install sendgrid
```

Efter installationen kan du kräva modulen i programmet med hjälp av följande kod:

```javascript
var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);
```

SendGrid-modulen exporterar **funktionerna SendGrid** och **E-post.**
**SendGrid** ansvarar för att skicka e-post via webb-API, medan **e-post** kapslar in ett e-postmeddelande.

## <a name="how-to-create-an-email"></a>Så här skapar du ett e-postmeddelande

Att skapa ett e-postmeddelande med modulen SendGrid innebär att du först skapar ett e-postmeddelande med hjälp av e-postfunktionen och sedan skickar det med funktionen SendGrid. Följande är ett exempel på hur du skapar ett nytt meddelande med hjälp av e-postfunktionen:

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
```

Du kan också ange ett HTML-meddelande för klienter som stöder det genom att ange html-egenskapen. Ett exempel:

```javascript
html: This is a sample <b>HTML<b> email message.
```

Om du anger både text- och html-egenskaperna kan du få ett graciöst återgång till textinnehåll för klienter som inte stöder HTML-meddelanden.

Mer information om alla egenskaper som stöds av funktionen E-post finns i [sendgrid-nodejs][sendgrid-nodejs].

## <a name="how-to-send-an-email"></a>Så här skickar du ett e-postmeddelande

När du har skapat ett e-postmeddelande med hjälp av e-postfunktionen kan du skicka det med webb-API:et som tillhandahålls av SendGrid. 

### <a name="web-api"></a>Webb-API

```javascript
sendgrid.send(email, function(err, json){
    if(err) { return console.error(err); }
    console.log(json);
});
```

> [!NOTE]
> Medan ovanstående exempel visar att du skickar ett e-postobjekt och motringningsfunktion, kan du också direkt anropa sändningsfunktionen genom att direkt ange e-postegenskaper. Ett exempel:  
> 
> ```javascript
> sendgrid.send({
> to: 'john@contoso.com',
> from: 'anna@contoso.com',
> subject: 'test mail',
> text: 'This is a sample email message.'
> });
> ```
>

## <a name="how-to-add-an-attachment"></a>Så här lägger du till en bifogad fil
Bifogade filer kan läggas till i ett meddelande genom att ange filnamn och sökvägar i **filegenskapen.** I följande exempel visas att en bifogad fil skickas:

```javascript
sendgrid.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.',
    files: [
        {
            filename:     '',           // required only if file.content is used.
            contentType:  '',           // optional
            cid:          '',           // optional, used to specify cid for inline content
            path:         '',           //
            url:          '',           // == One of these three options is required
            content:      ('' | Buffer) //
        }
    ],
});
```

> [!NOTE]
> När du använder egenskapen **files** måste filen vara tillgänglig via [fs.readFile](https://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Om filen som du vill bifoga finns i Azure Storage, till exempel i en Blob-behållare, måste du först kopiera filen till **files** lokal lagring eller till en Azure-enhet innan den kan skickas som en bifogad fil med hjälp av filegenskapen.
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Så här: Använd filter för att aktivera sidföns och spårning

SendGrid tillhandahåller ytterligare e-postfunktioner med hjälp av filter. Det här är inställningar som kan läggas till i ett e-postmeddelande för att aktivera specifika funktioner som att aktivera klickspårning, Google Analytics, prenumerationsspårning och så vidare. En fullständig lista över filter finns i [Filterinställningar][Filter Settings].

Filter kan användas på ett meddelande med hjälp av egenskapen **filters.**
Varje filter anges av en hash som innehåller filterspecifika inställningar.
I följande exempel visas sidfoten och klickspårningsfilter:

### <a name="footer"></a>Sidfot

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'footer': {
        'settings': {
            'enable': 1,
            'text/plain': 'This is a text footer.'
        }
    }
});

sendgrid.send(email);
```

### <a name="click-tracking"></a>Klicka på Spårning

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'clicktrack': {
        'settings': {
            'enable': 1
        }
    }
});

sendgrid.send(email);
```

## <a name="how-to-update-email-properties"></a>Så här uppdaterar du e-postegenskaper

Vissa e-postegenskaper kan skrivas över med **setProperty** eller läggas till med **addProperty**. Du kan till exempel lägga till ytterligare mottagare genom att använda

```javascript
email.addTo('jeff@contoso.com');
```

eller ställa in ett filter med hjälp av

```javascript
email.addFilter('footer', 'enable', 1);
email.addFilter('footer', 'text/html', '<strong>boo</strong>');
```

Mer information finns i [sendgrid-nodejs][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>Så här använder du ytterligare SendGrid-tjänster

SendGrid erbjuder webbaserade API:er som du kan använda för att utnyttja ytterligare SendGrid-funktioner från ditt Azure-program. Fullständig information finns i [SendGrid API-dokumentationen][SendGrid API documentation].

## <a name="next-steps"></a>Efterföljande moment

Nu när du har lärt dig grunderna i SendGrid e-posttjänsten, följ dessa länkar för att lära dig mer.

* SendGrid Node.js modularkiv: [sendgrid-nodejs][sendgrid-nodejs]
* SendGrid API-dokumentation:<https://sendgrid.com/docs>
* SendGrid specialerbjudande för Azure-kunder:[http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[molnbaserad e-posttjänst]: https://sendgrid.com/email-solutions
[transaktionell e-postleverans]: https://sendgrid.com/transactional-email
