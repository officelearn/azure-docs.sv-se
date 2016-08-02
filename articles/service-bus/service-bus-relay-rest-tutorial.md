<properties
    pageTitle="Självstudiekurs om att använda den vidarebefordrande meddelandetjänsten i Service Bus REST | Microsoft Azure"
    description="Skapa en enkel värdapp för Service Bus Relay som visar ett REST-baserat gränssnitt."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="06/03/2016"
    ms.author="sethm" />

# Självstudiekurs i Service Bus REST

I den här självstudiekursen beskriver vi hur du skapar en enkel värdapp för Service Bus som visar ett REST-baserat gränssnitt. Med hjälp av REST kan du ge en webbklient, till exempel en webbläsare, åtkomst till API:er för Service Bus via HTTP-förfrågningar.

Den här kursen använder programmeringsmodellen Windows Communication Foundation (WCF) REST för att skapa en REST-tjänst på Service Bus. Mer information finns i [Programmeringsmodellen WCF REST](https://msdn.microsoft.com/library/bb412169.aspx) och [Utforma och implementera tjänster](https://msdn.microsoft.com/library/ms729746.aspx) i WCF-dokumentationen.

## Steg 1: Skapa ett namnområde för tjänsten

Det första steget är att skapa ett namnområde och hämta en SAS-nyckel (signatur för delad åtkomst). Ett namnområde ger en appgräns för varje app som exponeras via Service Bus. SAS-nyckeln genereras automatiskt av systemet när ett namnområde för tjänsten har skapats. Kombinationen av tjänstens namnområde och SAS-nyckeln ger referensen för Service Bus som används för att tillåta åtkomst till ett program.

1. Du skapar ett namnområde för tjänsten i den [klassiska Azure-portalen][]. På den vänstra sidan klickar du på **Service Bus** och sedan på **Skapa**. Skriv ett namn för namnområdet och klicka på kryssmarkeringen.

2. Klicka på namnet på namnområdet som du skapade i föregående steg i den [klassiska Azure-portalen][] huvudfönster.

3. Klicka på fliken **Konfigurera**.

4. I avsnittet för **nyckelgenerator för delad åtkomst** skriver du ned den **primärnyckel** som är associerad med principen **RootManageSharedAccessKey**. Du kan även kopiera den till Urklipp. Du kommer att använda det här värdet senare under självstudiekursen.

## Steg 2: Definiera ett REST-baserat WCF-tjänstekontrakt som ska användas med Service Bus

Precis som med övriga Service Bus-tjänster måste du definiera kontraktet när du skapar en tjänst i REST-stil. Kontraktet anger vilka åtgärder som värden stöder. En tjänsteåtgärd kan anses vara en webbtjänstemetod. Kontrakt skapas genom att definiera ett gränssnitt för C++, C# eller Visual Basic. Varje metod i gränssnittet motsvarar en viss tjänsteåtgärd. Attributet [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) måste tillämpas på varje gränssnitt och attributet [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) måste tillämpas på varje åtgärd. Om en metod i ett gränssnitt som har [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) inte har [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), är inte den metoden exponerad. Den kod som används för dessa aktiviteter visas i exemplet som följer efter proceduren.

Den främsta skillnaden mellan ett grundläggande Service Bus-kontrakt och ett kontrakt i REST-format, är att en egenskap läggs till i [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx): [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx). Den här egenskapen gör att du kan mappa en metod i gränssnittet till en metod på andra sidan av gränssnittet. I det här fallet kommer vi att använda [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx) för att länka en metod och hämta HTTP GET. Detta gör att Service Bus kan hämta och tolka kommandon som skickas till gränssnittet på ett korrekt sätt.

### Skapa ett Service Bus-kontrakt med ett gränssnitt

1. Öppna Visual Studio som administratör: Högerklicka på programmet i **Start**-menyn och klicka sedan på **Kör som administratör**.

2. Skapa ett nytt konsolapprojekt. Klicka på **Arkiv**-menyn, välj **Nytt** och sedan **Projekt**. I dialogrutan **Nytt projekt** klickar du på **Visual C#**, väljer mallen **Konsolprogram** och ger den namnet **ImageListener**. Använd standardinställningen **Plats**. Klicka på **OK** för att skapa projektet.

3. Visual Studio skapar en `Program.cs`-fil för ett C#-projekt. Den här klassen innehåller en tom `Main()`-metod, ett krav för att ett konsolapprojekt ska kunna skapas på rätt sätt.

4. Lägg till referenser till Service Bus och **System.ServiceModel.dll** till projektet genom att installera Service Bus NuGet-paketet. Det här paketet lägger automatiskt till referenser till Service Bus-bibliotek, samt även WCF **System.ServiceModel**. Högerklicka på projektet **ImageListener** i Solution Explorer och klicka sedan på **Hantera NuGet-paket**. Klicka på **Bläddra**-fliken och sök sedan efter `Microsoft Azure Service Bus`. Klicka på **Installera** och godkänn användningsvillkoren.

5. Du måste uttryckligen lägga till en referens för **System.ServiceModel.Web.dll** i projektet:

    a. Högerklicka på mappen **Referenser** i Solution Explorer, under projektmappen, och klicka sedan på **Lägg till referens**.

    b. I dialogrutan **Lägg till referens** klickar du på fliken **Framework** till vänster. Sedan skriver du in **System.ServiceModel.Web** i rutan **Sök**. Markera kryssrutan **System.ServiceModel.Web** och klicka sedan på **OK**.

6. Lägg till följande `using`-uttryck högst upp i filen Program.cs.

    ```
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) är det namnområde som genom programmering ger åtkomst till grundläggande funktioner i WCF. Service Bus använder många av WFC:s objekt och attribut för att definiera tjänstekontrakt. Du använder det här namnområdet i de flesta Service Bus Relay-appar. På ett liknande sätt hjälper [System.ServiceModel.Channels](https://msdn.microsoft.com/library/system.servicemodel.channels.aspx) till att definiera kanalen, vilket är det objekt som du använder för att kommunicera med Service Bus och klientens webbläsare. Slutligen är det [System.ServiceModel.Web](https://msdn.microsoft.com/library/system.servicemodel.web.aspx) som innehåller de typer som gör att du kan skapa webbaserade appar.

7. Byt namn på `ImageListener` namnområdet till **Microsoft.ServiceBus.Samples**.

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

8. Direkt efter den inledande klammerparentesen för namnområdesdeklarationen definierar du ett nytt gränssnitt med namnet **IImageContract** och sedan tillämpar du attributet **ServiceContractAttribute** på gränssnittet med ett värde av `http://samples.microsoft.com/ServiceModel/Relay/`. Namnområdesvärdet skiljer sig från det namnområde som du använder under hela intervallet för din kod. Namnområdesvärdet används som en unik identifierare för det här kontraktet och bör innehålla versionsinformation. Mer information finns i [Versionshantering för tjänster](http://go.microsoft.com/fwlink/?LinkID=180498). Genom att ange namnområdet uttryckligen förhindrar du att det förvalda namnområdesvärdet läggs till i kontraktnamnet.

    ```
    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

9. Inne i gränssnittet `IImageContract` deklarerar du en metod för den enkla åtgärd som `IImageContract`-kontraktet exponerar i gränssnittet. Sedan tillämpar du attributet `OperationContractAttribute` på den metod som du vill exponera som en del av det offentliga Service Bus-kontraktet.

    ```
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

10. Lägg till **WebGet**-värdet i attributet **OperationContract**.

    ```
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

    Om du gör detta kan Service Bus dirigera HTTP GET-förfrågningarna till `GetImage`, och översätta returvärdena för `GetImage` till ett svar av typen HTTP GETRESPONSE. Lite längre fram i denna självstudiekurs kommer du att använda en webbläsare för att få tillgång till denna metod. Sedan visar du bilden i webbläsaren.

11. Direkt efter definitionen `IImageContract` deklarerar du en kanal som ärver från både `IImageContract`- och `IClientChannel`-gränssnittet.

    ```
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

    En kanal är det WCF-objekt via vilken tjänsten och klienten skickar information till varandra. Lite längre fram kommer du att skapa kanalen i din värdapp. Service Bus använder sedan den här kanalen för att skicka HTTP GET-förfrågningar från webbläsaren till din **GetImage**-implementering. Service Bus använder också kanalen för att ta returvärdet för **GetImage** och omvandla det till en HTTP GETRESPONSE för klientens webbläsare.

12. Från menyn **Skapa** klickar du på **Skapa lösning** för att bekräfta att det arbete du utfört hittills är korrekt.

### Exempel

Följande kod visar ett grundläggande gränssnitt som definierar ett Service Bus-kontrakt.

```
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

## Steg 3: Implementera ett REST-baserat WCF-tjänstekontrakt för användning av Service Bus

För att kunna skapa en Service Bus-tjänst i REST-format, måste du först skapa det kontrakt som definieras med hjälp av ett gränssnitt. Nästa steg är att implementera gränssnittet. Detta innebär att du skapar en klass som heter **ImageService** som implementerar det användardefinierade **IImageContract**-gränssnittet. Efter att du har implementerat kontraktet, konfigurerar du gränssnittet genom att använda en App.config-fil. Konfigurationsfilen innehåller information som appen behöver, till exempel namnet på tjänsten, namnet på kontraktet och vilken typ av protokoll som används för att kommunicera med Service Bus. Den kod som används för dessa arbetsuppgifter visas i exemplet som följer efter proceduren.

I likhet med föregående steg är det mycket lite skillnad mellan att implementera ett kontrakt i REST-format och ett grundläggande Service Bus-kontrakt.

### Implementera ett Service Bus-kontrakt i REST-format

1. Skapa en ny klass med namnet **ImageService** direkt efter definitionen av gränssnittet **IImageContract**. Klassen **ImageService** implementerar gränssnittet **IImageContract**.

    ```
    class ImageService : IImageContract
    {
    }
    ```
    Precis som med andra gränssnittsimplementeringar kan du implementera definitionen i en annan fil. Men i den här självstudiekursen visas implementeringen i samma fil som gränssnittsdefinitionen och `Main()`-metoden.

2. Tillämpa attributet [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) i klassen **IImageService** för att ange att klassen är en implementering av ett WCF-kontrakt.

    ```
    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Som tidigare nämnts är det här namnområdet inte ett traditionellt namnområde. Det är istället en del av WCF-arkitekturen som identifierar kontraktet. Mer information finns i [Datakontraktnamn](https://msdn.microsoft.com/library/ms731045.aspx) i WCF-dokumentationen.

3. Lägg till en .jpg-bild i projektet.  

    Det här är en bild som tjänsten visar i den mottagande webbläsaren. Högerklicka på ditt projekt och klicka sedan på **Lägg till**. Klicka därefter på **Befintligt objekt**. Använd dialogrutan **Lägg till befintligt objekt** för att bläddra till en lämplig .jpg-bild och klicka sedan på **Lägg till**.

    När du lägger till filen, måste du se till att **Alla filer** är markerad i listrutan bredvid fältet **Filnamn:**. I resten av den här självstudiekursen förutsätter vi att namnet på denna bild är ”image.jpg”. Om du har en annan fil måste du byta namn på bilden, eller ändra koden för att kompensera för detta.

4. För att försäkra dig om att den tjänst som körs kan hitta bildfilen kan du högerklicka på bildfilen i **Solution Explorer** och sedan klicka på **Egenskaper**. I rutan **Egenskaper** ställer du in **Kopiera till utdatakatalog** på **Kopiera om nyare**.

5. Lägg till en referens till sammansättningen **System.Drawing.dll** i projektet och lägg även till följande associerade `using`-uttryck.  

    ```
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

6. I klassen **ImageService** lägger du till följande konstruktor som läser in bitmappen och förbereder den för att skicka den till klientens webbläsare.

    ```
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

    ```
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

### Definiera konfigurationen för att köra webbtjänsten på Service Bus

1. Dubbelklicka på filen **App.config** i **Solution Explorer** för att öppna den i Visual Studio-redigeraren.

    Filen **App.config** liknar en WCF-konfigurationsfil och innehåller namnet på tjänsten, slutpunkten (det vill säga, den plats som Service Bus visar för klienter och värdar för att kommunicera med varandra) och bindningen (typ av protokoll som används för att kommunicera). Den största skillnaden här är att den konfigurerade tjänstslutpunkten refererar till en [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx)-bindning och denna är inte en del av .NET Framework.

2. XML-elementet `<system.serviceModel>` är ett WCF-element som definierar en eller flera tjänster. Här används det för att definiera tjänstenamnet och slutpunkten. Längst ned i elementet `<system.serviceModel>` (men fortfarande i `<system.serviceModel>`), lägger du till ett `<bindings>`-element som har följande innehåll. Detta definierar de bindningar som används i appen. Du kan definiera flera bindningar men i den här kursen definierar du bara en.

    ```
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    I det här steget definierar vi en [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx)-bindning för Service Bus med **relayClientAuthenticationType** inställd på **Ingen**. Den här inställningen anger att en slutpunkt som använder den här bindningen inte kräver autentiseringsuppgifter för klienten.

3. Lägg till ett `<services>`-element efter elementet `<bindings>`. På ett liknande sätt som med bindningarna kan du definiera flera tjänster i en enda konfigurationsfil. I den här självstudiekursen kommer du dock bara att definiera en.

    ```
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

4. Efter elementet `<services>` skapar du ett `<behaviors>`-element med följande innehåll. Ersätt ”SAS_KEY” med *signatur för delad åtkomst*-nyckeln (SAS) som du fick från den [klassiska Azure-portalen][] i steg 1.

    ```
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
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

    ```
    <appSettings>
    <!-- Service Bus specific app settings for messaging connections -->
    <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

6. Klicka på **Skapa lösning** för att skapa hela lösningen från menyn **Skapa**.

### Exempel

Följande kod visar kontrakt- och tjänsteimplementeringen för en REST-baserad tjänst som körs på Service Bus med hjälp av bindningen **WebHttpRelayBinding**.

```
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

```
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
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="[SAS_KEY]" />
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
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
</configuration>
```

## Steg 4: Värdbasera den REST-baserade WCF-tjänsten för att använda Service Bus

I det här steget beskriver vi hur du kör en webbtjänst genom att använda ett konsolprogram på Service Bus. En fullständig lista över den kod som skrivs i det här steget finns i det exempel som följer efter proceduren.

### Så här skapar du en basadress för tjänsten

1. Skapa en variabel för att lagra namnområdet för ditt Service Bus-projekt i funktionsdeklarationen `Main()`.  Se till att du ersätter `yourNamespace` med namnet på namnområdet för tjänsten som du skapade tidigare i denna kurs.

    ```
    string serviceNamespace = "yourNamespace";
    ```
    Service Bus använder namnet på ditt namnområde för att skapa ett unikt URI.

2. Skapa en `Uri`-instans för tjänstens basadress som baseras på namnområdet.

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### Så här skapar och konfigurerar du webbtjänstevärden

- Skapa webbtjänstevärden med hjälp av den URI-adress som du skapade tidigare i det här avsnittet.

    ```
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    Tjänstevärden är det WCF-objekt som instantierar värdprogrammet. Det här exemplet överför den typ av värd som du vill skapa (en **ImageService**), och även adressen som du vill exponera värdprogrammet på.

### Så här kör du webbtjänstevärden

1. Öppna tjänsten.

    ```
    host.Open();
    ```
    Tjänsten körs nu.

2. Visa ett meddelande som anger att tjänsten körs och hur du stoppar tjänsten.

    ```
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

3. Stäng tjänstevärden när du är klar.

    ```
    host.Close();
    ```

## Exempel

Följande exempel innehåller tjänstekontraktet och implementeringen från föregående steg i självstudiekursen och fungerar som värd för tjänsten i ett konsolprogram. Sammanställ följande kod i en körbar fil med namnet ImageListener.exe.

```
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

### Kompilera koden

Gör följande för att köra appen när du har skapat lösningen:

1. Tryck på **F5**, eller bläddra till den körbara filplatsen (ImageListener\bin\Debug\ImageListener.exe) för att köra tjänsten. Fortsätt att köra appen eftersom det är nödvändigt för att kunna utföra nästa steg.

2. Kopiera och klistra in adressen från kommandotolken i en webbläsare för att se bilden.

3. När du är klar trycker du på **Retur** i kommandotolken för att stänga appen.

## Nästa steg

Nu när du har skapat en app som använder tjänsten Service Bus Relay, kan du gå till följande artiklar för att läsa mer om den vidarebefordrande meddelandetjänsten.

- [Översikt över arkitekturen i Azure Service Bus](service-bus-fundamentals-hybrid-solutions.md#relays)

- [Så här använder du tjänsten Azure Service Bus Relay](service-bus-dotnet-how-to-use-relay.md)

[klassiska Azure-portalen]: http://manage.windowsazure.com


<!--HONumber=Jun16_HO2-->


