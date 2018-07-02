---
title: Hur du använder SendGrid e-posttjänst (Node.js) | Microsoft Docs
description: Lär dig hur skicka e-post med SendGrid-e-posttjänsten på Azure. Kodexempel som skrivits med Node.js-API.
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
ms.openlocfilehash: 327cea3a24cc47a9cc463b37cc2346ebc475ef7f
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "23873991"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Hur du skickar e-post med SendGrid från Node.js
Den här guiden visar hur du utför vanliga programmeringsuppgifter med SendGrid-e-posttjänsten på Azure. Exemplen är skrivna med Node.js-API. Scenarier som tas upp inkluderar **konstruera e-post**, **skicka e-post**, **lägga till bilagor**, **med hjälp av filter**, och **uppdatera egenskaperna för**. Mer information om SendGrid och skicka e-post finns i [nästa steg](#next-steps) avsnitt.

## <a name="what-is-the-sendgrid-email-service"></a>Vad är SendGrid e-posttjänst?
SendGrid är en [molnbaserade e-posttjänst] som ger tillförlitliga [transaktionella e-postleverans], skalbarhet och analys i realtid tillsammans med flexibel API: er som gör det enkelt anpassad integrering. Vanliga Användningsscenarier för SendGrid är:

* Skicka automatiskt kvitton till kunder
* Administrera distribution visas för att skicka kunder månatliga e-reklamblad och specialerbjudanden
* Samla in realtid mätvärden för sådant som blockerade e-post och kunden svarstider
* Generera rapporter för att identifiera trender
* Vidarebefordran av kundfrågor
* E-postaviseringar från ditt program

Mer information finns på [https://sendgrid.com](https://sendgrid.com).

## <a name="create-a-sendgrid-account"></a>Skapa ett SendGrid-konto
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>Referera till SendGrid Node.js-modul
Modulen SendGrid för Node.js kan installeras via noden package manager (npm) med hjälp av följande kommando:

    npm install sendgrid

Efter installationen kan kräva du modulen i ditt program med hjälp av följande kod:

    var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);

Modulen SendGrid exporterar den **SendGrid** och **e-post** funktioner.
**SendGrid** ansvarar för att skicka e-post via Web API medan **e-post** kapslar in ett e-postmeddelande.

## <a name="how-to-create-an-email"></a>Så här: skapa ett e-postmeddelande
Skapa ett e-postmeddelande med hjälp av modulen SendGrid innebär att först skapa ett e-postmeddelande med hjälp av funktionen för e-post och skicka det med hjälp av funktionen SendGrid. Följande är ett exempel på hur du skapar ett nytt meddelande med hjälp av funktionen för e-post:

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

Du kan också ange ett HTML-meddelande för klienter som stöder html-egenskapen. Exempel:

    html: This is a sample <b>HTML<b> email message.

Ange egenskaper för både text och html ger korrekt användning av textinnehåll för klienter som inte stöder HTML-meddelanden.

Mer information om alla egenskaper som stöds av funktionen för e-post finns [sendgrid nodejs][sendgrid-nodejs].

## <a name="how-to-send-an-email"></a>Så här: skicka ett e-postmeddelande
Du kan skicka den med hjälp av Web-API som tillhandahålls av SendGrid när du har skapat ett e-postmeddelande med hjälp av funktionen för e-post. 

### <a name="web-api"></a>Webb-API
    sendgrid.send(email, function(err, json){
        if(err) { return console.error(err); }
        console.log(json);
    });

> [!NOTE]
> Du kan också direkt anropa send-funktionen genom att ange egenskaper för e-post direkt när ovanstående exempel visa passera i en e-objektet och motringning funktion. Exempel:  
> 
> `````
> sendgrid.send({
> to: 'john@contoso.com',
> from: 'anna@contoso.com',
> subject: 'test mail',
> text: 'This is a sample email message.'
> });
> `````
> 
> 

## <a name="how-to-add-an-attachment"></a>Så här: Lägg till en bifogad fil
Bifogade filer kan läggas till ett meddelande genom att ange filnamn och sökväg i den **filer** egenskapen. Exemplet nedan visar skicka bifogade filer:

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

> [!NOTE]
> När du använder den **filer** egenskapen filen måste vara tillgängligt via [fs.readFile](http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Om filen som du vill ansluta finns i Azure Storage, exempelvis en Blob-behållare, måste du först kopiera filen till lokal lagring eller en Azure-enheten innan den kan skickas som en bifogad fil med hjälp av den **filer** egenskapen.
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Så här: använda filter för att aktivera sidfötter och spårning
SendGrid ger ytterligare e-postfunktioner genom att använda filter. Dessa finns inställningar som du kan lägga till ett e-postmeddelande för att aktivera vissa funktioner, till exempel aktivera Klicka spårning, Google analytics, prenumeration spårning och så vidare. En fullständig lista över filter finns [filterinställningar][Filter Settings].

Filter kan tillämpas på ett meddelande med hjälp av den **filter** egenskapen.
Varje filter som anges av ett hash-värde som innehåller filter-specifika inställningar.
Följande exempel visar sidfoten och på Spåra filter:

### <a name="footer"></a>Sidfot
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

### <a name="click-tracking"></a>Klicka på spårning
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

## <a name="how-to-update-email-properties"></a>Så här: uppdatera egenskaper för e-post
Vissa egenskaper för e-post kan skrivas över med **ange * egenskapen*** eller läggas till med hjälp av **lägga till*egenskapen ***. Du kan till exempel lägga till ytterligare mottagare med hjälp av

    email.addTo('jeff@contoso.com');

eller ange ett filter med hjälp av

    email.addFilter('footer', 'enable', 1);
    email.addFilter('footer', 'text/html', '<strong>boo</strong>');

Mer information finns i [sendgrid nodejs][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>Så här: använda ytterligare SendGrid tjänster
SendGrid erbjuder webbaserade API: er som du kan använda för att utnyttja ytterligare funktioner för SendGrid från Azure-program. Fullständig information finns i [SendGrid API-dokumentationen][SendGrid API documentation].

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om tjänsten SendGrid e-post, kan du följa dessa länkar om du vill veta mer.

* SendGrid Node.js modulen databasen: [sendgrid nodejs][sendgrid-nodejs]
* SendGrid API-dokumentationen: <https://sendgrid.com/docs>
* SendGrid specialerbjudande för Azure-kunder: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[molnbaserade e-posttjänst]: https://sendgrid.com/email-solutions
[transaktionella e-postleverans]: https://sendgrid.com/transactional-email
