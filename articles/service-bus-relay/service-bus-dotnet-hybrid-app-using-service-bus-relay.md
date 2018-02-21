---
title: Azure WCF Relay-hybridprogram lokalt/i molnet (.NET) | Microsoft Docs
description: "Lär dig hur du skapar ett lokalt eller molnbaserat .NET-hybridprogram med hjälp av Azure WCF Relay."
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9ed02f7c-ebfb-4f39-9c97-b7dc15bcb4c1
ms.service: service-bus-relay
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 11/02/2017
ms.author: sethm
ms.openlocfilehash: fe13de5f83fe18a85f8d46d0eee039159e1a60e9
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
# <a name="net-on-premisescloud-hybrid-application-using-azure-wcf-relay"></a>Lokalt eller molnbaserat .NET-hybridprogram med hjälp av Azure WCF Relay

Den här artikeln visar hur du skapar ett hybridprogram i molnet med Microsoft Azure och Visual Studio. Den här självstudiekursen förutsätter att du inte har några tidigare erfarenheter av att använda Azure. På mindre än 30 minuter kommer du att ha ett program färdigt i molnet som använder en rad Azure-resurser.

Du kommer att lära dig:

* Hur du skapar eller anpassar en befintlig webbtjänst för att den ska kunna användas av en webblösning.
* Lär dig hur du använder Azure WCF Relay-tjänsten för att dela data mellan ett Azure-program och en värdbaserad webbtjänst som finns någon annanstans.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Så här hjälper Azure Relay dig med hybridlösningar

Företagslösningar består normalt av en kombination av anpassad kod – som skrivs för att ta itu med nya och unika verksamhetskrav – och befintliga funktioner som kommer från lösningar och system som redan finns på plats.

Lösningsarkitekter har börjat använda molnet för enklare hantering av skalkrav och lägre driftskostnader. När de gör detta upptäcker de att de befintliga tjänstetillgångarna, som de vill utnyttja som byggblock för sina lösningar, ligger innanför företagets brandvägg och därför är svåra att nå för de lösningar som ligger i molnet. Många interna tjänster är inte skapade eller värdbaserade på ett sätt som gör att de enkelt kan exponeras vid företagets nätverksgräns.

