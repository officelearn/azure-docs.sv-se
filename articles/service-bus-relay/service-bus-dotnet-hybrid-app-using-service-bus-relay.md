---
title: Azure Windows Communication Foundation (WCF) Relay hybrid lokalt/molnprogram (.NET) | Microsoft-dokument
description: Lär dig att göra en lokal WCF-tjänst tillgänglig för en webbapp i molnet med Azure Relay
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 9ed02f7c-ebfb-4f39-9c97-b7dc15bcb4c1
ms.service: service-bus-relay
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: spelluru
ms.openlocfilehash: b86d535e4cbc275b3ee777d7c70146f7711c502c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212956"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>Göra en lokal WCF-tjänst tillgänglig för en webbapp i molnet med Azure Relay

Den här artikeln visar hur du skapar ett hybridprogram i molnet med Microsoft Azure och Visual Studio. Du skapar ett program som använder flera Azure-resurser i molnet. Den här självstudien hjälper dig att lära dig:

* Hur du skapar eller anpassar en befintlig webbtjänst för att den ska kunna användas av en webblösning.
* Så här använder du Tjänsten Azure Windows Communication Foundation (WCF) Relay för att dela data mellan ett Azure-program och en webbtjänst som finns någon annanstans.

Du gör följande uppgifter i den här självstudien:

> [!div class="checklist"]
>
> * Installera förutsättningar för den här självstudien.
> * Granska scenariot.
> * Skapa ett namnområde.
> * Skapa en lokal server.
> * Skapa ett ASP .NET-program.
> * Kör appen lokalt.
> * Distribuera webbappen till Azure.
> * Kör appen på Azure.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien, finns följande förhandskrav:

