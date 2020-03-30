---
title: Så här använder du e-posttjänsten SendGrid (.NET) | Microsoft-dokument
description: Läs om hur du skickar e-post med SendGrid-e-posttjänsten på Azure. Kodexempel skrivna i C# och använd .NET API.
services: ''
documentationcenter: .net
author: georgewallace
ms.author: gwallace
editor: ''
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.reviewer: dx@sendgrid.com
ms.openlocfilehash: 33df6b5c8c5c16a6eb896944de05068affc2b407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062208"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Så här skickar du e-post med SendGrid med Azure
## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga programmeringsuppgifter med SendGrid-e-posttjänsten på Azure. Exemplen är skrivna i C\# och stöder .NET Standard 1.3. Scenarierna omfattar att skapa e-post, skicka e-post, lägga till bilagor och aktivera olika e-post- och spårningsinställningar. Mer information om SendGrid och skicka e-post finns i avsnittet [Nästa steg.][Next steps]

## <a name="what-is-the-sendgrid-email-service"></a>Vad är SendGrids e-posttjänst?
SendGrid är en [molnbaserad e-posttjänst] som ger tillförlitlig [transaktionsbaserad e-postleverans,]skalbarhet och realtidsanalys tillsammans med flexibla API:er som gör anpassad integrering enkel. Vanliga SendGrid användningsfall inkluderar:

* Skicka kvitton eller inköpsbekräftelser automatiskt till kunder.
* Administrera distributionslistor för att skicka kunder månatliga flygblad och kampanjer.
* Samla in mätvärden i realtid för saker som blockerad e-post och kundengagemang.
* Vidarebefordra kundfrågor.
* Bearbeta inkommande e-post.