[Azure Relay](https://azure.microsoft.com/services/service-bus/) är avsett för användningsfall där befintliga WCF-webbtjänster (Windows Communication Foundation) på ett säkert sätt görs tillgängliga för lösningar som finns utanför företagets perimeter, utan behov av störande ändringar i företagets nätverksinfrastruktur. Sådana relätjänster finns fortfarande i deras befintliga miljö, men de delegerar lyssnandet efter inkommande sessioner och förfrågningar till relätjänsten i molnet. Azure Relay skyddar även tjänsterna mot obehörig åtkomst med hjälp av [signatur för delad åtkomst (SAS)](../service-bus-messaging/service-bus-sas.md)-autentisering.

## <a name="solution-scenario"></a>Lösningsscenario
I den här självstudiekursen kommer du att skapa en ASP.NET-webbplats som gör att du kan se en lista över produkter på sidan för inventarieförteckningar.

![][0]

I självstudiekursen förutsätter vi att du har produktinformation i ett befintligt, lokalt system och använder Azure Relay för att få åtkomst till det systemet. Det här simuleras av en webbtjänst som körs i ett enkelt konsolprogram och backas upp av en minnesintern uppsättning av produkter. Du kommer att kunna köra detta konsolprogram på din dator och distribuera webbrollen till Azure. När du gör detta, kommer du att se hur den webbroll som körs i Azures datacenter verkligen anropar din datorn, även om datorn sannolikt ligger bakom en brandvägg och ett NAT-lager (Network Address Translation).

## <a name="set-up-the-development-environment"></a>Konfigurera utvecklingsmiljön

Innan du kan börja utveckla Azure-program måste du hämta de verktyg som krävs och ställa in din utvecklingsmiljö:

1. Installera Azure SDK för .NET från [hämtningssidan](https://azure.microsoft.com/downloads/) för SDK.
2. Klicka på den version av [Visual Studio](http://www.visualstudio.com) du använder i kolumnen **.NET**. Stegen i den här självstudiekursen använder Visual Studio 2017.
3. När du uppmanas att köra eller spara installationsprogrammet, klickar du på **Kör**.
4. I **Installationsprogram för webbplattform** klickar du på **Installera** och fortsätter med installationen.
5. När installationen är klar har du allt som behövs för att börja utveckla appen. SDK inkluderar verktyg som låter dig utveckla Azure-program i Visual Studio på ett enkelt sätt.

## <a name="create-a-namespace"></a>Skapa ett namnområde

För att komma igång med reläfunktionerna i Azure måste du först skapa ett namnområde för tjänsten. Ett namnområde tillhandahåller en omfångsbehållare för adressering av Azure-resurser i ditt program. Följ [anvisningarna här](relay-create-namespace-portal.md) för att skapa ett Relay-namnområde.

## <a name="create-an-on-premises-server"></a>Skapa en lokal server

Först ska du skapa ett lokalt produktkatalogsystem (ett fingerat sådant). Det kan vara ett ganska enkel system. Du kan se detta som en representation av ett faktiskt, lokalt produktkatalogsystem med en fullständig serviceyta som vi försöker integrera.

Det här projektet är ett konsolprogram för Visual Studio som använder [Azure Service Bus NuGet-paketet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) för att inkludera Service Bus-bibliotek och -konfigurationsinställningar.

### <a name="create-the-project"></a>Skapa projektet

1. Starta Microsoft Visual Studio med administratörsbehörighet. För att göra det högerklickar du på programikonen för Visual Studio och sedan på **Kör som administratör**.
2. I Visual Studio klickar du på **Nytt** i menyn **Arkiv** och sedan på **Projekt**.
3. Klicka på **Konsolprogram (.NET Framework** från **Installerade mallar** under **Visual C#**. I rutan **Namn** anger du namnet **ProductsServer**:

   ![][11]
4. Klicka på **OK** för att skapa **ProductsServer**-projektet.
5. Om du redan har installerat NuGet Package Manager för Visual Studio kan du hoppa över nästa steg. Annars går du till [NuGet][NuGet] och klickar på [Installera NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Följ anvisningarna för att installera NuGet Package Manager och starta sedan om Visual Studio.
6. Högerklicka på projektet **ProductsServer** i Solution Explorer och klicka sedan på **Hantera NuGet-paket**.
7. Klicka på fliken **Bläddra** och sök sedan efter **WindowsAzure.ServiceBus**. Välj paketet **WindowsAzure.ServiceBus**.
8. Klicka på **Installera** och godkänn användningsvillkoren.

   ![][13]

   Observera att alla nödvändiga klientsammansättningar nu är refererade.
8. Lägg till en ny klass för ditt produktkontrakt. Högerklicka på projektet **ProductsServer** i Solution Explorer. Klicka sedan på **Lägg till** och på **Klass**.
9. I rutan **Namn** anger du namnet **ProductsServer.cs**. Klicka sedan på **Lägg till**.
10. I **ProductsContract.cs** ersätter du definitionen för namnområdet med följande kod. Den definierar kontraktet för tjänsten.

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
11. Ersätt definitionen för namnområdet i Program.cs med följande kod. Den lägger till profiltjänsten och värden för den.

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
12. Dubbelklicka på filen **App.config** i Solution Explorer för att öppna den i Visual Studio-redigeraren. Längst ned i elementet `<system.ServiceModel>` (men fortfarande i `<system.ServiceModel>`), lägger du till följande XML-kod. Se till att ersätta *yourServiceNamespace* med namnet på ditt namnområdet och *yourKey* med den SAS-nyckel som du tidigare hämtade från portalen:

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
    Felet som orsakas av ”transportClientEndpointBehavior” är bara en varning, inte ett blockeringsfel för det här exemplet.
    
13. Medan du fortfarande är kvar i App.config, i elementet `<appSettings>`, byter du ut värdet för anslutningssträngen mot den sträng som du tidigare fick från portalen.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```
14. Tryck på **Ctrl+Shift+B**, eller på **Skapa lösning** från menyn **Skapa**, för att skapa programmet och kontrollera att allt du gjort hittills är korrekt.

## <a name="create-an-aspnet-application"></a>Skapa ett ASP.NET-program

I det här avsnittet skapar du ett enkelt ASP.NET-program som visar data som hämtats från din produkttjänst.

### <a name="create-the-project"></a>Skapa projektet

1. Se till att Visual Studio körs med administratörsbehörighet.
2. I Visual Studio klickar du på **Nytt** i menyn **Arkiv** och sedan på **Projekt**.
3. Klicka på **ASP.NET webbapp (.NET Framwork)** från **Installerade mallar** under **Visual C#**. Ge projektet namnet **ProductsPortal**. Klicka sedan på **OK**.

   ![][15]

4. Från listan **ASP.NET-mallar** i dialogrutan **Nytt ASP.NET Web Application** dialogrutan klickar du på **MVC**.

   ![][16]

6. Klicka på knappen **Ändra autentisering**. I dialogrutan **Ändra autentisering** kontrollerar du att **Ingen autentisering** har markerats och klickar sedan på **OK**. För den här självstudiekursen distribuerar du en app som inte kräver någon användarinloggning.

    ![][18]

7. I dialogrutan **Ny ASP.NET Web Application** klickar du på **OK** för att skapa MVC-app.
8. Nu måste du konfigurera Azure-resurserna för en ny webbapp. Följ stegen i avsnittet [Publicera till Azure i den här artikeln](../app-service/app-service-web-get-started-dotnet-framework.md#publish-to-azure). Gå sedan tillbaka till den här självstudien och gå vidare till nästa steg.
10. I Solution Explorer högerklickar du på **Modeller**. Klicka sedan på **Lägg till** och på **Klass**. Ange namnet **Product.cs** i rutan **Namn**. Klicka sedan på **Lägg till**.

    ![][17]

### <a name="modify-the-web-application"></a>Göra ändringar i webbprogrammet

1. Ersätt den befintliga definitionen för namnområdet med följande kod i filen Product.cs i Visual Studio.

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
2. I Solution Explorer expanderar du mappen **Controllers** och sedan dubbelklickar du på filen **HomeController.cs** för att öppna den i Visual Studio.
3. Ersätt den befintliga definitionen för namnområdet med följande kod i **HomeController.cs**.

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
4. Expandera mappen Views\Shared i Solution Explorer och dubbelklicka sedan på **_Layout.cshtml** så att den öppnas i Visual Studio-redigeraren.
5. Byt ut alla förekomster av **My ASP.NET Application** till **Northwind Traders Products**.
6. Ta bort länkarna för **Start**, **Om** och **Kontakt**. Ta bort den markerade koden i följande exempel.

    ![][41]

7. Expandera mappen Views\Home i Solution Explorer och dubbelklicka sedan på **Index.cshtml** så att den öppnas i Visual Studio-redigeraren. Ersätt hela innehållet i filen med följande kod.

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
8. För att kontrollera om det arbete du gjort hittills är korrekt trycker du på **Ctrl + Skift + B** att skapa projektet.

### <a name="run-the-app-locally"></a>Köra appen lokalt

Kör programmet för att kontrollera att det fungerar.

1. Se till att **ProductsPortal** är det aktiva projektet. Högerklicka på projektnamnet på i Solution Explorer och markera **Ställ in som startprojekt**.
2. I Visual Studio trycker du på **F5**.
3. Programmet bör visas och körs i en webbläsare.

   ![][21]

## <a name="put-the-pieces-together"></a>Knyta ihop säcken

Nästa steg är att koppla samman den lokala produktservern med ASP.NET-programmet.

1. Om programmet inte redan är öppet öppnar du det **ProductsPortal**-projekt som du skapade i avsnittet [Skapa ett ASP.NET-program](#create-an-aspnet-application) igen i Visual Studio.
2. Lägg till NuGet-paketet i projektreferenserna på ett liknande sätt som i steget i avsnittet ”Skapa en lokal server”. Högerklicka på projektet **ProductsPortal** i Solution Explorer och klicka sedan på **Hantera NuGet-paket**.
3. Sök efter **WindowsAzure.ServiceBus** och markera posten **WindowsAzure.ServiceBus**. Slutför sedan installationen och stäng den här dialogrutan.
4. Högerklicka på projektet **ProductsPortal** i Solution Explorer och klicka sedan på **Lägg till** och **Befintligt objekt**.
5. Navigera till filen **ProductsContract.cs** från konsolprojektet **ProductsServer**. Klicka för att markera ProductsContract.cs. Klicka på nedåtpilen bredvid **Lägg till** och sedan på **Lägg till som länk**.

   ![][24]

6. Öppna nu filen **HomeController.cs** i Visual Studio-redigeraren och ersätt definitionen för namnområde med följande kod. Se till att ersätta *yourServiceNamespace* med namnet på ditt namnområde för tjänsten och *yourKey* med din SAS-nyckel. Detta aktiverar klienten för att anropa den lokala tjänsten och returnera resultatet av anropet.

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
7. I Solution Explorer högerklickar du på lösningen **ProductsPortal** (se till att du högerklickar på lösningen, inte projektet). Klicka på **Lägg till** och sedan på **Befintligt projekt**.
8. Navigera till projektet **ProductsServer** och dubbelklicka sedan på lösningsfilen **ProductsServer.csproj** för att lägga till den.
9. **ProductsServer** måste köras för att kunna visa data på **ProductsPortal**. Högerklicka på lösningen **ProductsPortal** i Solution Explorer och klicka på **Egenskaper**. Dialogrutan **Egenskapssidor** visas.
10. Klicka på **Startprojekt** på den vänstra sidan. Klicka på **Flera startprojekt** på den högra sidan. Se till att **ProductsServer** och **ProductsPortal** visas, och i den ordningen. Åtgärden för båda ska vara **Starta**.

      ![][25]

11. Medan du fortfarande är kvar i dialogrutan **Egenskaper**, klickar du på **Projektberoenden** på den vänstra sidan.
12. Klicka på **ProductsServer** i listan **Projekt**. Se till att **ProductsPortal** inte är markerat.
13. Klicka på **ProductsPortal** i listan **Projekt**. Se till att **ProductsServer** är markerat.

    ![][26]

14. Klicka på **OK** i dialogrutan **Egenskapssidor**.

## <a name="run-the-project-locally"></a>Köra projektet lokalt

Tryck på **F5** i Visual Studio för att testa programmet lokalt. Den lokala servern (**ProductsServer**) bör starta först och sedan ska programmet **ProductsPortal** starta i ett webbläsarfönster. Den här gången ser du att data för produktinventarielistorna hämtats från det lokala systemet för produkttjänster.

![][10]

Tryck på **Uppdatera** på sidan **ProductsPortal**. Varje gång du uppdaterar sidan kommer du att se att appservern visar ett meddelande när `GetProducts()` från **ProductsServer** anropas.

Stäng båda programmen innan du fortsätter till nästa steg.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Distribuera ProductsPortal-projektet till en Azure-webbapp

Nästa steg är att publicera om klientdelen för **ProductsPortal** som en Azure-webbapp. Gör följande:

1. Högerklicka på lösningen **ProductsPortal** i Solution Explorer och klicka på **Publicera**. Klicka på **Publicera** på **publiceringssidan**.

  > [!NOTE]
  > Du kanske får upp ett felmeddelande i webbläsarfönstret när webbprojektet **ProductsPortal** startas automatiskt efter distributionen. Detta är normalt och beror på att **ProductsServer**-programmet ännu inte körs.
>
>

2. Kopiera den distribuerade webbappens URL eftersom du kommer att behöva URL-adressen i nästa steg. Du kan även hämta denna URL från aktivitetsfönstret Azure App Service i Visual Studio:

  ![][9]

3. Stäng webbläsarfönstret för att stoppa programmet som körs.

### <a name="set-productsportal-as-web-app"></a>Ställa in ProductsPortal som en webbapp

Innan du kör programmet i molnet måste du se till att **ProductsPortal** startas från Visual Studio som en webbapp.

1. Högerklicka på projektet **ProductsPortal** i Visual Studio och klicka sedan på **Egenskaper**.
2. Klicka på **Webb** i den vänstra kolumnen.
3. I avsnittet **Starta åtgärden** klickar du på knappen **Starta URL**. Ange URL-adressen för den webbapp som du distribuerade i de föregående stegen i textrutan, till exempel `http://productsportal1234567890.azurewebsites.net/`.

    ![][27]

4. Klicka på **Spara alla** från menyn **Arkiv** i Visual Studio.
5. Klicka på **Återskapa lösning** från menyn Skapa i Visual Studio.

## <a name="run-the-application"></a>Köra programmet

1. Tryck på F5 för att skapa och köra programmet. Den lokala servern (konsolprogrammet **ProductsServer**) bör starta först och sedan ska programmet **ProductsPortal** starta i ett webbläsarfönster, som visas på följande skärmdump. Återigen ser du att data för produktinventarielistorna hämtats från det lokala systemet för produkttjänster. Dessa data visas sedan i webbappen. Kontrollera URL:en för att se till att **ProductsPortal** körs i molnet, som en Azure-webbapp.

   ![][1]

   > [!IMPORTANT]
   > Konsolprogrammet **ProductsServer** måste köras och det måste kunna skicka data till programmet **ProductsPortal**. Om ett felmeddelande visas i webbläsaren ska du vänta i några sekunder för att **ProductsServer** ska läsas in. Den visar sedan följande meddelande. Tryck sedan på **Uppdatera** i webbläsaren.
   >
   >

   ![][37]
2. När du har gått tillbaka till webbläsaren trycker du på **Uppdatera** på sidan **ProductsPortal**. Varje gång du uppdaterar sidan kommer du att se att appservern visar ett meddelande när `GetProducts()` från **ProductsServer** anropas.

    ![][38]

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Relay finns i följande resurser:  

* [Vad är Azure Relay?](relay-what-is-it.md)  
* [Så här använder du Azure Relay](relay-wcf-dotnet-get-started.md)  

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[NuGet]: http://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