* En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* [Visual Studio 2015 eller senare](https://www.visualstudio.com). Exemplen i den här självstudien använder Visual Studio 2019.
* Azure SDK för .NET. Installera det från den [nedladdningssidan för SDK](https://azure.microsoft.com/downloads/).

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Så här hjälper Azure Relay dig med hybridlösningar

Affärslösningar består vanligtvis av en kombination av anpassad kod och befintliga funktioner. Anpassad kod tar itu med nya och unika affärskrav. Lösningar och system som redan finns ger befintlig funktionalitet.

Lösningsarkitekter har börjat använda molnet för enklare hantering av skalkrav och lägre driftskostnader. På så sätt upptäcker de att befintliga tjänsttillgångar som de vill använda som byggstenar för sina lösningar finns i företagets brandvägg och är utom räckhåll för molnlösningen. Många interna tjänster är inte byggda eller värd på ett sätt som de lätt kan exponeras på företagets nätverkskant.

[Azure Relay](https://azure.microsoft.com/services/service-bus/) tar befintliga WCF-webbtjänster och gör dessa tjänster säkert tillgängliga för lösningar som ligger utanför företagets omkrets utan att kräva påträngande ändringar i företagets nätverksinfrastruktur. Sådana relätjänster finns fortfarande i deras befintliga miljö, men de delegerar lyssnandet efter inkommande sessioner och förfrågningar till relätjänsten i molnet. Azure Relay skyddar även tjänsterna mot obehörig åtkomst med hjälp av [signatur för delad åtkomst (SAS)](../service-bus-messaging/service-bus-sas.md)-autentisering.

## <a name="review-the-scenario"></a>Granska scenariot

I den här självstudiekursen kommer du att skapa en ASP.NET-webbplats som gör att du kan se en lista över produkter på sidan för inventarieförteckningar.

![Scenario][0]

I självstudiekursen förutsätter vi att du har produktinformation i ett befintligt, lokalt system och använder Azure Relay för att få åtkomst till det systemet. En webbtjänst som körs i ett enkelt konsolprogram simulerar den här situationen. Den innehåller en i minnet uppsättning produkter. Du kan köra det här konsolprogrammet på din egen dator och distribuera webbrollen till Azure. På så sätt får du se hur webbrollen som körs i Azure-datacenter anropar till din dator. Detta samtal händer även om din dator kommer nästan säkert att ligga bakom minst en brandvägg och ett nätverk adress översättning (NAT) lager.

## <a name="set-up-the-development-environment"></a>Konfigurera utvecklingsmiljön

Innan du kan börja utveckla Azure-program måste du hämta de verktyg som krävs och ställa in din utvecklingsmiljö:

1. Installera Azure SDK för .NET från [hämtningssidan](https://azure.microsoft.com/downloads/) för SDK.
1. Välj den version av Visual [Studio](https://www.visualstudio.com) du använder i kolumnen **.NET.** Den här självstudien använder Visual Studio 2019.
1. När du uppmanas att köra eller spara installationsprogrammet väljer du **Kör**.
1. I dialogrutan **Webbplattformsinstallation väljer** du **Installera** och fortsätter med installationen.

När installationen är klar har du allt som krävs för att starta appen. SDK inkluderar verktyg som låter dig utveckla Azure-program i Visual Studio på ett enkelt sätt.

## <a name="create-a-namespace"></a>Skapa ett namnområde

Det första steget är att skapa ett namnområde och hämta en [SAS-nyckel (signatur för delad åtkomst)](../service-bus-messaging/service-bus-sas.md). Ett namnområde ger en appgräns för varje app som exponeras via Relay-tjänsten. En SAS-nyckel genereras automatiskt av systemet när ett tjänstnamnområde skapas. Kombinationen av tjänstens namnområde och SAS-nyckeln ger referensen för Azure som används för att tillåta åtkomst till ett program.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Skapa en lokal server

Först skapar du ett simulerat lokalt produktkatalogsystem.  Det här projektet är ett konsolprogram för Visual Studio som använder [Azure Service Bus NuGet-paketet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) för att inkludera Service Bus-bibliotek och -konfigurationsinställningar. <a name="create-the-project"></a>

1. Starta Microsoft Visual Studio som administratör. Om du vill göra det högerklickar du på programikonen Visual Studio och väljer **Kör som administratör**.
1. I Visual Studio väljer du **Skapa ett nytt projekt**.
1. I **Skapa ett nytt projekt**väljer du Console App **(.NET Framework)** för C# och väljer **Nästa**.
1. Namnge projektet *ProductsServer* och välj **Skapa**.

   ![Konfigurera det nya projektet][11]

1. Högerklicka på **Projektet ProductsServer** i **Solution Explorer**och välj sedan **Hantera NuGet-paket**.
1. Välj **Bläddra**och sök sedan efter och välj **WindowsAzure.ServiceBus**. Välj **Installera**och acceptera användningsvillkoren.

   ![Välj NuGet-paket][13]

   Alla nödvändiga klientsammansättningar nu är refererade.

1. Lägg till en ny klass för ditt produktkontrakt. Högerklicka på **Projektet ProductsServer** i **Solution Explorer**och välj **Lägg till** > **klass**.
1. Ange namnet ProductsContract.cs *i* **Namn**och välj **Lägg till**.

Gör följande kodändringar i din lösning:

1. I *ProductsContract.cs* ersätter du definitionen för namnområdet med följande kod. Den definierar kontraktet för tjänsten.

    ```csharp
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;

        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }

        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();

        }

        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

1. I *Program.cs*ersätter du namnområdesdefinitionen med följande kod, som lägger till profiltjänsten och värden för den.

    ```csharp
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;

        // Implement the IProducts interface.
        class ProductsService : IProducts
        {

            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };

            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }

        }

        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();

                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();

                sh.Close();
            }
        }
    }
    ```

1. Dubbelklicka på **App.config** i **Solution Explorer**för att öppna filen i Visual Studio-redigeraren. Lägg till följande `<system.ServiceModel>` XML-kod `<system.ServiceModel>`längst ned i elementet, men fortfarande inom. Var noga `yourServiceNamespace` med att ersätta med namnet `yourKey` på ditt namnområde och med SAS-nyckeln som du hämtade tidigare från portalen:

    ```xml
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```

    > [!NOTE]
    > Felet som `transportClientEndpointBehavior` orsakas av är bara en varning och är inte ett blockeringsproblem för det här exemplet.

1. Fortfarande i *App.config* `<appSettings>` , i elementet, ersätta anslutningssträngen värde med anslutning strängen som du tidigare fått från portalen.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

1. Välj Ctrl+Skift+B eller välj **Bygg** > **bygglösning** för att skapa programmet och kontrollera hur exakt ditt arbete är hittills.

## <a name="create-an-aspnet-application"></a>Skapa ett ASP.NET-program

I det här avsnittet skapar du ett enkelt ASP.NET-program som visar data som hämtats från din produkttjänst.

### <a name="create-the-project"></a>Skapa projektet

1. Kontrollera att Visual Studio körs som administratör.
1. I Visual Studio väljer du **Skapa ett nytt projekt**.
1. I **Skapa ett nytt projekt**väljer du ASP.NET **webbprogram (.NET Framework)** för C# och väljer **Nästa**.
1. Namnge projektet *ProductsPortal* och välj **Skapa**.
1. I **Skapa ett nytt ASP.NET webbprogram**väljer du **MVC** och väljer **Ändra** under **Autentisering**.

   ![Välj ASP.NET-webbprogram][16]

1. I **Ändra autentisering**väljer du **Ingen autentisering** och väljer sedan **OK**. För den här självstudien distribuerar du en app som inte behöver en användare för att logga in.

    ![Ange autentisering][18]

1. Tillbaka i **Skapa ett nytt ASP.NET webbprogram**väljer du **Skapa** för att skapa MVC-appen.
1. Konfigurera Azure-resurser för en ny webbapp. Följ stegen i [Publicera din webbapp](../app-service/app-service-web-get-started-dotnet-framework.md#launch-the-publish-wizard). Gå sedan tillbaka till den här självstudien och fortsätt till nästa steg.
1. Högerklicka på **Modeller** i **Solution Explorer**och välj sedan **Lägg till** > **klass**.
1. Ge klassen *namnet Product.cs*och välj sedan Lägg **till**.

    ![Skapa produktmodell][17]

### <a name="modify-the-web-application"></a>Göra ändringar i webbprogrammet

1. I *den Product.cs* filen i Visual Studio ersätter du den befintliga namnområdesdefinitionen med följande kod:

   ```csharp
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
       public class Product
       {
           public string Id { get; set; }
           public string Name { get; set; }
           public string Quantity { get; set; }
       }
    }
    ```

1. Expandera Styrenheter i **Solution Explorer**och dubbelklicka sedan på **HomeController.cs** för att öppna filen i Visual Studio. **Controllers**
1. Ersätt den befintliga definitionen för namnområdet med följande kod i *HomeController.cs*:

    ```csharp
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;

        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

