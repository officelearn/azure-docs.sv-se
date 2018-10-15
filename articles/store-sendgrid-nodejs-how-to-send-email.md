---
title: Hur du använder e-posttjänsten SendGrid (Node.js) | Microsoft Docs
description: Lär dig hur skicka e-postmeddelande med e-posttjänsten SendGrid på Azure. Kodexempel som skrivits med Node.js-API.
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
ms.openlocfilehash: 8908a6ceb87e2c0e4c7222e8a8e72fa7ebfa7f82
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309271"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Hur du skickar e-post med SendGrid från Node.js

Den här guiden visar hur du utför vanliga programmeringsspråk uppgifter med e-posttjänsten SendGrid på Azure. Exemplen är skrivna med hjälp av Node.js-API. Scenarier som omfattas är **konstruera e-post**, **skicka e-postmeddelande**, **att lägga till bilagor**, **med hjälp av filter**, och **uppdatera egenskaperna för**. Mer information om SendGrid och skicka e-post finns i den [nästa steg](#next-steps) avsnittet.

## <a name="what-is-the-sendgrid-email-service"></a>Vad är e-posttjänsten SendGrid?

SendGrid är en [molnbaserade e-posttjänsten] som ger tillförlitliga [transaktionsbaserad e-postleverans], skalbarhet och realtidsanalys tillsammans med flexibla API: er som gör anpassad integration enkel. Vanliga Användningsscenarier för SendGrid är:

* Skicka automatiskt kvitton till kunder
* Administrera distribution visas för att skicka kunder månatliga e-flygblad och specialerbjudanden
* Samla in i realtid mått för saker som blockerade e-post och kundsvarstid
* Generera rapporter för att identifiera trender
* Vidarebefordran av kundfrågor
* E-postmeddelanden från ditt program

Mer information finns på [https://sendgrid.com](https://sendgrid.com).

## <a name="create-a-sendgrid-account"></a>Skapa ett SendGrid-konto

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>Referera till SendGrid Node.js-modulen

SendGrid-modulen för Node.js kan installeras via node package manager (npm) med hjälp av följande kommando:

```bash
npm install sendgrid
```

Efter installationen kan kräva du modulen i ditt program med hjälp av följande kod:

```javascript
var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);
```

SendGrid-modulen exporterar den **SendGrid** och **e-post** funktioner.
**SendGrid** ansvarar för att skicka e-post via webb-API, medan **e-post** kapslar in ett e-postmeddelande.

## <a name="how-to-create-an-email"></a>Så här: skapa ett e-postmeddelande

Skapa ett e-postmeddelande med SendGrid-modulen innebär att du först skapar ett e-postmeddelande med hjälp av e-post-funktion och sedan skicka den med hjälp av SendGrid-funktionen. Följande är ett exempel för att skapa ett nytt meddelande med hjälp av e-post-funktion:

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
```

Du kan också ange ett HTML-meddelande för klienter som stöder html-egenskapen. Exempel:

```javascript
html: This is a sample <b>HTML<b> email message.
```

Ange både text och HTML-egenskaper innehåller korrekt användning av textinnehåll för klienter som inte stöder HTML-meddelanden.

Mer information om alla egenskaper som stöds av funktionen för e-post finns i [sendgrid nodejs][sendgrid-nodejs].

## <a name="how-to-send-an-email"></a>Så här: skicka ett e-postmeddelande

Du kan skicka den med hjälp av webb-API från SendGrid när du har skapat ett e-postmeddelande med hjälp av e-post-funktion. 

### <a name="web-api"></a>Webb-API

```javascript
sendgrid.send(email, function(err, json){
    if(err) { return console.error(err); }
    console.log(json);
});
```

> [!NOTE]
> När ovanstående exempel visa skicka i en e-objektet och återanrop funktion, kan du också direkt anropa funktionen Skicka genom att ange postegenskaper för e-direkt. Exempel:  
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

## <a name="how-to-add-an-attachment"></a>Så här: Lägg till en bifogad fil
Bifogade filer som kan läggas till ett meddelande genom att ange filnamn och sökväg i den **filer** egenskapen. I följande exempel visar att skicka en bifogad fil:

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
> När du använder den **filer** egenskapen filen måste vara tillgängligt via [fs.readFile](http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Om den fil du vill bifoga finns i Azure Storage, till exempel i en Blob-behållare, måste du först kopiera filen till lokal lagring eller till en Azure-enheten innan den kan skickas som en bifogad fil med hjälp av den **filer** egenskapen.
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Så här: använda filter för att aktivera sidfötter och spårning

SendGrid ger ytterligare e-funktioner genom att använda filter. Det här är inställningar som kan läggas till i ett e-postmeddelande för att aktivera vissa funktioner, till exempel aktivera klickspårning, Google analytics, prenumeration, spårnings- och så vidare. En fullständig lista över filter finns i [filterinställningar][Filter Settings].

Filter kan tillämpas på ett meddelande med hjälp av den **filter** egenskapen.
Varje filter anges av ett hash-värde som innehåller filter-specifika inställningar.
I följande exempel visar sidfoten och klickar på Spåra filter:

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

### <a name="click-tracking"></a>Klickspårning

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

## <a name="how-to-update-email-properties"></a>Så här: uppdatera postegenskaper för e-

Vissa postegenskaper för e-kan skrivas över med hjälp av **setProperty** eller läggs till med **addProperty**. Du kan till exempel lägga till ytterligare mottagare med hjälp av

```javascript
email.addTo('jeff@contoso.com');
```

eller ange ett filter med hjälp av

```javascript
email.addFilter('footer', 'enable', 1);
email.addFilter('footer', 'text/html', '<strong>boo</strong>');
```

Mer information finns i [sendgrid nodejs][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>Så här: använda ytterligare SendGrid-tjänster

SendGrid erbjuder webbaserad API: er som du kan använda för att utnyttja ytterligare funktioner för SendGrid från ditt Azure-program. Fullständig information finns i [SendGrid API-dokumentation][SendGrid API documentation].

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna för den e-posttjänsten SendGrid kan du följa dessa länkar om du vill veta mer.

* SendGrid Node.js-modulen lagringsplats: [sendgrid-nodejs][sendgrid-nodejs]
* SendGrid API-dokumentation: <https://sendgrid.com/docs>
* SendGrid specialerbjudande för Azure-kunder: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[molnbaserade e-posttjänsten]: https://sendgrid.com/email-solutions
[transaktionsbaserad e-postleverans]: https://sendgrid.com/transactional-email