Mer information finns [https://sendgrid.com](https://sendgrid.com) i eller sendGrids [C#-bibliotek][sendgrid-csharp] GitHub repo.

## <a name="create-a-sendgrid-account"></a>Skapa ett SendGrid-konto
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Referera till klassbiblioteket SendGrid .NET
[SendGrid NuGet-paketet](https://www.nuget.org/packages/Sendgrid) är det enklaste sättet att hämta SendGrid-API:et och konfigurera ditt program med alla beroenden. NuGet är ett Visual Studio-tillägg som ingår i Microsoft Visual Studio 2015 och högre som gör det enkelt att installera och uppdatera bibliotek och verktyg.

> [!NOTE]
> Om du vill installera NuGet om du kör en version av Visual [https://www.nuget.org](https://www.nuget.org)Studio tidigare än Visual Studio 2015 besöker du och klickar på knappen **Installera NuGet.**
>
>

Så här installerar du SendGrid NuGet-paketet i ditt program:

1. Klicka på **Nytt projekt** och välj en **mall**.

   ![Skapa ett nytt projekt][create-new-project]
2. Högerklicka på **Referenser**i **Solution Explorer**och klicka sedan på **Hantera NuGet-paket**.

   ![SendGrid NuGet-paketet][SendGrid-NuGet-package]
3. Sök efter **SendGrid** och välj **SendGrid-objektet** i resultatlistan.
4. Välj den senaste stabila versionen av Nuget-paketet från versionsrullgardermenyn för att kunna arbeta med objektmodellen och API:erna som visas i den här artikeln.

   ![SendGrid-paket][sendgrid-package]
5. Klicka på **Installera** för att slutföra installationen och stäng sedan den här dialogrutan.

SendGrids .NET-klassbibliotek heter **SendGrid**. Den innehåller följande namnområden:

* **SendGrid** för att kommunicera med SendGrids API.
* **SendGrid.Helpers.Mail** för hjälpmetoder för att enkelt skapa SendGridMessage-objekt som anger hur e-postmeddelanden ska skickas.

Lägg till följande kodnamnområdesdeklarationer överst i en C#-fil där du vill programmässigt komma åt SendGrid-e-posttjänsten.

    using SendGrid;
    using SendGrid.Helpers.Mail;

## <a name="how-to-create-an-email"></a>Så här skapar du ett e-postmeddelande
Använd **SendGridMessage-objektet** för att skapa ett e-postmeddelande. När meddelandeobjektet har skapats kan du ange egenskaper och metoder, inklusive e-postavsändaren, e-postmottagaren och e-postmeddelandets ämne och brödtext.

I följande exempel visas hur du skapar ett fullständigt fyllt e-postobjekt:

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

Mer information om alla egenskaper och metoder som stöds av typen **SendGrid** finns i [sendgrid-csharp][sendgrid-csharp] på GitHub.

## <a name="how-to-send-an-email"></a>Så här skickar du ett e-postmeddelande
När du har skapat ett e-postmeddelande kan du skicka det med SendGrids API. Alternativt kan du använda [. NET:s inbyggda bibliotek][NET-library].

Att skicka e-post kräver att du anger din SendGrid API-nyckel. Om du behöver information om hur du konfigurerar API-nycklar besöker du SendGrids [API Keys-dokumentation][documentation].

Du kan lagra dessa autentiseringsuppgifter via din Azure-portal genom att klicka på Programinställningar och lägga till nyckel-/värdepar under Appinställningar.

 ![Inställningar för Azure-appar][azure_app_settings]

 Sedan kan du komma åt dem på följande sätt:

    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
    var client = new SendGridClient(apiKey);

Följande exempel visar hur du skickar ett e-postmeddelande med SendGrid Web API med ett konsolprogram.

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
    
## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>Så här skickar du e-post från ASP .NET Core API med klassen MailHelper

Nedanstående exempel kan användas för att skicka ett enda e-postmeddelande till `MailHelper` flera `SendGrid.Helpers.Mail` personer från ASP .NET Core API med hjälp av klassen namespace. I det här exemplet använder vi ASP .NET Core 1.0. 

I det här exemplet har API-nyckeln lagrats i `appsettings.json` filen som kan åsidosättas från Azure-portalen som visas i ovanstående exempel.

Innehållet i `appsettings.json` filen bör se ut ungefär som:

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

Först måste vi lägga till nedanstående kod i `Startup.cs` filen i .NET Core API-projektet. Detta krävs så att vi `SENDGRID_API_KEY` kan `appsettings.json` komma åt filen från filen med hjälp av beroendeinjektion i API-styrenheten. Gränssnittet `IConfiguration` kan injiceras på konstruktorn av styrenheten `ConfigureServices` efter att ha lagt till den i metoden nedan. Innehållet i `Startup.cs` filen ser ut som följande efter att ha lagt till den kod som krävs:

        public IConfigurationRoot Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            // Add mvc here
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }

På styrenheten, efter `IConfiguration` att ha injicerat `CreateSingleEmailToMultipleRecipients` gränssnittet, `MailHelper` kan vi använda metoden för klassen för att skicka ett enda e-postmeddelande till flera mottagare. Metoden accepterar ytterligare en boolesk parameter med namnet `showAllRecipients`. Den här parametern kan användas för att styra om e-postmottagare ska kunna se varandras e-postadress i avsnittet Till i e-posthuvudet. Exempelkoden för styrenheten ska vara som nedan 

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
    
## <a name="how-to-add-an-attachment"></a>Så här lägger du till en bifogad fil
Bifogade filer kan läggas till i ett meddelande genom att anropa **metoden AddAttachment** och minimalt ange filnamnet och Base64-kodat innehåll som du vill bifoga. Du kan inkludera flera bifogade filer genom att anropa den här metoden en gång för varje fil som du vill bifoga eller med metoden **AddAttachments.** I följande exempel visas att du lägger till en bifogad fil i ett meddelande:

    var banner2 = new Attachment()
    {
        Content = Convert.ToBase64String(raw_content),
        Type = "image/png",
        Filename = "banner2.png",
        Disposition = "inline",
        ContentId = "Banner 2"
    };
    msg.AddAttachment(banner2);

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Så här: Använd e-postinställningar för att aktivera sidfönningar, spårning och analys
SendGrid tillhandahåller ytterligare e-postfunktioner med hjälp av e-postinställningar och spårningsinställningar. Dessa inställningar kan läggas till i ett e-postmeddelande för att aktivera specifika funktioner som klickspårning, Google Analytics, prenumerationsspårning och så vidare. En fullständig lista över appar finns i [Dokumentationen för Inställningar][settings-documentation].

Appar kan tillämpas på **SendGrid-e-postmeddelanden** med metoder som implementerats som en del av klassen **SendGridMessage.** I följande exempel visas sidfoten och klickspårningsfilter:

I följande exempel visas sidfoten och klickspårningsfilter:

### <a name="footer-settings"></a>Sidfotsinställningar
    msg.SetFooterSetting(
                         true,
                         "Some Footer HTML",
                         "<strong>Some Footer Text</strong>");

### <a name="click-tracking"></a>Klicka på spårning
    msg.SetClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Så här använder du ytterligare SendGrid-tjänster
SendGrid erbjuder flera API:er och webhooks som du kan använda för att utnyttja ytterligare funktioner i ditt Azure-program. Mer information finns i [SendGrid API Reference][SendGrid API documentation].

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i SendGrid e-posttjänsten, följ dessa länkar för att lära dig mer.

* SendGrid\# C bibliotek repo: [sendgrid-csharp][sendgrid-csharp]
* SendGrid API-dokumentation:<https://sendgrid.com/docs>

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

[molnbaserad e-posttjänst]: https://sendgrid.com/solutions
[transaktionell e-postleverans]: https://sendgrid.com/use-cases/transactional-email