1. Expandera **Delade vyer** > i **Solution Explorer**och dubbelklicka sedan på **_Layout.cshtml** för att öppna filen i Visual Studio-redigeraren.**Shared**
1. Ändra alla förekomster av `My ASP.NET Application` till *Northwind Traders Produkter*.
1. Ta `Home`bort `About`, `Contact` och länkar. Ta bort den markerade koden i följande exempel.

    ![Ta bort de genererade listobjekten][41]

1. Expandera **Vyer** > **hem**i **Solution Explorer**och dubbelklicka sedan på **Index.cshtml** för att öppna filen i Visual Studio-redigeraren. Ersätt hela innehållet i filen med följande kod:

   ```html
   @model IEnumerable<ProductsWeb.Models.Product>

   @{
            ViewBag.Title = "Index";
   }

   <h2>Prod Inventory</h2>

   <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.Name)
                 </th>
                 <th></th>
                 <th>
                     @Html.DisplayNameFor(model => model.Quantity)
                 </th>
             </tr>

   @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.Name)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Quantity)
                 </td>
             </tr>
   }

   </table>
   ```

1. Om du vill kontrollera hur exakt ditt arbete är hittills kan du välja Ctrl+Skift+B för att skapa projektet.

### <a name="run-the-app-locally"></a>Köra appen lokalt

