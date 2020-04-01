---
title: 'Självstudiekurs: REST-självstudie med Azure Relay'
description: 'Självstudiekurs: Skapa ett Azure Service Bus Relay-värdprogram som exponerar ett REST-baserat gränssnitt.'
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 1312b2db-94c4-4a48-b815-c5deb5b77a6a
ms.service: service-bus-relay
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 229ed2b00582f2c73ce68c47406d68325abda736
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73718830"
---
# <a name="tutorial-azure-wcf-relay-rest-tutorial"></a>Självstudiekurs: Azure WCF Relay REST-självstudiekurs

Den här självstudien beskriver hur du skapar ett Azure Relay-värdprogram som exponerar ett REST-baserat gränssnitt. Med hjälp av REST kan du ge en webbklient, till exempel en webbläsare, åtkomst till API:er för Service Bus via HTTP-förfrågningar.

Självstudien använder programmeringsmodellen För Windows Communication Foundation (WCF) rest för att skapa en REST-tjänst på Azure Relay. Mer information finns i [WCF REST Programming Model](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) och [Designing and Implementing Services](/dotnet/framework/wcf/designing-and-implementing-services).

Du gör följande uppgifter i den här självstudien:

> [!div class="checklist"]
>
> * Installera förutsättningar för den här självstudien.
> * Skapa ett relay-namnområde.
> * Definiera ett REST-baserat WCF-servicekontrakt.
> * Implementera det REST-baserade WCF-kontraktet.
> * Vara värd för och kör REST-baserade WCF-tjänsten.
> * Kör och testa tjänsten.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien, finns följande förhandskrav:

