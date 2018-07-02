---
title: Hur du använder SendGrid e-posttjänst (.NET) | Microsoft Docs
description: Lär dig hur skicka e-post med SendGrid-e-posttjänsten på Azure. Kodexempel som skrivits i C# och använder .NET-API.
services: ''
documentationcenter: .net
author: thinkingserious
manager: erikre
editor: ''
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.author: dx@sendgrid.com
ms.openlocfilehash: a5f07d02bfe4032d77a17e5972b88f6530125f28
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "27534863"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Hur du skickar e-post med SendGrid med Azure
## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga programmeringsuppgifter med SendGrid-e-posttjänsten på Azure. Exemplen är skrivna i C\# och stöder .NET Standard 1.3. Scenarier som tas upp är hur du skapar e-post, skicka e-post, lägga till bilagor, och aktivera olika e-post och spåra inställningar. Mer information om SendGrid och skicka e-post finns i [nästa steg] [ Next steps] avsnitt.

## <a name="what-is-the-sendgrid-email-service"></a>Vad är SendGrid e-posttjänst?
SendGrid är en [molnbaserade e-posttjänst] som ger tillförlitliga [transaktionella e-postleverans], skalbarhet och analys i realtid tillsammans med flexibel API: er som gör det enkelt anpassad integrering. Vanliga användningsområden för SendGrid är:

* Skicka automatiskt inleveranser eller köp bekräftelser till kunder.
* Administrera distribution visas för att skicka kunder månatliga reklamblad och kampanjer.
* Samlar in realtid mätvärden för sådant som blockerade e-post och engagera kunder.
* Vidarebefordran av kundfrågor.
* Bearbetning av inkommande e-postmeddelanden.