Kör programmet för att kontrollera att det fungerar.

1. Se till att **ProductsPortal** är det aktiva projektet. Högerklicka på projektnamnet i **Solution Explorer** och välj Ange **som startprojekt**.
1. Välj F5 i Visual Studio.

Programmet bör visas och körs i en webbläsare.

![Webbprogram][21]

## <a name="put-the-pieces-together"></a>Knyta ihop säcken

Nästa steg är att koppla samman den lokala produktservern med ASP.NET-programmet.

1. Om den inte redan är öppen öppnar du projektet **ProductsPortal** som du skapade i avsnittet [Skapa ett ASP.NET-program](#create-an-aspnet-application) i Visual Studio.
1. I likhet med steget i avsnittet Skapa en lokal server lägger du till [NuGet-paketet](#create-an-on-premises-server) i projektreferenserna. Högerklicka på **Projektet ProductsPortal** i **Solution Explorer**och välj sedan **Hantera NuGet-paket**.
1. Sök efter *WindowsAzure.ServiceBus* och markera posten **WindowsAzure.ServiceBus**. Slutför sedan installationen och stäng den här dialogrutan.
1. Högerklicka på **Projektet ProductsPortal** i **Solution Explorer**och välj sedan **Lägg till** > **befintligt objekt**.
1. Navigera till filen *ProductsContract.cs* från konsolprojektet **ProductsServer**. Markera *ProductsContract.cs*. Markera nedpilen bredvid **Lägg till**och välj sedan Lägg till **som länk**.

   ![Lägg till som en länk][24]

1. Öppna nu filen *HomeController.cs* i Visual Studio-redigeraren och ersätt definitionen för namnområde med följande kod. Var noga `yourServiceNamespace` med att ersätta med namnet `yourKey` på tjänstens namnområde och med SAS-tangenten. Med den här koden kan klienten ringa den lokala tjänsten och returnera resultatet av samtalet.

   ```csharp
   namespace ProductsWeb.Controllers
   {
       using System.Linq;
       using System.ServiceModel;
       using System.Web.Mvc;
       using Microsoft.ServiceBus;
       using Models;
       using ProductsServer;

       public class HomeController : Controller
       {
           // Declare the channel factory.
           static ChannelFactory<IProductsChannel> channelFactory;

           static HomeController()
           {
               // Create shared access signature token credentials for authentication.
               channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                   "sb://yourServiceNamespace.servicebus.windows.net/products");
               channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                   TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                       "RootManageSharedAccessKey", "yourKey") });
           }

           public ActionResult Index()
           {
               using (IProductsChannel channel = channelFactory.CreateChannel())
               {
                   // Return a view of the products inventory.
                   return this.View(from prod in channel.GetProducts()
                                    select
                                        new Product { Id = prod.Id, Name = prod.Name,
                                            Quantity = prod.Quantity });
               }
           }
       }
   }
   ```

1. Högerklicka på **ProductsPortal-lösningen** i **Solution Explorer.** Se till att högerklicka på lösningen, inte projektet. Välj **Lägg till** > **befintligt projekt**.
1. Navigera till projektet **ProductsServer** och dubbelklicka sedan på lösningsfilen *ProductsServer.csproj* för att lägga till den.
1. **ProductsServer** måste köras för att visa data på **ProductsPortal**. Högerklicka på **ProductsPortal-lösningen** i **Solution Explorer**och välj **Egenskaper** för att visa **egenskapssidor**.
1. Välj **Common Properties** > **Startup Project** och välj **Flera startprojekt**. Se till att **ProductsServer** och **ProductsPortal** visas i den ordningen och att **åtgärden** för båda är **Start**.

      ![Flera startprojekt][25]

1. Välj **Vanliga egenskaper** > **Projektberoenden** på vänster sida.
1. För **projekt**väljer du **ProductsPortal**. Se till att **ProductsServer** är markerat.

    ![Projektberoenden][26]

1. För **projekt**väljer du **ProductsServer**. Kontrollera att **ProductsPortal** inte är markerat och välj sedan **OK** för att spara ändringarna.

## <a name="run-the-project-locally"></a>Köra projektet lokalt

Om du vill testa programmet lokalt väljer du F5 i Visual Studio. Den lokala servern, **ProductsServer**, ska starta först, sedan ska **Programmet ProductsPortal** starta i ett webbläsarfönster. Den här gången ser du att data för produktinventarielistorna hämtats från det lokala systemet för produkttjänster.

![Webbprogram][10]

Välj **Uppdatera** på sidan **ProductsPortal.** Varje gång du uppdaterar sidan kommer du att se att appservern visar ett meddelande när `GetProducts()` från **ProductsServer** anropas.

Stäng båda programmen innan du fortsätter till nästa avsnitt.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Distribuera ProductsPortal-projektet till en Azure-webbapp

Nästa steg är att publicera om **ProductsPortal** Azure Web App ProductsPortal-klientdelen:

1. Högerklicka på **Projektet ProductsPortal** i **Solution Explorer**och välj **Publicera**. På sidan **Publicera** väljer du **Publicera**.

   > [!NOTE]
   > Du kanske får upp ett felmeddelande i webbläsarfönstret när webbprojektet **ProductsPortal** startas automatiskt efter distributionen. Detta är normalt och beror på att **ProductsServer**-programmet ännu inte körs.
   >

1. Kopiera URL:en för den distribuerade webbappen. Du behöver webbadressen senare. Du kan också hämta den här URL:en från fönstret Aktivitet i **Azure App Service** i Visual Studio:

   ![Webbadressen för den distribuerade appen][9]

1. Stäng webbläsarfönstret för att stoppa programmet som körs.

<a name="set-productsportal-as-web-app"></a>Innan du kör programmet i molnet måste du se till att **ProductsPortal** startas inifrån Visual Studio som en webbapp.

1. Högerklicka på **Projektet ProductsPortal** i Visual Studio och välj **Egenskaper**.
1. Välj **Webb**. Under **Startåtgärd**väljer du **Start-URL.** Ange URL:en för din tidigare distribuerade `https://productsportal20190906122808.azurewebsites.net/`webbapp i det här exemplet .

    ![Start-URL][27]

1. Välj **Spara fil** > **.**
1. Välj **Bygg** > **ombyggnadslösning**.

## <a name="run-the-application"></a>Köra appen

Välj F5 om du vill skapa och köra programmet. Den lokala servern, som är **ProductsServer-konsolprogrammet,** ska starta först, sedan ska Programmet **ProductsPortal** starta i ett webbläsarfönster, som visas här:

   ![Köra webbappen i Azure][1]

Produktlagret visar data som hämtats från den lokala produkttjänsten och visar dessa data i webbappen. Kontrollera URL:en för att se till att **ProductsPortal** körs i molnet, som en Azure-webbapp.

   > [!IMPORTANT]
   > Konsolprogrammet **ProductsServer** måste köras och det måste kunna skicka data till programmet **ProductsPortal**. Om ett fel visas i webbläsaren väntar du några sekunder till innan **ProductsServer** läser in och visar följande meddelande och uppdaterar sedan webbläsaren.
   >

Uppdatera **sidan ProductsPortal** i webbläsaren. Varje gång du uppdaterar sidan kommer du att se att appservern visar ett meddelande när `GetProducts()` från **ProductsServer** anropas.

![Uppdaterade utdata][38]

## <a name="next-steps"></a>Nästa steg

Fortsätt till följande självstudie:

> [!div class="nextstepaction"]
>[Göra en lokal WCF-tjänst tillgänglig för en WCF-klient utanför nätverket](service-bus-relay-tutorial.md)

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app.png
[NuGet]: https://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/configure-productsserver.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/install-nuget-service-bus-productsserver.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/choose-web-application-template.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-class-productsportal.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/change-authentication.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/web-publish-activity.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally-no-content.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-existing-item-link.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/launch-app-as-web-app.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
