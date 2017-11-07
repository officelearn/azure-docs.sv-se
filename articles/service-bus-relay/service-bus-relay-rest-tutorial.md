---
title: "REST-självstudiekurs med hjälp av Azure Relay | Microsoft Docs"
description: "Skapa en enkel värdapp för Azure Service Bus Relay som visar ett REST-baserat gränssnitt."
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 1312b2db-94c4-4a48-b815-c5deb5b77a6a
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2017
ms.author: sethm
ms.openlocfilehash: 7a5a2916514a125d0b7443ced42e5ec600c68857
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="azure-wcf-relay-rest-tutorial"></a>Självstudiekurs för Azure WCF Relay REST

Den här självstudiekursen beskrivs hur du skapar en enkel värdapp för Azure Relay som visar ett REST-baserat gränssnitt. Med hjälp av REST kan du ge en webbklient, till exempel en webbläsare, åtkomst till API:er för Service Bus via HTTP-förfrågningar.

I självstudiekursen används Windows Communication Foundation (WCF) RESTEN programmeringsmodell för att skapa ett REST-tjänst på Azure Relay. Mer information finns i [Programmeringsmodellen WCF REST](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) och [Utforma och implementera tjänster](/dotnet/framework/wcf/designing-and-implementing-services) i WCF-dokumentationen.

## <a name="step-1-create-a-namespace"></a>Steg 1: Skapa ett namnområde

För att komma igång med reläfunktionerna i Azure måste du först skapa ett namnområde för tjänsten. Ett namnområde tillhandahåller en omfångsbehållare för adressering av Azure-resurser i ditt program. Följ [anvisningarna här](relay-create-namespace-portal.md) för att skapa ett Relay-namnområde.

## <a name="step-2-define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>Steg 2: Definiera en REST-baserat WCF-tjänstekontrakt för användning med Azure-relä

När du skapar en tjänst för WCF REST-format, måste du definiera kontraktet. Kontraktet anger vilka åtgärder som värden stöder. En tjänsteåtgärd kan anses vara en webbtjänstemetod. Kontrakt skapas genom att definiera ett gränssnitt för C++, C# eller Visual Basic. Varje metod i gränssnittet motsvarar en viss tjänsteåtgärd. Attributet [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) måste tillämpas på varje gränssnitt och attributet [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) måste tillämpas på varje åtgärd. Om en metod i ett gränssnitt som har [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) inte har [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute), är inte den metoden exponerad. Den kod som används för dessa aktiviteter visas i exemplet som följer efter proceduren.

Den viktigaste skillnaden mellan en WCF-kontrakt och ett kontrakt i REST-format är att lägga till en egenskap för den [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute): [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute). Den här egenskapen gör att du kan mappa en metod i gränssnittet till en metod på andra sidan av gränssnittet. Det här exemplet används den [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) attribut för att länka en metod och HTTP GET. Detta gör att Service Bus korrekt hämta och tolka kommandon som skickas till gränssnittet.

### <a name="to-create-a-contract-with-an-interface"></a>Skapa ett kontrakt med ett gränssnitt

1. Öppna Visual Studio som administratör: Högerklicka på programmet i **Start**-menyn och klicka sedan på **Kör som administratör**.
2. Skapa ett nytt konsolapprojekt. Klicka på **Arkiv**-menyn, välj **Nytt** och sedan **Projekt**. I dialogrutan **Nytt projekt** klickar du på **Visual C#**, väljer mallen **Konsolprogram** och ger den namnet **ImageListener**. Använd standardinställningen **Plats**. Klicka på **OK** för att skapa projektet.
3. Visual Studio skapar en `Program.cs`-fil för ett C#-projekt. Den här klassen innehåller en tom `Main()`-metod, ett krav för att ett konsolapprojekt ska kunna skapas på rätt sätt.
4. Lägg till referenser till Service Bus och **System.ServiceModel.dll** till projektet genom att installera Service Bus NuGet-paketet. Det här paketet lägger automatiskt till referenser till Service Bus-bibliotek, samt även WCF **System.ServiceModel**. Högerklicka på projektet **ImageListener** i Solution Explorer och klicka sedan på **Hantera NuGet-paket**. Klicka på **Bläddra**-fliken och sök sedan efter `Microsoft Azure Service Bus`. Klicka på **Installera** och godkänn användningsvillkoren.
5. Du måste uttryckligen lägga till en referens för **System.ServiceModel.Web.dll** i projektet:
   
    a. Högerklicka på mappen **Referenser** i Solution Explorer, under projektmappen, och klicka sedan på **Lägg till referens**.
   
    b. I dialogrutan **Lägg till referens** klickar du på fliken **Framework** till vänster. Sedan skriver du in **System.ServiceModel.Web** i rutan **Sök**. Markera kryssrutan **System.ServiceModel.Web** och klicka sedan på **OK**.