Mer information finns [ https://sendgrid.com ](https://sendgrid.com) eller Sendgrid's [C#-biblioteket] [ sendgrid-csharp] GitHub-lagringsplatsen.

## <a name="create-a-sendgrid-account"></a>Skapa ett SendGrid-konto
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Referens SendGrid .NET-klassbibliotek
Den [SendGrid NuGet-paketet](https://www.nuget.org/packages/Sendgrid) är det enklaste sättet att hämta SendGrid-API och att konfigurera ditt program med alla beroenden. NuGet är ett Visual Studio-tillägg som ingår i Microsoft Visual Studio 2015 och ovan som gör det enkelt att installera och uppdatera bibliotek och verktyg.

> [!NOTE]
> Du kan installera NuGet om du kör en tidigare version av Visual Studio än Visual Studio 2015 [ http://www.nuget.org ](http://www.nuget.org), och klicka på den **installera NuGet** knappen.
>
>

Om du vill installera SendGrid NuGet-paketet i ditt program, gör du följande:

1. Klicka på **nytt projekt** och välj en **mallen**.

   ![Skapa ett nytt projekt][create-new-project]
2. I **Solution Explorer**, högerklicka på **referenser**, klicka på **hantera NuGet-paket**.

   ![SendGrid NuGet-paketet][SendGrid-NuGet-package]
3. Sök efter **SendGrid** och välj den **SendGrid** objekt i resultatlistan.
4. Välj den senaste säkra versionen av Nuget-paketet i version listrutan för att kunna arbeta med objektmodellen och API: er som visas i den här artikeln.

   ![SendGrid-paket][sendgrid-package]
5. Klicka på **installera** att slutföra installationen och stäng sedan dialogrutan.

Sendgrid's .NET-klassbibliotek kallas **SendGrid**. Det innehåller följande namnområden:

* **SendGrid** för att kommunicera med Sendgrid's API.
* **SendGrid.Helpers.Mail** för helper-metoder för att skapa SendGridMessage-objekt som anger hur du skickar e-postmeddelanden.

Lägg till följande kod namnrymdsdeklarationer överst i en C#-fil som du vill komma åt SendGrid e-posttjänsten.

    using SendGrid;
    using SendGrid.Helpers.Mail;

## <a name="how-to-create-an-email"></a>Så här: skapa ett e-postmeddelande
Använd den **SendGridMessage** objekt för att skapa ett e-postmeddelande. När meddelandeobjektet har skapats kan ange du egenskaper och metoder, inklusive e-postavsändaren, e-postmottagare syfte och brödtexten i e-postmeddelandet.

I följande exempel visar hur du skapar ett fullständigt ifyllda e-objekt:

    var msg = new SendGridMessage();

    msg.SetFrom(new EmailAddress("dx@example.com", "SendGrid DX Team"));

    var recipients = new List<EmailAddress>
    {
        new EmailAddress("jeff@example.com", "Jeff Smith"),
        new EmailAddress("anna@example.com", "Anna Lidman"),
        new EmailAddress("peter@example.com", "Peter Saddow")
    };
    msg.AddTos(recipients);

    msg.SetSubject("Testing the SendGrid C# Library");

    msg.AddContent(MimeType.Text, "Hello World plain text!");
    msg.AddContent(MimeType.Html, "<p>Hello World!</p>");

Mer information om alla egenskaper och metoder som stöds av den **SendGrid** skriver, se [sendgrid csharp] [ sendgrid-csharp] på GitHub.

## <a name="how-to-send-an-email"></a>Så här: skicka ett e-postmeddelande
Du kan skicka den med hjälp av Sendgrid's API när du har skapat ett e-postmeddelande. Du kan också använda [. NETS inbyggd i biblioteket][NET-library].

Skicka e-post måste du ange din SendGrid API-nyckel. Om du behöver information om hur du konfigurerar API-nycklar, går du till Sendgrid's API-nycklar [dokumentationen][documentation].

Du kan lagra dessa autentiseringsuppgifter via Azure Portal genom att klicka på inställningar för program och lägga till nyckel/värde-par under App-inställningar.

 ![Azure app-inställningar][azure_app_settings]

 Sedan kan du kan komma åt dem på följande sätt:

    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
    var client = new SendGridClient(apiKey);

Följande exempel visar hur du skickar ett e-postmeddelande med ett konsolprogram SendGrid Web API.

    using System;
    using System.Threading.Tasks;
    using SendGrid;
    using SendGrid.Helpers.Mail;

    namespace Example
    {
        internal class Example
        {
            private static void Main()
            {
                Execute().Wait();
            }

            static async Task Execute()
            {
                var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
                var client = new SendGridClient(apiKey);
                var msg = new SendGridMessage()
                {
                    From = new EmailAddress("test@example.com", "DX Team"),
                    Subject = "Hello World from the SendGrid CSharp SDK!",
                    PlainTextContent = "Hello, Email!",
                    HtmlContent = "<strong>Hello, Email!</strong>"
                };
                msg.AddTo(new EmailAddress("test@example.com", "Test User"));
                var response = await client.SendEmailAsync(msg);
            }
        }
    }
    
## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>Så här: skicka e-post från ASP .NET Core API: et med MailHelper-klass

I exemplet nedan kan användas för att skicka ett enda e-postmeddelande till flera personer från ASP .NET Core-API med hjälp av den `MailHelper` klass `SendGrid.Helpers.Mail` namnområde. Det här exemplet använder vi ASP .NET Core 1.0. 

I det här exemplet API-nyckeln har lagrats i den `appsettings.json` -fil som kan åsidosättas i Azure Portal som visas i ovanstående exempel.

Innehållet i `appsettings.json` filen bör likna:

    {
       "Logging": {
       "IncludeScopes": false,
       "LogLevel": {
       "Default": "Debug",
       "System": "Information",
       "Microsoft": "Information"
         }
       },
     "SENDGRID_API_KEY": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    }

Först måste vi behöver lägga till den nedan koden i den `Startup.cs` -filen för .NET Core API-projekt. Detta krävs så att vi kan nå den `SENDGRID_API_KEY` från den `appsettings.json` filen med hjälp av beroendeinmatning i API-kontrollanten. Den `IConfiguration` gränssnitt kan matas in i konstruktorn för styrenheten har lagt till i den `ConfigureServices` metoden nedan. Innehållet i `Startup.cs` filen ser ut som följande när du lägger till kod som krävs:

        public IConfigurationRoot Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            // Add mvc here
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }

På domänkontrollant efter att Injicera den `IConfiguration` -gränssnittet, som vi kan använda den `CreateSingleEmailToMultipleRecipients` metod för den `MailHelper` klassen för att skicka ett enda e-postmeddelande till flera mottagare. Metoden tar en ytterligare en boolesk parameter med namnet `showAllRecipients`. Den här parametern kan användas för att kontrollen om e-postmottagare kommer att kunna se varandras e-postadress i avsnittet till i e-postrubrik. Exempelkod för domänkontrollanten ska vara som nedan 

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using SendGrid;
    using SendGrid.Helpers.Mail;
    using Microsoft.Extensions.Configuration;

    namespace SendgridMailApp.Controllers
    {
        [Route("api/[controller]")]
        public class NotificationController : Controller
        {
           private readonly IConfiguration _configuration;

           public NotificationController(IConfiguration configuration)
           {
             _configuration = configuration;
           }      
        
           [Route("SendNotification")]
           public async Task PostMessage()
           {
              var apiKey = _configuration.GetSection("SENDGRID_API_KEY").Value;
              var client = new SendGridClient(apiKey);
              var from = new EmailAddress("test1@example.com", "Example User 1");
              List<EmailAddress> tos = new List<EmailAddress>
              {
                  new EmailAddress("test2@example.com", "Example User 2"),
                  new EmailAddress("test3@example.com", "Example User 3"),
                  new EmailAddress("test4@example.com","Example User 4")
              };
            
              var subject = "Hello world email from Sendgrid ";
              var htmlContent = "<strong>Hello world with HTML content</strong>";
              var displayRecipients = false; // set this to true if you want recipients to see each others mail id 
              var msg = MailHelper.CreateSingleEmailToMultipleRecipients(from, tos, subject, "", htmlContent, false);
              var response = await client.SendEmailAsync(msg);
          }
       }
    }
    
## <a name="how-to-add-an-attachment"></a>Så här: Lägg till en bifogad fil
Bifogade filer kan läggas till ett meddelande genom att anropa den **AddAttachment** metod och minimalt ange filnamnet och Base64-kodade innehåll du vill bifoga. Du kan innehålla flera bifogade filer genom att anropa den här metoden när du vill ansluta för varje fil eller med hjälp av den **AddAttachments** metod. Exemplet nedan visar hur du lägger till en bifogad fil till ett meddelande:

    var banner2 = new Attachment()
    {
        Content = Convert.ToBase64String(raw_content),
        Type = "image/png",
        Filename = "banner2.png",
        Disposition = "inline",
        ContentId = "Banner 2"
    };
    msg.AddAttachment(banner2);

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Så här: använda e-postinställningar för att aktivera sidfötter, spårning och analys
SendGrid ger ytterligare e-postfunktioner genom att använda postinställningar för e-och spårning. De här inställningarna kan läggas till ett e-postmeddelande för att aktivera vissa funktioner, till exempel klicka på spårning, Google analytics, prenumeration spårning och så vidare. En fullständig lista över appar, finns det [inställningar dokumentationen][settings-documentation].

Appar kan tillämpas på **SendGrid** e-postmeddelanden med metoder som implementeras som en del av den **SendGridMessage** klass. Följande exempel visar sidfoten och på Spåra filter:

Följande exempel visar sidfoten och på Spåra filter:

### <a name="footer-settings"></a>Inställningar för sidfot
    msg.SetFooterSetting(
                         true,
                         "Some Footer HTML",
                         "<strong>Some Footer Text</strong>");

### <a name="click-tracking"></a>Klicka på spårning
    msg.SetClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Så här: använda ytterligare SendGrid tjänster
SendGrid innehåller flera API: er och webhooks som du kan använda för att utnyttja ytterligare funktioner i Azure-program. Mer information finns i [SendGrid API-referens][SendGrid API documentation].

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om tjänsten SendGrid e-post, kan du följa dessa länkar om du vill veta mer.

* SendGrid C\# biblioteket lagringsplatsen: [sendgrid csharp][sendgrid-csharp]
* SendGrid API-dokumentationen: <https://sendgrid.com/docs>

[Next steps]: #next-steps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices

[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/new-project.png
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/reference.png
[sendgrid-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid-package.png
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/azure-app-settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
[App Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/api_v3.html
[NET-library]: https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html#-Using-NETs-Builtin-SMTP-Library
[documentation]: https://sendgrid.com/docs/Classroom/Send/api_keys.html
[settings-documentation]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html

[molnbaserade e-posttjänst]: https://sendgrid.com/solutions
[transaktionella e-postleverans]: https://sendgrid.com/use-cases/transactional-email

