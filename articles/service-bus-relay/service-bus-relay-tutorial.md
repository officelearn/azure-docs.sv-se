---
title: Exponera en WCF REST-tjänst för prem för klienter med Azure Relay
description: Den här självstudien beskriver hur du exponerar en lokal WCF REST-tjänst för en extern klient med hjälp av Azure WCF Relay.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 53dfd236-97f1-4778-b376-be91aa14b842
ms.service: service-bus-relay
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 551c8e662669737d9d074a69cb03d6060ab87ad5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76513090"
---
# <a name="tutorial-expose-an-on-premises-wcf-rest-service-to-external-client-by-using-azure-wcf-relay"></a>Självstudiekurs: Exponera en lokal WCF REST-tjänst för extern klient med hjälp av Azure WCF Relay

Den här självstudien beskriver hur du skapar ett WCF Relay-klientprogram och -tjänst med Azure Relay. En liknande självstudiekurs som använder [Service Bus-meddelanden](../service-bus-messaging/service-bus-messaging-overview.md)finns [i Komma igång med servicebussköer](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Genom att arbeta igenom den här självstudien får du en förståelse för stegen för att skapa ett WCF Relay-klient- och tjänstprogram. Precis som sina ursprungliga WCF-motsvarigheter är en tjänst en konstruktion som exponerar en eller flera slutpunkter. Varje slutpunkt exponerar en eller flera tjänståtgärder. Slutpunkten för en tjänst anger en adress där tjänsten ligger, en bindning som innehåller den information som en klient måste använda för att kommunicera med tjänsten och ett kontrakt som definierar de funktioner som tjänsten levererar till sina klienter. Den största skillnaden mellan WCF och WCF Relay är att slutpunkten exponeras i molnet i stället för lokalt på datorn.

När du har gått igenom sekvensen av avsnitt i den här självstudien har du en tjänst som körs. Du har också en klient som kan anropa tjänstens åtgärder. 

Du gör följande uppgifter i den här självstudien:

> [!div class="checklist"]
>
> * Installera förutsättningar för den här självstudien.
> * Skapa ett relay-namnområde.
> * Skapa ett WCF-servicekontrakt.
> * Implementera WCF-kontraktet.
> * Vara värd och kör WCF-tjänsten för att registrera sig med relay-tjänsten.
> * Skapa en WCF-klient för servicekontraktet.
> * Konfigurera WCF-klienten.
> * Implementera WCF-klienten.
> * Kör programmen.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien, finns följande förhandskrav:

* En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* [Visual Studio 2015 eller senare](https://www.visualstudio.com). Exemplen i den här självstudien använder Visual Studio 2019.
* Azure SDK för .NET. Installera det från den [nedladdningssidan för SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Skapa ett relay-namnområde

Det första steget är att skapa ett namnområde och hämta en [SAS-nyckel (signatur för delad åtkomst)](../service-bus-messaging/service-bus-sas.md). Ett namnområde ger en appgräns för varje app som exponeras via Relay-tjänsten. En SAS-nyckel genereras automatiskt av systemet när ett tjänstnamnområde skapas. Kombinationen av tjänstens namnområde och SAS-nyckeln ger referensen för Azure som används för att tillåta åtkomst till ett program.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract"></a>Definiera ett WCF-servicekontrakt

Servicekontraktet anger vilka åtgärder tjänsten stöder. Operationer är webbtjänstmetoder eller funktioner. Kontrakt skapas genom att definiera ett gränssnitt för C++, C# eller Visual Basic. Varje metod i gränssnittet motsvarar en viss tjänsteåtgärd. Attributet [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) måste tillämpas på varje gränssnitt och attributet [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) måste tillämpas på varje åtgärd. Om en metod i ett gränssnitt som har attributet [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) inte har [attributet OperationContractAttribute,](/dotnet/api/system.servicemodel.operationcontractattribute) visas inte den metoden. Koden för dessa arbetsuppgifter visas in exemplet som följer efter proceduren. En större diskussion om kontrakt och tjänster finns i [Designing and Implementing Services](/dotnet/framework/wcf/designing-and-implementing-services).

### <a name="create-a-relay-contract-with-an-interface"></a>Skapa ett reläkontrakt med ett gränssnitt

1. Starta Microsoft Visual Studio som administratör. Om du vill göra det högerklickar du på programikonen Visual Studio och väljer **Kör som administratör**.
1. I Visual Studio väljer du **Skapa ett nytt projekt**.
1. I **Skapa ett nytt projekt**väljer du Console App **(.NET Framework)** för C# och väljer **Nästa**.
1. Namnge projektet *EchoService* och välj **Skapa**.

   ![Skapa en konsolapp][2]

1. Högerklicka på projektet i **Solution Explorer**och välj **Hantera NuGet-paket**. I **NuGet Package Manager**väljer du **Bläddra**och söker sedan efter och väljer **WindowsAzure.ServiceBus**. Välj **Installera**och acceptera användningsvillkoren.

    ![Service Bus-paket][3]

   Det här paketet lägger automatiskt till referenser till `System.ServiceModel`Service Bus-biblioteken och WCF . [System.ServiceModel](/dotnet/api/system.servicemodel) är det namnområde som ger dig programmatisk åtkomst till de grundläggande funktionerna i WCF. Service Bus använder många av WFC:s objekt och attribut för att definiera tjänstekontrakt.

1. Lägg till `using` följande satser högst upp i *Program.cs:*

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Ändra namnet på namnområdet från standardnamnet `EchoService` till `Microsoft.ServiceBus.Samples`.

   > [!IMPORTANT]
   > Den här självstudien `Microsoft.ServiceBus.Samples` använder namnområdet C# som är namnområdet för den kontraktsbaserade hanterade typen som används i konfigurationsfilen i avsnittet [Konfigurera WCF-klientavsnittet.](#configure-the-wcf-client) Du kan ange vilket namnområde du vill när du skapar det här exemplet. Självstudien fungerar dock inte om du inte ändrar namnområdena för kontraktet och tjänsten i enlighet med detta i programkonfigurationsfilen. Namnområdet som anges i *filen App.config* måste vara samma som det namnområde som anges i C#-filerna.
   >

1. Direkt efter `Microsoft.ServiceBus.Samples` namnområdesdeklarationen, men inom namnområdet, `IEchoContract` definiera `ServiceContractAttribute` ett nytt gränssnitt med namnet `https://samples.microsoft.com/ServiceModel/Relay/`och tillämpa attributet på gränssnittet med ett namnområdesvärde i . Klistra in följande kod efter namnområdesdeklarationen:

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    Namnområdesvärdet skiljer sig från det namnområde som du använder under hela intervallet för din kod. Istället används namnområdesvärdet som en unik identifierare för det här kontraktet. Genom att ange namnområdet uttryckligen förhindrar du att det förvalda namnområdesvärdet läggs till i kontraktnamnet.

   > [!NOTE]
   > Namnområdet för tjänstekontraktet innehåller vanligtvis ett namngivningsschema som inkluderar information om versionen. Om du tar med versionsinformation i namnområdet för tjänstekontraktet kan tjänsterna isolera större ändringar genom att definiera ett nytt tjänstkontrakt med ett nytt namnområde och sedan exponera det på en ny slutpunkt. På så sätt kan klienter fortsätta att använda det gamla serviceavtalet utan att behöva uppdateras. Versionsinformation kan bestå av ett datum eller ett build-nummer. Mer information finns i [Versionhantering för tjänster](/dotnet/framework/wcf/service-versioning). För den här självstudien innehåller namngivningsschemat för namnområdet för servicekontrakt inte versionsinformation.
   >

1. I `IEchoContract` gränssnittet deklarerar du en metod `IEchoContract` för den enda åtgärden `OperationContractAttribute` som kontraktet exponerar i gränssnittet och tillämpar attributet på den metod som du vill exponera som en del av det offentliga WCF Relay-kontraktet, enligt följande:

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```

1. Direkt efter gränssnittsdefinitionen `IEchoContract` deklarerar du en kanal som ärver egenskaper från båda `IEchoContract` och även från gränssnittet `IClientChannel`, som visas här:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    En kanal är det WCF-objekt via vilken värden och klienten skickar information till varandra. Senare skriver du kod mot kanalen för att ekoa information mellan de två programmen.

1. Välj **Bygg** > **bygglösning** eller välj Ctrl+Skift+B för att bekräfta hur exakt ditt arbete är hittills.

### <a name="example-of-a-wcf-contract"></a>Exempel på ett WCF-kontrakt

Följande kod visar ett grundläggande gränssnitt som definierar ett WCF Relay-kontrakt.

```csharp
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Nu när gränssnittet har skapats kan du implementera det.

## <a name="implement-the-wcf-contract"></a>Implementera WCF-kontraktet

Skapa en Azure relay kräver att du först skapa kontraktet med hjälp av ett gränssnitt. Mer information om hur du skapar gränssnittet finns i föregående avsnitt. Nästa procedur implementerar gränssnittet. Den här uppgiften innebär `EchoService` att skapa en klass `IEchoContract` med namnet som implementerar det användardefinierade gränssnittet. När du har implementerat gränssnittet konfigurerar du gränssnittet med hjälp av en *App.config-konfigurationsfil.* Konfigurationsfilen innehåller nödvändig information för programmet. Den här informationen innehåller namnet på tjänsten, namnet på kontraktet och vilken typ av protokoll som används för att kommunicera med relätjänsten. Koden som används för dessa uppgifter finns i exemplet som följer proceduren. En mer allmän diskussion om hur du [implementerar](/dotnet/framework/wcf/implementing-service-contracts)ett servicekontrakt finns i Implementera servicekontrakt .

1. Skapa en ny klass med namnet `EchoService` direkt efter definitionen av gränssnittet `IEchoContract`. Klassen `EchoService` implementerar gränssnittet `IEchoContract`.

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    Precis som med andra gränssnittsimplementeringar kan du implementera definitionen i en annan fil. Men i den här självstudiekursen ligger implementeringen i samma fil som gränssnittsdefinitionen och `Main()`-metoden.

1. Tillämpa attributet [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) på gränssnittet `IEchoContract`. Attributet anger namnet på tjänsten och på namnområdet. När du har gjort det, visas klassen `EchoService` på följande sätt:

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. Implementera metoden `Echo` som definierats i gränssnittet `IEchoContract` i klassen `EchoService`.

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. Välj **Bygg** > **bygglösning** eller välj Ctrl+Skift+B.

### <a name="define-the-configuration-for-the-service-host"></a>Definiera konfigurationen för tjänstvärden

Konfigurationsfilen liknar en WCF-konfigurationsfil. Den innehåller tjänstnamnet, slutpunkten och bindningen. Slutpunkten är den plats som Azure Relay exponerar för klienter och värdar att kommunicera med varandra. Bindningen är den typ av protokoll som används för att kommunicera. Den största skillnaden är att den här konfigurerade tjänstslutpunkten refererar till en [NetTcpRelayBinding-bindning,](/dotnet/api/microsoft.servicebus.nettcprelaybinding) som inte ingår i .NET Framework. [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) är en av bindningarna som definieras av tjänsten.

1. Dubbelklicka på **App.config** i **Solution Explorer**för att öppna filen i Visual Studio-redigeraren.
1. I elementet `<appSettings>` ersätter du platshållarna med namnet på ditt namnområde för tjänsten och den SAS-nyckel som du kopierade i ett av de föregående stegen.
1. Lägg till ett `<services>`-element inom taggarna `<system.serviceModel>`. Du kan definiera flera reläprogram i en enda konfigurationsfil. I den här självstudiekursen definieras dock bara en.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. Inne i elementet `<services>` element lägger du till ett `<service>`-element för att definiera namnet på tjänsten.

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. Definiera platsen för slutpunktskontraktet inne i elementet `<service>` och även typen av bindning för slutpunkten.

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    Slutpunkten definierar var klienten ska söka efter värdprogrammet. Senare använder självstudien det här steget för att skapa en URI som helt exponerar värden via Azure Relay. Bindningen förklarar att vi använder TCP som protokoll för att kommunicera med relätjänsten.

1. Välj **Bygg** > **bygglösning** eller välj Ctrl+Skift+B för att bekräfta hur exakt ditt arbete är hittills.

### <a name="example-of-implementation-of-a-service-contract"></a>Exempel på implementering av ett servicekontrakt

Följande kod visar implementeringen av tjänstekontraktet.

```csharp
[ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

Följande kod visar det grundläggande formatet för filen *App.config* som är associerad med tjänstvärden.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-the-wcf-service-to-register-with-the-relay-service"></a>Vara värd och kör WCF-tjänsten för att registrera sig med relätjänsten

I det här steget beskrivs hur du kör en Azure Relay-tjänst.

### <a name="create-the-relay-credentials"></a>Skapa reläautentiseringsuppgifter

1. Skapa två variabler i `Main()` där du ska lagra namnområdet och SAS-nyckeln som läses från konsolfönstret.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    SAS-nyckeln används senare för att komma åt ditt projekt. Namnområdet skickas som en parameter till `CreateServiceUri` för att skapa en URI för tjänsten.

1. Med ett [TransportClientEndpointBehavior-objekt](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior) deklarerar du att du ska använda en SAS-nyckel som autentiseringsuppgifter. Lägg till följande kod direkt efter den som lades till i det senaste steget.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>Skapa en basadress för tjänsten

När koden som du har lagt `Uri` till i föregående avsnitt skapar du en instans för tjänstens basadress. Den här URI anger Service Bus-schemat, namnområdet och sökvägen för tjänstegränssnittet.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

Värdet "sb" är en förkortning för servicebussschemat. Det indikerar att vi använder TCP som protokoll. Det här schemat angavs också tidigare i konfigurationsfilen, när [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) angavs som bindning.

URI är `sb://putServiceNamespaceHere.windows.net/EchoService` för den här självstudiekursen.

### <a name="create-and-configure-the-service-host"></a>Skapa och konfigurera tjänstvärden

1. Arbetar fortfarande `Main()`i , ställer `AutoDetect`in anslutningsläget till .

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    Anslutningsläget beskriver det protokoll som tjänsten använder för att kommunicera med relätjänsten. antingen HTTP eller TCP. Med standardinställningen `AutoDetect`försöker tjänsten ansluta till Azure Relay via TCP om den är tillgänglig och HTTP om TCP inte är tillgänglig. Detta resultat skiljer sig från det protokoll som tjänsten anger för klientkommunikation. Det här protokollet bestäms av den bindning som används. En tjänst kan till exempel använda [bindningen BasicHttpRelayBinding,](/dotnet/api/microsoft.servicebus.basichttprelaybinding) som anger att slutpunkten kommunicerar med klienter via HTTP. Samma tjänst kan `ConnectivityMode.AutoDetect` ange så att tjänsten kommunicerar med Azure Relay via TCP.

1. Skapa tjänstevärden med hjälp av den URI som du skapade tidigare i det här avsnittet.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    Tjänstevärden är det WCF-objekt som instantierar tjänsten. Här skickar du den typ av tjänst som `EchoService` du vill skapa, en typ och även till den adress där du vill exponera tjänsten.

1. Lägg till referenser till [System.ServiceModel.Description](/dotnet/api/system.servicemodel.description) och [Microsoft.ServiceBus.Description](/dotnet/api/microsoft.servicebus.description)högst upp i *Program.cs filen.*

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. När du är tillbaka i `Main()`, konfigurerar du slutpunkten för att ge allmän åtkomst till den.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Det här steget informerar relätjänsten om att din ansökan kan hittas offentligt genom att undersöka Atom-feeden för ditt projekt. Om du `DiscoveryType` `private`ställer in på kan en klient fortfarande komma åt tjänsten. Tjänsten visas dock inte när den `Relay` söker i namnområdet. Klienten måste då i stället känna till sökvägen för slutpunkten på förhand.

1. Använda tjänstautentiseringsuppgifterna på tjänstslutpunkterna som definierats i *filen App.config:*

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Som tidigare nämnts kunde du ha deklarerat flera tjänster och slutpunkter i konfigurationsfilen. Om du hade gjort detta skulle den här koden ha bläddrat igenom hela konfigurationsfilen och ha sökt efter varje slutpunkt som den skulle ha tillämpat på dina autentiseringsuppgifter. För den här självstudien har konfigurationsfilen bara en slutpunkt.

### <a name="open-the-service-host"></a>Öppna tjänstvärden

1. Fortfarande `Main()`i , lägg till följande rad för att öppna tjänsten.

    ```csharp
    host.Open();
    ```

1. Informera användaren om att tjänsten körs och förklara hur tjänsten stängs av.

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Stäng tjänstevärden när du är klar.

    ```csharp
    host.Close();
    ```

1. Välj Ctrl+Skift+B om du vill skapa projektet.

### <a name="example-that-hosts-a-service-in-a-console-application"></a>Exempel som är värd för en tjänst i ett konsolprogram

Den ifyllda servicekoden ska visas på följande sätt. Koden innehåller servicekontraktet och implementeringen från tidigare steg i självstudien och är värd för tjänsten i ett konsolprogram.

```csharp
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         

            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Relay credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }

            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>Skapa en WCF-klient för tjänstekontraktet

Nästa uppgift är att skapa ett klientprogram och definiera det servicekontrakt som du ska implementera senare. De här stegen liknar de steg som används för att skapa en tjänst: definiera ett kontrakt, redigera en *App.config-fil,* använda autentiseringsuppgifter för att ansluta till relätjänsten och så vidare. Den kod som används för dessa arbetsuppgifter visas in exemplet som följer efter proceduren.

1. Skapa ett nytt projekt i den aktuella Visual Studio-lösningen för klienten:

   1. Högerklicka på den aktuella lösningen (inte projektet) i **Solution Explorer**och välj **Lägg till** > **nytt projekt**.
   1. I **Lägg till ett nytt projekt**väljer du Console App **(.NET Framework)** för C#och väljer **Nästa**.
   1. Namnprojekt *EchoClient* och välj **Skapa**.

1. I Solution Explorer **EchoClient** dubbelklickar **du på Program.cs** i Solution **Explorer**för att öppna filen i redigeraren om den inte redan är öppen.
1. Ändra namnet på namnområdet från standardnamnet `EchoClient` till `Microsoft.ServiceBus.Samples`.
1. Installera [Service Bus NuGet-paketet:](https://www.nuget.org/packages/WindowsAzure.ServiceBus)

   1. Högerklicka på **EchoClient** i **Solution Explorer**och välj Hantera **NuGet-paket**.
   1. Välj **Bläddra**och sök sedan efter och välj **WindowsAzure.ServiceBus**. Välj **Installera**och acceptera användningsvillkoren.

      ![Installera servicebusspaket][4]

1. Lägg `using` till en sats för namnområdet [System.ServiceModel](/dotnet/api/system.servicemodel) i *Program.cs* filen.

    ```csharp
    using System.ServiceModel;
    ```

1. Lägga till en definition för tjänstekontraktet i namnområdet, som visas i följande exempel. Den här definitionen är identisk med den definition som används i **serviceprojektet.** Lägg till den här `Microsoft.ServiceBus.Samples` koden högst upp i namnområdet.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Välj Ctrl+Skift+B om du vill skapa klienten.

### <a name="example-of-the-echoclient-project"></a>Exempel på EchoClient-projektet

Följande kod visar aktuell status *för den Program.cs* filen i **EchoClient-projektet.**

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>Konfigurera WCF-klienten

I det här steget skapar du en *App.config-fil* för ett grundläggande klientprogram som har åtkomst till tjänsten som skapats tidigare i den här självstudien. Den här *App.config-filen* definierar kontraktet, bindningen och namnet på slutpunkten. Den kod som används för dessa arbetsuppgifter visas in exemplet som följer efter proceduren.

1. I **Solution Explorer**dubbelklickar du på **App.config** i **Solution** Explorer för att öppna filen i Visual Studio-redigeraren.
1. I elementet `<appSettings>` ersätter du platshållarna med namnet på ditt namnområde för tjänsten och den SAS-nyckel som du kopierade i ett av de föregående stegen.
1. Lägg `system.serviceModel` till ett `<client>` element i elementet.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Den här koden deklarerar att du definierar ett klientprogram i WCF-stil.

1. Definiera namnet, kontraktet och bindningstypen för slutpunkten inom elementet `client`.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Den här koden definierar namnet på slutpunkten. Det definierar också kontraktet som definierats i tjänsten och det faktum att klientprogrammet använder TCP för att kommunicera med Azure Relay. Namnet på slutpunkten används i nästa steg för att länka samman denna slutpunktskonfiguration med URI:n för tjänsten.

1. Välj **Spara fil** > **.**

### <a name="example-of-the-appconfig-file"></a>Exempel på filen App.config

Följande kod visar *filen App.config* för Echo-klienten.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client"></a>Implementera WCF-klienten

I det här avsnittet implementerar du ett grundläggande klientprogram som använder tjänsten som du skapade tidigare i den här självstudien. I likhet med tjänsten gör klienten många av samma åtgärder för att komma åt Azure Relay:

* Ställer in anslutningsläget.
* Skapar den URI som lokaliserar värdtjänsten.
* Definierar säkerhetsautentiseringen.
* Tillämpar autentiseringsuppgifterna på anslutningen.
* Öppnar anslutningen.
* Utför de programspecifika uppgifterna.
* Stänger ned anslutningen.

En av de största skillnaderna är dock att klientprogrammet använder en kanal för att ansluta till relätjänsten. Tjänsten använder ett anrop till **ServiceHost**. Den kod som används för dessa arbetsuppgifter visas in exemplet som följer efter proceduren.

### <a name="implement-a-client-application"></a>Implementera ett klientprogram

1. Ställ in anslutningsläget på `AutoDetect`. Lägg till följande kod inne i `Main()`-metoden för **EchoClient**-programmet.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Definiera variabler för att lagra värdena för tjänstens namnområde och den SAS-nyckel som läses från konsolen.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Skapa URI som definierar platsen för värden i relay-projektet.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Skapa autentiseringsobjektet för slutpunkten för tjänstens namnområde.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Skapa kanalfabriken som läser in konfigurationen som beskrivs i *filen App.config.*

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    En kanalfabriken är ett WCF-objekt som skapar en kanal via vilken tjänsten och klientprogrammet kan kommunicera.

1. Använd autentiseringsuppgifterna.

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Skapa och öppna kanalen till tjänsten.

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Skriv det grundläggande gränssnittet och funktionerna för ekot.

    ```csharp
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Koden använder instansen av kanalobjektet som en proxy för tjänsten.

1. Stäng kanalen och stäng fabriken.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

### <a name="example-code-for-this-tutorial"></a>Exempel på kod för den här självstudien

Den ifyllda koden ska visas på följande sätt. Den här koden visar hur du skapar ett klientprogram, hur du anropar tjänstens åtgärder och hur du stänger klienten när åtgärdsanropet är klart.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;


            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="run-the-applications"></a>Köra programmen

1. Välj Ctrl+Skift+B om du vill skapa lösningen. Den här åtgärden bygger både klientprojektet och serviceprojektet som du skapade i föregående steg.
1. Du måste se till att tjänsteprogrammet körs innan du startar klientprogrammet. Högerklicka på **EchoService-lösningen** i **Solution Explorer**och välj sedan **Egenskaper**.
1. Välj**Startprojekt** **för gemensamma egenskaper** > i **egenskapssidor**och välj sedan **Flera startprojekt.** Kontrollera att **EchoService** visas först i listan.
1. Ställ in rutan **Åtgärd** för både **EchoService**- och **EchoClient**-projektet på **Starta**.

    ![Projektegenskapssidor][5]

1. Välj **Projektberoenden**. Välj **EchoClient**i **Projekt**. För **Beror på**kontrollerar du att **EchoService** är markerat.

    ![Projektberoenden][6]

1. Välj **OK** om du vill stänga **egenskapssidor**.
1. Välj F5 om du vill köra båda projekten.
1. Båda konsolfönstren öppnas och uppmanar dig att ange namnet på namnområdet. Tjänsten måste köras först, så i **EchoService-konsolfönstret** anger du namnområdet och väljer sedan Retur.
1. Därefter uppmanas du att ange DIN SAS-nyckel. Ange SAS-tangenten och välj Retur.

    Här är exempel på vad som kan matas ut från konsolfönstret. Värdena här är bara exempel.

    `Your Service Namespace: myNamespace`

    `Your SAS Key: <SAS key value>`

    Tjänsteprogrammet skriver till konsolfönstret, till adressen som den lyssnar på, som visas i följande exempel.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`

    `Press [Enter] to exit`

1. Ange samma information som du angav tidigare för tjänsteprogrammet i konsolfönstret för **EchoClient**. Ange samma tjänstnamnområde och SAS-nyckelvärden för klientprogrammet.
1. När du har angett dessa värden, öppnar klienten en kanal till tjänsten och du uppmanas att ange lite text. Följ anvisningarna i följande utmatningsexempel för konsolen.

    `Enter text to echo (or [Enter] to exit):`

    Ange text som ska skickas till serviceprogrammet och välj Retur. Texten skickas till tjänsten via tjänsteåtgärden Echo och visas i tjänstekonsolfönstret, som i följande exempelutmatning.

    `Echoing: My sample text`

    Klientprogrammet får returvärdet för `Echo`-åtgärden, som är den ursprungliga texten, och skriver ut denna i sitt konsolfönster. Följande text är exempelutdata från klientkonsolfönstret.

    `Server echoed: My sample text`

1. Du kan fortsätta att skicka textmeddelanden från klienten till tjänsten på detta sätt. När du är klar väljer du Retur i klient- och servicekonsolens fönster för att avsluta båda programmen.

## <a name="next-steps"></a>Nästa steg

Fortsätt till följande självstudie:

> [!div class="nextstepaction"]
>[Göra en lokal WCF REST-tjänst tillgänglig för en klient utanför nätverket](service-bus-relay-rest-tutorial.md)

[2]: ./media/service-bus-relay-tutorial/configure-echoservice-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget-service-bus.png
[4]: ./media/service-bus-relay-tutorial/install-nuget-service-bus-client.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