* En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* [Visual Studio 2015 eller senare](https://www.visualstudio.com). Exemplen i den här självstudien använder Visual Studio 2019.
* Azure SDK för .NET. Installera det från den [nedladdningssidan för SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Skapa ett relay-namnområde

För att komma igång med reläfunktionerna i Azure måste du först skapa ett namnområde för tjänsten. Ett namnområde tillhandahåller en omfångscontainer för adressering av Azure-resurser i ditt program. Följ [anvisningarna här](relay-create-namespace-portal.md) för att skapa ett Relay-namnområde.

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>Definiera ett REST-baserat WCF-servicekontrakt som ska användas med Azure Relay

När du skapar en WCF REST-stil tjänst, måste du definiera kontraktet. Kontraktet anger vilka åtgärder som värden stöder. En tjänståtgärd liknar en webbtjänstmetod. Definiera ett kontrakt med ett C++, C# eller Visual Basic-gränssnitt. Varje metod i gränssnittet motsvarar en viss tjänsteåtgärd. Använd attributet [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) på varje gränssnitt och tillämpa attributet [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) på varje åtgärd. 

> [!TIP]
> Om en metod i ett gränssnitt som har [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) inte har [OperationContractAttribute,](/dotnet/api/system.servicemodel.operationcontractattribute)är den metoden inte exponerad. Koden som används för dessa aktiviteter visas i exemplet efter proceduren.

Den primära skillnaden mellan ett WCF-kontrakt och ett REST-liknande kontrakt är tillägget av en egenskap till [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute): [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute). Den här egenskapen gör att du kan mappa en metod i gränssnittet till en metod på andra sidan av gränssnittet. I det här exemplet används [attributet WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) för att länka en metod till `HTTP GET`. Med den här metoden kan Service Bus hämta och tolka kommandon som skickas till gränssnittet korrekt.

### <a name="to-create-a-contract-with-an-interface"></a>Så här skapar du ett kontrakt med ett gränssnitt

1. Starta Microsoft Visual Studio som administratör. Om du vill göra det högerklickar du på programikonen Visual Studio och väljer **Kör som administratör**.
1. I Visual Studio väljer du **Skapa ett nytt projekt**.
1. I **Skapa ett nytt projekt**väljer du Console App **(.NET Framework)** för C# och väljer **Nästa**.
1. Ge projektet namnet *ImageListener*. Använd **standardplatsen**och välj sedan **Skapa**.

   För ett C#-projekt skapar *Program.cs* Visual Studio en Program.cs-fil. Den här klassen innehåller en tom `Main()`-metod, ett krav för att ett konsolapprojekt ska kunna skapas på rätt sätt.

1. Högerklicka på **ImageListener-projektet** i **Solution Explorer**och välj sedan **Hantera NuGet-paket**.
1. Välj **Bläddra**och sök sedan efter och välj **WindowsAzure.ServiceBus**. Välj **Installera**och acceptera användningsvillkoren.

    Det här steget lägger till referenser till Service Bus och *System.ServiceModel.dll*. Det här paketet lägger automatiskt till referenser till `System.ServiceModel`Service Bus-biblioteken och WCF .

1. Lägg uttryckligen till `System.ServiceModel.Web.dll` en referens till projektet. Högerklicka på **Referenser** under projektmappen i **Solution Explorer**och välj Lägg **till referens**.
1. Välj **Ramverket** och ange *System.ServiceModel.Web* i Sök i **Lägg** **till**referens . Markera kryssrutan **System.ServiceModel.Web** och klicka sedan på **OK**.

Gör sedan följande kodändringar i projektet:

1. Lägg till `using` följande satser högst upp i *Program.cs-filen.*

    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    * [System.ServiceModel](/dotnet/api/system.servicemodel) är det namnområde som genom programmering ger åtkomst till grundläggande funktioner i WCF. WCF Relay använder många av WCF:s objekt och attribut för att definiera servicekontrakt. Du använder det här namnområdet i de flesta reläprogram.
    * [System.ServiceModel.Channels](/dotnet/api/system.servicemodel.channels) hjälper till att definiera kanalen, som är det objekt genom vilket du kommunicerar med Azure Relay och klientwebbläsaren.
    * [System.ServiceModel.Web](/dotnet/api/system.servicemodel.web) innehåller de typer som gör att du kan skapa webbaserade program.

1. Byt `ImageListener` namnområdet `Microsoft.ServiceBus.Samples`till .

    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Direkt efter den inledande klammerparentesen för `IImageContract` namnområdesdeklarationen definierar du ett nytt gränssnitt med namnet och tillämpar `ServiceContractAttribute` attributet på gränssnittet med värdet `https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1`. 

    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

    Namnområdesvärdet skiljer sig från det namnområde som du använder under hela intervallet för din kod. Namnområdesvärdet är en unik identifierare för det här kontraktet och bör ha versionsinformation. Mer information finns i [Versionhantering för tjänster](/dotnet/framework/wcf/service-versioning). Genom att ange namnområdet uttryckligen förhindrar du att det förvalda namnområdesvärdet läggs till i kontraktnamnet.

1. I `IImageContract` gränssnittet deklarerar du en metod `IImageContract` för den enda åtgärd som `OperationContract` kontraktet exponerar i gränssnittet och tillämpar attributet på den metod som du vill exponera som en del av avtalet för allmän servicebuss.

    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

1. Lägg `OperationContract` till värdet `WebGet` i attributet.

    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

   Genom `WebGet` att lägga till värdet kan relätjänsten dirigera HTTP GET-begäranden till `GetImage`och översätta returvärdena `GetImage` till ett `HTTP GETRESPONSE` svar. Senare i självstudien använder du en webbläsare för att komma åt den här metoden och för att visa bilden i webbläsaren.

1. Direkt efter definitionen `IImageContract` deklarerar du en kanal som ärver från både `IImageContract`- och `IClientChannel`-gränssnittet.

    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

   En kanal är det WCF-objekt via vilken tjänsten och klienten skickar information till varandra. Senare skapar du kanalen i värdprogrammet. Azure Relay använder sedan den här kanalen för att `GetImage` skicka HTTP GET-begäranden från webbläsaren till implementeringen. Reläet använder också kanalen `GetImage` för att ta `HTTP GETRESPONSE` returvärdet och översätta det till en för klientwebbläsaren.

1. Välj **Bygg** > **bygglösning** för att bekräfta riktigheten i ditt arbete hittills.

### <a name="example-that-defines-a-wcf-relay-contract"></a>Exempel som definierar ett WCF Relay-kontrakt

Följande kod visar ett grundläggande gränssnitt som definierar ett WCF Relay-kontrakt.

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

    [ServiceContract(Name = "IImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
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

## <a name="implement-the-rest-based-wcf-service-contract"></a>Implementera det REST-baserade WCF-servicekontraktet

Om du vill skapa en WCF Relay-tjänst i REST-stil skapar du först kontraktet med hjälp av ett gränssnitt. Nästa steg är att implementera gränssnittet. Den här proceduren innebär `ImageService` att skapa en `IImageContract` klass med namnet som implementerar det användardefinierade gränssnittet. När du har implementerat kontraktet konfigurerar du gränssnittet med hjälp av en *App.config-fil.* Konfigurationsfilen innehåller nödvändig information för programmet. Den här informationen innehåller namnet på tjänsten, namnet på kontraktet och vilken typ av protokoll som används för att kommunicera med relätjänsten. Koden som används för dessa aktiviteter visas i exemplet efter proceduren.

Som med föregående steg, det är liten skillnad mellan att genomföra en REST-stil kontrakt och en WCF Relay kontrakt.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Implementera ett Service Bus-kontrakt i REST-format

1. Skapa en ny klass med namnet `ImageService` direkt efter definitionen av gränssnittet `IImageContract`. Klassen `ImageService` implementerar gränssnittet `IImageContract`.

    ```csharp
    class ImageService : IImageContract
    {
    }
    ```

    Precis som med andra gränssnittsimplementeringar kan du implementera definitionen i en annan fil. Men i den här självstudiekursen visas implementeringen i samma fil som gränssnittsdefinitionen och `Main()`-metoden.

1. Använd [attributet ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) till `IImageService` klassen för att ange att klassen är en implementering av ett WCF-kontrakt.

    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Som tidigare nämnts är det här namnområdet inte ett traditionellt namnområde. Det är en del av WCF-arkitekturen som identifierar kontraktet. Mer information finns i [datakontraktsnamnen](/dotnet/framework/wcf/feature-details/data-contract-names/).

1. Lägg till en *JPG-bild* i projektet. Den här filen är en bild som tjänsten visar i den mottagande webbläsaren.

   1. Högerklicka på projektet och välj **Lägg till**.
   1. Välj sedan **Befintligt objekt**.
   1. Använd **Lägg till befintligt objekt** för att bläddra till en lämplig .jpg och välj sedan Lägg **till**. När du lägger till filen väljer du **Alla filer** i listrutan bredvid **Filnamn**.

   Resten av den här självstudien förutsätter att namnet på bilden är *image.jpg*. Om du har en annan fil måste du byta namn på bilden eller ändra koden för att kompensera.

1. Om du vill vara säker på att den tjänst som körs kan hitta bildfilen högerklickar du på bildfilen i **Lösningsutforskaren** och väljer sedan **Egenskaper**. Ange Kopiera **till utdatakatalog** i **Egenskaper**om du vill **kopiera om den är nyare**.

1. Använd proceduren i [Om du vill skapa ett kontrakt med ett gränssnitt](#to-create-a-contract-with-an-interface) för att lägga till en referens till *system.Drawing.dll-sammansättningen* i projektet.

1. Lägg till `using` följande associerade satser:

    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

1. I `ImageService` klassen lägger du till följande konstruktor som läser in bitmappen och förbereder att skicka den till klientwebbläsaren:

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

1. Direkt efter föregående kod lägger du till följande `GetImage` metod i `ImageService` klassen för att returnera ett HTTP-meddelande som innehåller avbildningen.

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

    Den här `MemoryStream` implementeringen använder för att hämta avbildningen och förbereda den för direktuppspelning till webbläsaren. Den startar strömpositionen vid noll, deklarerar ströminnehållet som en *.jpg*och strömmar informationen.

1. Välj **Bygg** > **bygglösning**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Definiera konfigurationen för att köra webbtjänsten på Service Bus

1. Dubbelklicka på **App.config** i **Solution Explorer**för att öppna filen i Visual Studio-redigeraren.

    *Filen App.config* innehåller tjänstnamnet, slutpunkten och bindningen. Slutpunkten är den plats som Azure Relay exponerar för klienter och värdar att kommunicera med varandra. Bindningen är den typ av protokoll som används för att kommunicera. Den största skillnaden här är att den konfigurerade tjänstslutpunkten refererar till en [WebHttpRelayBinding-bindning.](/dotnet/api/microsoft.servicebus.webhttprelaybinding)

1. XML-elementet `<system.serviceModel>` är ett WCF-element som definierar en eller flera tjänster. Här används den för att definiera tjänstens namn och slutpunkt. Lägg till ett `<system.serviceModel>` `<bindings>` element med `<system.serviceModel>`följande innehåll längst ned i elementet, men finns fortfarande kvar i:

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

    Det här innehållet definierar bindningar som används i programmet. Du kan definiera flera bindningar, men för den här självstudien definierar du bara en.

    Den tidigare koden definierar en WCF Relay [WebHttpRelayBinding bindning](/dotnet/api/microsoft.servicebus.webhttprelaybinding) med `relayClientAuthenticationType` inställd på `None`. Den här inställningen anger att en slutpunkt med den här bindningen inte kräver en klientautentiseringsinformation.

1. Lägg till ett `<services>`-element efter elementet `<bindings>`. På ett liknande sätt som med bindningarna kan du definiera flera tjänster i en enda konfigurationsfil. I den här självstudiekursen kommer du dock bara att definiera en.

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

    Det här innehållet konfigurerar en tjänst `webHttpRelayBinding`som använder den tidigare definierade standardinställningen . Den använder också `sbTokenProvider`standardvärdet , som definieras i nästa steg.

1. Efter `<services>` elementet skapar `<behaviors>` du ett element `SAS_KEY` med följande innehåll och ersätter med SAS-tangenten (Shared Access Signature). Information om hur du hämtar en SAS-nyckel från [Azure-portalen][Azure portal]finns i [Hämta hanteringsautentiseringsuppgifter](service-bus-relay-tutorial.md#get-management-credentials).

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

1. Fortfarande i *App.config* `<appSettings>` , i elementet, ersätta hela anslutningssträngen värde med anslutningssträngen som du tidigare fått från portalen.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```

1. Välj **Bygg** > **bygglösning** för att skapa hela lösningen.

### <a name="example-that-implements-the-rest-based-wcf-service-contract"></a>Exempel som implementerar det REST-baserade WCF-serviceavtalet

Följande kod visar kontrakts- och serviceimplementeringen för en REST-baserad tjänst som körs på Service Bus med hjälp av bindningen. `WebHttpRelayBinding`

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


    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
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

I följande exempel visas filen *App.config* som är associerad med tjänsten.

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
        <!-- Service Bus specific app settings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY>"/>
    </appSettings>
</configuration>
```

## <a name="host-the-rest-based-wcf-service-to-use-azure-relay"></a>Vara värd för den REST-baserade WCF-tjänsten för att använda Azure Relay

I det här avsnittet beskrivs hur du kör en webbtjänst med ett konsolprogram med WCF Relay. En fullständig lista över koden som är skriven i det här avsnittet visas i exemplet efter proceduren.

### <a name="to-create-a-base-address-for-the-service"></a>Så här skapar du en basadress för tjänsten

1. Skapa `Main()` en variabel i funktionsdeklarationen för att lagra namnområdet för projektet. Se till `yourNamespace` att ersätta med namnet på relay-namnområdet som du skapade tidigare.

    ```csharp
    string serviceNamespace = "yourNamespace";
    ```

    Service Bus använder namnet på ditt namnområde för att skapa ett unikt URI.

1. Skapa en `Uri`-instans för tjänstens basadress som baseras på namnområdet.

    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Så här skapar och konfigurerar du webbtjänstevärden

Fortfarande `Main()`i , skapa webbhotell värd, med hjälp av URI-adressen som skapats tidigare i det här avsnittet.
  
```csharp
WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
```

Tjänstevärden är det WCF-objekt som instantierar värdprogrammet. Det här exemplet skickar den typ av värd `ImageService`som du vill skapa, vilket är en , och även den adress där du vill exponera värdprogrammet.

### <a name="to-run-the-web-service-host"></a>Så här kör du webbtjänstevärden

1. Fortfarande `Main()`i , lägg till följande rad för att öppna tjänsten.

    ```csharp
    host.Open();
    ```

    Tjänsten körs nu.

1. Visa ett meddelande som anger att tjänsten körs och hur du stoppar tjänsten.

    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Stäng tjänstevärden när du är klar.

    ```csharp
    host.Close();
    ```

### <a name="example-of-the-service-contract-and-implementation"></a>Exempel på servicekontrakt och implementering

Följande exempel innehåller tjänstekontraktet och implementeringen från föregående steg i självstudiekursen och fungerar som värd för tjänsten i ett konsolprogram. Kompilera följande kod till en körbar med namnet *ImageListener.exe*.

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

    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
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

## <a name="run-and-test-the-service"></a>Kör och testa tjänsten

Gör följande för att köra appen när du har skapat lösningen:

1. Välj F5 eller bläddra till den körbara filplatsen *ImageListener\bin\Debug\ImageListener.exe*för att köra tjänsten. Håll appen igång eftersom den krävs för nästa steg.
1. Kopiera och klistra in adressen från kommandotolken i en webbläsare för att se bilden.
1. När du är klar väljer du Retur i kommandotolksfönstret för att stänga appen.

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat ett program som använder Azure Relay-tjänsten läser du följande artiklar om du vill veta mer:

* [Vad är Azure Relay?](relay-what-is-it.md)
* [Exponera en lokal WCF REST-tjänst för extern klient med hjälp av Azure WCF Relay](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