6. Lägg till följande `using`-uttryck högst upp i filen Program.cs.
   
    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```
   
    [System.ServiceModel](/dotnet/api/system.servicemodel) är det namnområde som genom programmering ger åtkomst till grundläggande funktioner i WCF. Vidarebefordrande WCF använder många av objekt och attribut i WCF för att definiera tjänstekontrakt. Du kan använda det här namnområdet i de flesta relay-appar. På liknande sätt [System.ServiceModel.Channels](/dotnet/api/system.servicemodel.channels) hjälper dig att definiera kanalen, vilket är det objekt som du kommunicera med Azure Relay och klientens webbläsare. Slutligen är det [System.ServiceModel.Web](/dotnet/api/system.servicemodel.web) som innehåller de typer som gör att du kan skapa webbaserade appar.
7. Byt namn på `ImageListener` namnområdet till **Microsoft.ServiceBus.Samples**.
   
    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```
8. Direkt efter den inledande klammerparentesen för namnområdesdeklarationen definierar du ett nytt gränssnitt med namnet **IImageContract** och sedan tillämpar du attributet **ServiceContractAttribute** på gränssnittet med ett värde av `http://samples.microsoft.com/ServiceModel/Relay/`. Namnområdesvärdet skiljer sig från det namnområde som du använder under hela intervallet för din kod. Namnområdesvärdet används som en unik identifierare för det här kontraktet och bör innehålla versionsinformation. Mer information finns i [Versionshantering för tjänster](http://go.microsoft.com/fwlink/?LinkID=180498). Genom att ange namnområdet uttryckligen förhindrar du att det förvalda namnområdesvärdet läggs till i kontraktnamnet.
   
    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```
9. Inne i gränssnittet `IImageContract` deklarerar du en metod för den enkla åtgärd som `IImageContract`-kontraktet exponerar i gränssnittet. Sedan tillämpar du attributet `OperationContractAttribute` på den metod som du vill exponera som en del av det offentliga Service Bus-kontraktet.
   
    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```
10. Lägg till **WebGet**-värdet i attributet **OperationContract**.
    
    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```
    
    Gör så gör den vidarebefordrande tjänsten på vägen HTTP GET-begäranden till `GetImage`, och översätta returvärdena för `GetImage` till en HTTP GETRESPONSE svaret. Lite längre fram i denna självstudiekurs kommer du att använda en webbläsare för att få tillgång till denna metod. Sedan visar du bilden i webbläsaren.
11. Direkt efter definitionen `IImageContract` deklarerar du en kanal som ärver från både `IImageContract`- och `IClientChannel`-gränssnittet.
    
    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```
    
    En kanal är det WCF-objekt via vilken tjänsten och klienten skickar information till varandra. Senare kan skapa du kanalen i din värdapp. Azure Relay sedan använder den här kanalen för att skicka HTTP GET-förfrågningar från webbläsaren till dina **GetImage** implementering. Vidarebefordran använder också kanalen för att ta den **GetImage** returvärde och omvandla det till en HTTP GETRESPONSE för klientens webbläsare.
12. Från menyn **Skapa** klickar du på **Skapa lösning** för att bekräfta att det arbete du utfört hittills är korrekt.

### <a name="example"></a>Exempel
Följande kod visar ett grundläggande gränssnitt som definierar ett vidarebefordrande WCF-kontrakt.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="step-3-implement-a-rest-based-wcf-service-contract-to-use-service-bus"></a>Steg 3: Implementera ett REST-baserat WCF-tjänstekontrakt för användning av Service Bus
Skapa ett REST-format WCF vidarebefordrande tjänsten måste du först skapa det kontrakt som definieras med hjälp av ett gränssnitt. Nästa steg är att implementera gränssnittet. Detta innebär att du skapar en klass som heter **ImageService** som implementerar det användardefinierade **IImageContract**-gränssnittet. Efter att du har implementerat kontraktet, konfigurerar du gränssnittet genom att använda en App.config-fil. Konfigurationsfilen innehåller information som behövs för programmet, till exempel namnet på tjänsten, namnet på kontraktet och vilken typ av protokoll som används för att kommunicera med den vidarebefordrande tjänsten. Den kod som används för dessa arbetsuppgifter visas i exemplet som följer efter proceduren.

Det finns mycket lite skillnad mellan att implementera ett kontrakt i REST-format och ett vidarebefordrande WCF-kontrakt som föregående steg.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Implementera ett Service Bus-kontrakt i REST-format
1. Skapa en ny klass med namnet **ImageService** direkt efter definitionen av gränssnittet **IImageContract**. Klassen **ImageService** implementerar gränssnittet **IImageContract**.
   
    ```csharp
    class ImageService : IImageContract
    {
    }
    ```
    Precis som med andra gränssnittsimplementeringar kan du implementera definitionen i en annan fil. Men i den här självstudiekursen visas implementeringen i samma fil som gränssnittsdefinitionen och `Main()`-metoden.
2. Tillämpa attributet [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) i klassen **IImageService** för att ange att klassen är en implementering av ett WCF-kontrakt.
   
    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```
   
    Som tidigare nämnts är det här namnområdet inte ett traditionellt namnområde. Det är istället en del av WCF-arkitekturen som identifierar kontraktet. Mer information finns i [datakontraktnamn](https://msdn.microsoft.com/library/ms731045.aspx) artikel i WCF-dokumentationen.
3. Lägg till en .jpg-bild i projektet.  
   
    Det här är en bild som tjänsten visar i den mottagande webbläsaren. Högerklicka på ditt projekt och klicka sedan på **Lägg till**. Klicka därefter på **Befintligt objekt**. Använd dialogrutan **Lägg till befintligt objekt** för att bläddra till en lämplig .jpg-bild och klicka sedan på **Lägg till**.
   
    När du lägger till filen, måste du se till att **Alla filer** är markerad i listrutan bredvid fältet **Filnamn:**. I resten av den här självstudiekursen förutsätter vi att namnet på denna bild är ”image.jpg”. Om du har en annan fil, måste du byta namn på bilden eller ändra koden för att kompensera.
4. För att försäkra dig om att den tjänst som körs kan hitta bildfilen kan du högerklicka på bildfilen i **Solution Explorer** och sedan klicka på **Egenskaper**. I rutan **Egenskaper** ställer du in **Kopiera till utdatakatalog** på **Kopiera om nyare**.
5. Lägg till en referens till sammansättningen **System.Drawing.dll** i projektet och lägg även till följande associerade `using`-uttryck.  
   
    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```
6. I klassen **ImageService** lägger du till följande konstruktor som läser in bitmappen och förbereder den för att skicka den till klientens webbläsare.
   
    ```csharp
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";
   
        Image bitmap;
   
        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```
7. Direkt efter föregående kod lägger du till följande **GetImage**-metod i klassen **ImageService** för att returnera ett HTTP-meddelande som innehåller bilden.
   
    ```csharp
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```
   
    Den här implementeringen använder **MemoryStream** för att hämta bilden och förbereda den för strömning till webbläsaren. Den startar strömningspositionen vid noll, deklarerar ströminnehållet som jpeg och strömmar sedan informationen.
8. Klicka på **Skapa lösning** från **Skapa**-menyn.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Definiera konfigurationen för att köra webbtjänsten på Service Bus
1. Dubbelklicka på filen **App.config** i **Solution Explorer** för att öppna den i Visual Studio-redigeraren.
   
    Den **App.config** filen innehåller tjänstnamn, slutpunkten (det vill säga den plats som Azure Relay visar för klienter och värdar att kommunicera med varandra) och bindningen (typ av protokoll som används för att kommunicera). Den största skillnaden är att den konfigurerade tjänstslutpunkten refererar till en [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) bindning.
2. XML-elementet `<system.serviceModel>` är ett WCF-element som definierar en eller flera tjänster. Här används det för att definiera tjänstenamnet och slutpunkten. Längst ned i elementet `<system.serviceModel>` (men fortfarande i `<system.serviceModel>`), lägger du till ett `<bindings>`-element som har följande innehåll. Detta definierar de bindningar som används i appen. Du kan definiera flera bindningar men i den här kursen definierar du bara en.
   
    ```xml
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```
   
    Föregående kod definierar en WCF-relä [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) bindning med **relayClientAuthenticationType** inställd på **ingen**. Den här inställningen anger att en slutpunkt som använder den här bindningen inte kräver autentiseringsuppgifter för klienten.
3. Lägg till ett `<services>`-element efter elementet `<bindings>`. På ett liknande sätt som med bindningarna kan du definiera flera tjänster i en enda konfigurationsfil. I den här självstudiekursen kommer du dock bara att definiera en.
   
    ```xml
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```
   
    I det här steget konfigurerar vi en tjänst som använder den förvalda **webHttpRelayBinding** som definierats tidigare. Den använder också den förvalda **sbTokenProvider**, som definieras i nästa steg.
4. Efter den `<services>` element, skapa en `<behaviors>` element med följande innehåll, Ersätt ”SAS_KEY” med den *signatur för delad åtkomst* nyckeln (SAS) du tidigare fick från den [Azure-portalen][Azure portal].
   
    ```xml
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```
5. Medan du fortfarande är kvar i App.config, i elementet `<appSettings>`, byter du ut hela värdet för anslutningssträngen mot den sträng som du tidigare fick från portalen. 
   
    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```
6. Klicka på **Skapa lösning** för att skapa hela lösningen från menyn **Skapa**.

### <a name="example"></a>Exempel
Följande kod visar kontrakt- och tjänsteimplementeringen för en REST-baserad tjänst som körs på Service Bus med hjälp av bindningen **WebHttpRelayBinding**.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

I följande exempel visas den App.config-fil som är associerad med tjänsten.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app setings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey="YOUR_SAS_KEY"/>
    </appSettings>
</configuration>
```

## <a name="step-4-host-the-rest-based-wcf-service-to-use-azure-relay"></a>Steg 4: Värdbasera REST-baserat WCF-tjänst om du vill använda Azure Relay
Det här steget beskriver hur du kör en webbtjänst som använder ett konsolprogram med WCF Relay. En fullständig lista över den kod som skrivs i det här steget finns i det exempel som följer efter proceduren.

### <a name="to-create-a-base-address-for-the-service"></a>Så här skapar du en basadress för tjänsten
1. I den `Main()` funktionsdeklarationen, skapa en variabel för att lagra namnområdet för ditt projekt. Ersätt `yourNamespace` med namnet på namnområdet Relay som du skapade tidigare.
   
    ```csharp
    string serviceNamespace = "yourNamespace";
    ```
    Service Bus använder namnet på ditt namnområde för att skapa ett unikt URI.
2. Skapa en `Uri`-instans för tjänstens basadress som baseras på namnområdet.
   
    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Så här skapar och konfigurerar du webbtjänstevärden
* Skapa webbtjänstevärden med hjälp av den URI-adress som du skapade tidigare i det här avsnittet.
  
    ```csharp
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    Tjänstevärden är det WCF-objekt som instantierar värdprogrammet. Det här exemplet överför den typ av värd som du vill skapa (en **ImageService**), och även adressen som du vill exponera värdprogrammet på.

### <a name="to-run-the-web-service-host"></a>Så här kör du webbtjänstevärden
1. Öppna tjänsten.
   
    ```csharp
    host.Open();
    ```
    Tjänsten körs nu.
2. Visa ett meddelande som anger att tjänsten körs och hur du stoppar tjänsten.
   
    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```
3. Stäng tjänstevärden när du är klar.
   
    ```csharp
    host.Close();
    ```

## <a name="example"></a>Exempel
Följande exempel innehåller tjänstekontraktet och implementeringen från föregående steg i självstudiekursen och fungerar som värd för tjänsten i ett konsolprogram. Sammanställ följande kod i en körbar fil med namnet ImageListener.exe.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

### <a name="compiling-the-code"></a>Kompilera koden
Gör följande för att köra appen när du har skapat lösningen:

1. Tryck på **F5**, eller bläddra till den körbara filplatsen (ImageListener\bin\Debug\ImageListener.exe) för att köra tjänsten. Fortsätt att köra appen eftersom det är nödvändigt för att kunna utföra nästa steg.
2. Kopiera och klistra in adressen från kommandotolken i en webbläsare för att se bilden.
3. När du är klar trycker du på **Retur** i kommandotolken för att stänga appen.

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat ett program som använder tjänsten Azure Relay finns i följande artiklar för mer information:

* [Azure Service Bus-Arkitekturöversikt](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
* [Översikt över Azure Relay](relay-what-is-it.md)
* [Hur du använder tjänsten WCF relay med .NET](relay-wcf-dotnet-get-started.md)

[Azure portal]: https://portal.azure.com
