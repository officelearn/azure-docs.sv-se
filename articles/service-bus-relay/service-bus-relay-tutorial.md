---
title: Exponera en lokal WCF REST-tjänst för extern klient med hjälp av Azure WCF Relay | Microsoft Docs
description: Skapa ett klient- och -program med hjälp av WCF Relay.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 53dfd236-97f1-4778-b376-be91aa14b842
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 6927788fa79c567222a199064f5b375546ecf9ad
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615484"
---
# <a name="expose-an-on-premises-wcf-rest-service-to-external-client-by-using-azure-wcf-relay"></a>Exponera en lokal WCF REST-tjänst för extern klient med hjälp av Azure WCF Relay

Den här självstudien beskrivs hur du skapar en enkel WCF Relay-klient och en med hjälp av Azure Relay-tjänsten. En liknande självstudiekurs som använder [Service Bus-meddelanden](../service-bus-messaging/service-bus-messaging-overview.md), se [Kom igång med Service Bus-köer](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Gå igenom den här självstudien får du en förståelse för de steg som krävs för att skapa WCF Relay-klienten och tjänsten för program. En tjänst är en konstruktion som Exponerar en eller flera slutpunkter tur Exponerar en eller flera åtgärder som deras ursprungliga WCF-motsvarigheter. Slutpunkten för en tjänst anger en adress där tjänsten ligger, en bindning som innehåller den information som en klient måste använda för att kommunicera med tjänsten och ett kontrakt som definierar de funktioner som tjänsten levererar till sina klienter. Den största skillnaden mellan WCF- och WCF Relay är att slutpunkten exponeras i molnet istället för lokalt på datorn.

När du har gått igenom alla teman i den här självstudiekursen, kommer du att ha en tjänst om är redo att köras samt en klient som kan anropa tjänstens funktionsåtgärder. I det första avsnittet beskriver vi hur du skapar ett konto. Nästa steg beskriver hur du definierar en tjänst som använder ett kontrakt, hur du implementerar tjänsten och hur du konfigurerar tjänsten i kod. Här beskrivs också hur du hyser in och kör tjänsten. Tjänsten som skapas är egenvärdsbaserad (self-hosted) och klienten och tjänsten körs på samma dator. Du kan konfigurera tjänsten genom att antingen använda koden eller en konfigurationsfil.

I de tre sista stegen beskriver vi hur du skapar ett klientprogram, konfigurerar detta och skapar och använder en klient som har åtkomst till värdens funktioner.

Du kan utföra följande steg i den här självstudien:

> [!div class="checklist"]
> * Skapa ett Relay-namnområde.
> * Skapa en WCF-tjänstekontrakt
> * Implementera WC-kontraktet
> * Hantera och köra WCF-tjänst för att registrera med Relay-tjänsten
> * Skapa en WCF-klient för tjänstekontraktet
> * Konfigurera WCF-klienten
> * Implementera WCF-klienten
> * Köra programmen. 

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien, finns följande förhandskrav:

- En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- [Visual Studio 2015 eller senare](http://www.visualstudio.com). I exemplen i den här självstudiekursen används Visual Studio 2017.
- Azure SDK för .NET. Installera det från den [hämtningssidan för SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Skapa ett Relay-namnområde
Det första steget är att skapa ett namnområde och få en [signatur för delad åtkomst (SAS)](../service-bus-messaging/service-bus-sas.md) nyckel. Ett namnområde ger en programgräns för varje program som exponeras via den vidarebefordrande tjänsten. SAS-nyckeln genereras automatiskt av systemet när ett namnområde för tjänsten har skapats. Kombinationen av namnområde för tjänsten och SAS-nyckeln ger referensen för Azure för att autentisera åtkomst till ett program.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract"></a>Definiera ett WCF-tjänstekontrakt
Tjänstekontraktet anger vilka åtgärder (webbserviceterminologin för metoder eller funktioner) tjänsten stöder. Kontrakt skapas genom att definiera ett gränssnitt för C++, C# eller Visual Basic. Varje metod i gränssnittet motsvarar en viss tjänsteåtgärd. Attributet [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) måste tillämpas på varje gränssnitt och attributet [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) måste tillämpas på varje åtgärd. Om en metod i ett gränssnitt som har attributet [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) inte har attributet [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), är inte den metoden exponerad. Koden för dessa arbetsuppgifter visas in exemplet som följer efter proceduren. En mer utförlig beskrivning av kontrakt och tjänster finns i [Utforma och implementera tjänster](https://msdn.microsoft.com/library/ms729746.aspx) i WCF-dokumentationen.

### <a name="create-a-relay-contract-with-an-interface"></a>Skapa ett relay-kontrakt med ett gränssnitt

1. Öppna Visual Studio som administratör genom att högerklicka på programmet i **Start**-menyn och välja **Kör som administratör**.
2. Skapa ett nytt konsolappsrojekt. Klicka på **Arkiv**-menyn, välj **Nytt** och klicka sedan på **Projekt**. Klicka på **Visual C#** i dialogrutan **Nytt projekt** (om **Visual C#** inte visas, tittar du under **Andra språk**). Klicka på den **Konsolapp (.NET Framework)** mall och ge den namnet **EchoService**. Klicka på **OK** för att skapa projektet.

    ![Skapa en konsolapp][2]

3. Installera Service Bus NuGet-paketet. Det här paketet lägger automatiskt till referenser till Service Bus-bibliotek, samt även WCF **System.ServiceModel**. [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) är det namnområde som ger dig programmatisk åtkomst till de grundläggande funktionerna i WCF. Service Bus använder många av WFC:s objekt och attribut för att definiera tjänstekontrakt.

    Högerklicka på projektet i Solution Explorer och klicka sedan på **hantera NuGet-paket...** . Klicka på fliken Bläddra och sök sedan efter **WindowsAzure.ServiceBus**. Kontrollera att projektnamnet är markerat i rutan **Versioner**. Klicka på **Installera** och godkänn användningsvillkoren.

    ![Service Bus-paket][3]
4. Dubbelklicka på filen Program.cs i Solution Explorer för att öppna den i redigeraren, om den inte redan är öppen.
5. Lägg till följande using-uttryck högst upp i filen:

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```
6. Ändra namnet för namnområdet från dess standardnamn **EchoService** till **Microsoft.ServiceBus.Samples**.

   > [!IMPORTANT]
   > Den här självstudien använder C#-namnområdet **Microsoft.ServiceBus.Samples**, vilket är namnområdet för kontrakt-baserad hanterad typ som används i konfigurationsfilen i den [konfigurerar WCF-klienten](#configure-the-wcf-client) steg. Du kan ange vilken namnområden du vill när du skapar det här exemplet. Men självstudiekursen kommer inte att fungera om du inte sedan ändrar kontraktets namnområden och tjänster därefter. Det här görs i programmets konfigurationsfil. Det namnområde som anges i filen App.config måste vara samma som det namnområde som angavs i C#-filerna.
   >
   >
7. Direkt efter den `Microsoft.ServiceBus.Samples` namnområdesdeklarationen men inom namnområdet, definierar du ett nytt gränssnitt med namnet `IEchoContract` och tillämpa den `ServiceContractAttribute` attributet på gränssnittet med ett namnområdesvärde på `http://samples.microsoft.com/ServiceModel/Relay/`. Namnområdesvärdet skiljer sig från det namnområde som du använder under hela intervallet för din kod. Istället används namnområdesvärdet som en unik identifierare för det här kontraktet. Genom att ange namnområdet uttryckligen förhindrar du att det förvalda namnområdesvärdet läggs till i kontraktnamnet. Klistra in följande kod efter namnområdesdeklarationen:

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

   > [!NOTE]
   > Namnområdet för tjänstekontraktet innehåller vanligtvis ett namngivningsschema som inkluderar information om versionen. Om du tar med versionsinformation i namnområdet för tjänstekontraktet kan tjänsterna isolera större ändringar genom att definiera ett nytt tjänstkontrakt med ett nytt namnområde och sedan exponera det på en ny slutpunkt. På så sätt kan klienter fortsätta att använda det gamla tjänstkontraktet utan att behöva uppdateras. Versionsinformation kan bestå av ett datum eller ett build-nummer. Mer information finns i [Versionhantering för tjänster](https://go.microsoft.com/fwlink/?LinkID=180498). I just den här självstudiekursen innehåller namngivningsschemat för tjänstekontraktets namnområde inte någon information om versionerna.
   >
   >
8. I den `IEchoContract` gränssnitt, deklarerar du en metod för den enda åtgärden i `IEchoContract` kontraktet exponerar i gränssnittet och tillämpa den `OperationContractAttribute` attributet den metod som du vill exponera som en del av det offentliga WCF Relay-kontraktet, enligt följande:

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```
9. Direkt efter gränssnittsdefinitionen `IEchoContract` deklarerar du en kanal som ärver egenskaper från båda `IEchoContract` och även från gränssnittet `IClientChannel`, som visas här:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    En kanal är det WCF-objekt via vilken värden och klienten skickar information till varandra. Lite längre fram ska du skriva kod mot kanalen för att skapa ett eko av information mellan de två programmen.
10. Från menyn **Skapa** klickar du på **Skapa lösning** eller trycker på **Ctrl + Skift + B** för att bekräfta att det arbete du gjort hittills är korrekt.

### <a name="example"></a>Exempel

Följande kod visar ett grundläggande gränssnitt som definierar ett WCF Relay-kontrakt.

```csharp
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
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

## <a name="implement-the-wcf-contract"></a>Implementera WC-kontraktet

Skapa en Azure relay kräver att du först skapa det kontrakt som definieras med hjälp av ett gränssnitt. Mer information om hur du skapar gränssnittet finns i det förra steget. Nästa steg är att implementera gränssnittet. Detta innebär att du skapar en klass som heter `EchoService` och som implementerar det användardefinierade gränssnittet `IEchoContract`. Efter att du har implementerat gränssnittet, konfigurerar du det genom att använda konfigurationsfilen App.config. Konfigurationsfilen innehåller information som behövs för programmet, till exempel namnet på tjänsten, namnet på kontraktet och vilken typ av protokoll som används för att kommunicera med den vidarebefordrande tjänsten. Den kod som används för dessa arbetsuppgifter visas i exemplet som följer efter proceduren. En mer allmän diskussion om hur du implementerar ett tjänstekontrakt finns i [Implementera tjänstekontrakt](https://msdn.microsoft.com/library/ms733764.aspx) i WCF-dokumentationen.

1. Skapa en ny klass med namnet `EchoService` direkt efter definitionen av gränssnittet `IEchoContract`. Klassen `EchoService` implementerar gränssnittet `IEchoContract`.

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    Precis som med andra gränssnittsimplementeringar kan du implementera definitionen i en annan fil. Men i den här självstudiekursen ligger implementeringen i samma fil som gränssnittsdefinitionen och `Main`-metoden.
2. Tillämpa attributet [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) på gränssnittet `IEchoContract`. Attributet anger namnet på tjänsten och på namnområdet. När du har gjort det, visas klassen `EchoService` på följande sätt:

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```
3. Implementera metoden `Echo` som definierats i gränssnittet `IEchoContract` i klassen `EchoService`.

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```
4. Klicka på **Skapa** och sedan på **Skapa lösning** för att bekräfta att det arbete du utfört är korrekt.

### <a name="define-the-configuration-for-the-service-host"></a>Definiera konfigurationen för tjänstevärden

1. Konfigurationsfilen liknar till stora delar en WCF-konfigurationsfil. Den innehåller namnet på tjänsten, slutpunkten (det vill säga den plats som Azure Relay visar för klienter och värdar att kommunicera med varandra) och bindningen (typ av protokoll som används för att kommunicera). Den största skillnaden är att den här konfigurerade tjänstslutpunkten refererar till en [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding)-bindning och denna är inte en del av .NET Framework. [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) är en av de bindningar som definieras av tjänsten.
2. Dubbelklicka på filen App.config i **Solution Explorer** för att öppna den i Visual Studio-redigeraren.
3. I elementet `<appSettings>` ersätter du platshållarna med namnet på ditt namnområde för tjänsten och den SAS-nyckel som du kopierade i ett av de föregående stegen.
4. Lägg till ett `<services>`-element inom taggarna `<system.serviceModel>`. Du kan definiera flera relay-program i en enda konfigurationsfil. I den här självstudiekursen definieras dock bara en.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```
5. Inne i elementet `<services>` element lägger du till ett `<service>`-element för att definiera namnet på tjänsten.

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```
6. Definiera platsen för slutpunktskontraktet inne i elementet `<service>` och även typen av bindning för slutpunkten.

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    Slutpunkten definierar var klienten ska söka efter värdprogrammet. I självstudiekursen används senare, det här steget för att skapa en URI som exponerar värden via Azure Relay helt. Bindningen deklarerar att vi använder TCP som protokoll för kommunikation med relay-tjänsten.
7. Från menyn **Skapa** klickar du på **Skapa lösning** för att bekräfta att det arbete du utfört är korrekt.

### <a name="example"></a>Exempel

Följande kod visar implementeringen av tjänstekontraktet.

```csharp
[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

Följande kod visar det grundläggande formatet för den App.config-fil som är associerad med tjänstevärden.

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

## <a name="host-and-run-the-wcf-service-to-register-with-the-relay-service"></a>Hantera och köra WCF-tjänst för att registrera med relay-tjänsten

Det här steget beskriver hur du kör ett Azure Relay-tjänsten.

### <a name="create-the-relay-credentials"></a>Skapa relay-autentiseringsuppgifter

1. Skapa två variabler i `Main()` där du ska lagra namnområdet och SAS-nyckeln som läses från konsolfönstret.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    SAS-nyckel ska användas senare för att få åtkomst till ditt projekt. Namnområdet skickas som en parameter till `CreateServiceUri` för att skapa en URI för tjänsten.
2. Med hjälp av ett objekt av typen [TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior) anger du att du kommer att använda en SAS-nyckel som autentiseringstyp. Lägg till följande kod direkt efter den som lades till i det senaste steget.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>Skapa en basadress för tjänsten

Efter den du lade till i det sista steget, skapa en `Uri` -instans för basadress för tjänsten. Den här URI anger Service Bus-schemat, namnområdet och sökvägen för tjänstegränssnittet.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

"sb" är en förkortning för Service Bus-schemat och anger att vi använder TCP som protokoll. Detta har även tidigare angetts i konfigurationsfilen när [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) angavs som bindning.

URI är `sb://putServiceNamespaceHere.windows.net/EchoService` för den här självstudiekursen.

### <a name="create-and-configure-the-service-host"></a>Skapa och konfigurera tjänstevärden

1. Ställ in anslutningsläget på `AutoDetect`.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    Anslutningsläget beskriver det protokoll som tjänsten använder för att kommunicera med den vidarebefordrande tjänsten; antingen HTTP eller TCP. Genom att använda standardinställningen `AutoDetect`, försöker tjänsten ansluta till Azure Relay via TCP, om den är tillgänglig och HTTP om TCP inte är tillgänglig. Observera att detta skiljer sig från det protokoll som tjänsten anger för klientkommunikation. Det här protokollet bestäms av den bindning som används. En tjänst kan till exempel använda den [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx) bindning som anger att dess slutpunkt kommunicerar med klienterna via HTTP. Att samma tjänst skulle kunna ange **ConnectivityMode.AutoDetect** så att tjänsten kommunicerar med Azure Relay via TCP.
2. Skapa tjänstevärden med hjälp av den URI som du skapade tidigare i det här avsnittet.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    Tjänstevärden är det WCF-objekt som instantierar tjänsten. Här kan du skicka över den typ av tjänst som du vill skapa (en `EchoService`-typ) samt även den adress som du vill exponera tjänsten på.
3. Lägg till referenser i [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) och [Microsoft.ServiceBus.Description](/dotnet/api/microsoft.servicebus.description) högst upp i filen Program.cs.

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```
4. När du är tillbaka i `Main()`, konfigurerar du slutpunkten för att ge allmän åtkomst till den.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Det här steget meddelar den vidarebefordrande tjänsten som ditt program kan hittas offentligt genom att undersöka ATOM-flödet för projektet. Om du ställer in **DiscoveryType** till **private**, kommer en klient fortfarande att kunna komma åt tjänsten. Tjänsten skulle dock inte visas när den söker Relay-namnområde. Klienten måste då i stället känna till sökvägen för slutpunkten på förhand.
5. Tillämpa autentiseringsuppgifterna för tjänsten på de slutpunkter för tjänsten som finns angivna i filen App.config:

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Som vi sa i det förra steget kan du ha deklarerat flera tjänster och slutpunkter i konfigurationsfilen. Om du hade gjort detta skulle den här koden ha bläddrat igenom hela konfigurationsfilen och ha sökt efter varje slutpunkt som den skulle ha tillämpat på dina autentiseringsuppgifter. Men i den här självstudiekursen har konfigurationsfilen endast en slutpunkt.

### <a name="open-the-service-host"></a>Öppna tjänstevärden

1. Öppna tjänsten.

    ```csharp
    host.Open();
    ```
2. Informera användaren om att tjänsten körs och förklara hur tjänsten stängs av.

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```
3. Stäng tjänstevärden när du är klar.

    ```csharp
    host.Close();
    ```
4. Tryck på **Ctrl+Shift+B** för att skapa projektet.

### <a name="example"></a>Exempel

Den färdiga koden bör visas på följande sätt. Koden innehåller tjänstekontraktet och implementeringen från föregående steg i självstudiekursen och hyser in tjänsten i ett konsolprogram.

```csharp
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
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

Nästa steg är att skapa ett klientprogram och definiera det tjänstekontrakt som du kommer att implementera i senare steg. Observera att många av de här stegen liknar de steg som används för att skapa en tjänst: definiera ett kontrakt, redigera en App.config-fil, använda autentiseringsuppgifter för att ansluta till den vidarebefordrande tjänsten och så vidare. Den kod som används för dessa arbetsuppgifter visas i exemplet som följer efter proceduren.

1. Skapa ett nytt projekt i den befintliga Visual Studio-lösningen för klienten genom att göra följande:

   1. Högerklicka på den aktuella lösningen (inte projektet) i Solution Explorer, i samma lösning som innehåller tjänsten, och klicka sedan på **Lägg till**. Klicka sedan på **Nytt projekt**.
   2. I den **Lägg till nytt projekt** dialogrutan klickar du på **Visual C#** (om **Visual C#** inte visas, tittar du under **andra språk**), Välj den **Konsolapp (.NET Framework)** mall och ge den namnet **EchoClient**.
   3. Klicka på **OK**.
      <br />
2. Dubbelklicka på filen Program.cs i projektet **EchoClient** i Solution Explorer för att öppna den i redigeraren, om den inte redan är öppen.
3. Ändra namnet på namnområdet från standardnamnet `EchoClient` till `Microsoft.ServiceBus.Samples`.
4. Installera den [Service Bus NuGet-paketet](https://www.nuget.org/packages/WindowsAzure.ServiceBus): i Solution Explorer högerklickar du på den **EchoClient** projektet och klicka sedan på **hantera NuGet-paket**. Klicka på **Bläddra**-fliken och sök sedan efter `Microsoft Azure Service Bus`. Klicka på **Installera** och godkänn användningsvillkoren.

    ![][3]
5. Lägg till ett `using`-uttryck för namnområdet [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) i filen Program.cs.

    ```csharp
    using System.ServiceModel;
    ```
6. Lägga till en definition för tjänstekontraktet i namnområdet, som visas i följande exempel. Observera att den här definitionen är identisk med den som används i projektet **Service**. Du måste lägga till den här koden längst upp i namnområdet `Microsoft.ServiceBus.Samples`.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```
7. Tryck på **Ctrl+Shift+B** för att skapa klienten.

### <a name="example"></a>Exempel

Följande kod visar den aktuella statusen för filen Program.cs i den **EchoClient** projekt.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
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

I det här steget ska du skapa en App.config-fil för ett grundläggande klientprogrammet som ansluter till den tjänst som skapades tidigare under självstudiekursen. Den här App.config-filen definierar kontraktet, bindningen och namnet på slutpunkten. Den kod som används för dessa arbetsuppgifter visas i exemplet som följer efter proceduren.

1. Dubbelklicka på filen **App.config** i projektet **EchoClient** i Solution Explorer för att öppna filen i Visual Studio-redigeraren.
2. I elementet `<appSettings>` ersätter du platshållarna med namnet på ditt namnområde för tjänsten och den SAS-nyckel som du kopierade i ett av de föregående stegen.
3. Lägg till ett `<client>`-element inom elementet  system.serviceModel.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    I det här steget anger du att du definierar ett klientprogram i WCF-format.
4. Definiera namnet, kontraktet och bindningstypen för slutpunkten inom elementet `client`.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Det här steget definierar namnet på slutpunkten, det kontrakt som definierats i tjänsten och det faktum att klientprogrammet använder TCP för att kommunicera med Azure Relay. Namnet på slutpunkten används i nästa steg för att länka samman denna slutpunktskonfiguration med URI:n för tjänsten.
5. Klicka på **filen**, klicka sedan på **spara alla**.

### <a name="example"></a>Exempel

Följande kod visar filen App.config för Echo-klienten.

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
I det här steget ska du implementera ett grundläggande klientprogrammet som ansluter till den tjänst som du skapade tidigare under självstudiekursen. Liknande till tjänsten, utför klienten många av samma åtgärder för att komma åt Azure Relay:

1. Ställer in anslutningsläget.
2. Skapar den URI som lokaliserar värdtjänsten.
3. Definierar säkerhetsautentiseringen.
4. Tillämpar autentiseringsuppgifterna på anslutningen.
5. Öppnar anslutningen.
6. Utför de programspecifika uppgifterna.
7. Stänger ned anslutningen.

En av de viktigaste skillnaderna är dock att klientprogrammet använder en kanal för att ansluta till den vidarebefordrande tjänsten medan tjänsten använder ett anrop till **ServiceHost**. Den kod som används för dessa arbetsuppgifter visas i exemplet som följer efter proceduren.

### <a name="implement-a-client-application"></a>Implementera ett klientprogram
1. Ställ in anslutningsläget på **AutoDetect**. Lägg till följande kod inne i `Main()`-metoden för **EchoClient**-programmet.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```
2. Definiera variabler för att lagra värdena för tjänstens namnområde och den SAS-nyckel som läses från konsolen.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```
3. Skapa den URI som definierar värdens plats i Relay-projektet.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```
4. Skapa autentiseringsobjektet för slutpunkten för tjänstens namnområde.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```
5. Skapa den kanalfabrik som läser in konfigurationen som beskrivs i filen App.config.

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    En kanalfabriken är ett WCF-objekt som skapar en kanal via vilken tjänsten och klientprogrammet kan kommunicera.
6. Tillämpa autentiseringsuppgifterna.

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```
7. Skapa och öppna kanalen till tjänsten.

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```
8. Skriv det grundläggande gränssnittet och funktionerna för ekot.

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

    Observera att koden använder kanalobjektets instans som proxy för tjänsten.
9. Stäng kanalen och stäng fabriken.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

### <a name="example"></a>Exempel

Den färdiga koden bör se ut så här visar hur du skapar ett klientprogram, hur du anropar tjänsten och hur du stänger klienten när funktionsanropet har slutförts.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
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

1. Tryck på **Ctrl+Shift+B** för att skapa lösningen. Detta skapar både det klientprojekt och det tjänsteprojekt som du skapade i det förra steget.
2. Du måste se till att tjänsteprogrammet körs innan du startar klientprogrammet. Högerklicka på lösningen **EchoService** och klicka sedan på **Egenskaper** i Solution Explorer i Visual Studio.
3. Klicka på **Startprojekt** och sedan på knappen **Flera startprojekt** i dialogrutan för lösningsegenskaper. Kontrollera att **EchoService** visas först i listan.
4. Ställ in rutan **Åtgärd** för både **EchoService**- och **EchoClient**-projektet på **Starta**.

    ![][5]
5. Klicka på **Projektberoenden**. Markera **EchoClient** i rutan **Projekt**. Se till att **EchoService** är markerad i rutan **Är beroende av**.

    ![][6]
6. Klicka på **OK** för att stänga dialogrutan **Egenskaper**.
7. Tryck på **F5** att köra båda projekten.
8. Båda konsolfönstren öppnas och uppmanar dig att ange namnet på namnområdet. Tjänsten måste köras först och därför anger du namnområdet i konsolfönstret för **EchoService** och sedan trycker du på **Retur**.
9. Därefter uppmanas du ange din SAS-nyckel. Ange SAS-nyckeln och tryck på RETUR.

    Här är exempel på vad som kan matas ut från konsolfönstret. Observera att de värdena som anges här endast visas i exempelsyfte.

    `Your Service Namespace: myNamespace` `Your SAS Key: <SAS key value>`

    Tjänsteprogrammet skriver till konsolfönstret, till adressen som den lyssnar på, som visas i följande exempel.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/` `Press [Enter] to exit`
10. Ange samma information som du angav tidigare för tjänsteprogrammet i konsolfönstret för **EchoClient**. Följ de föregående stegen för att ange samma värden för namnområde för tjänsten och SAS-nyckeln för klientprogrammet.
11. När du har angett dessa värden, öppnar klienten en kanal till tjänsten och du uppmanas att ange lite text. Följ anvisningarna i följande utmatningsexempel för konsolen.

    `Enter text to echo (or [Enter] to exit):`

    Ange text för att skicka den till tjänsteprogrammet och tryck på Retur. Texten skickas till tjänsten via tjänsteåtgärden Echo och visas i tjänstekonsolfönstret, som i följande exempelutmatning.

    `Echoing: My sample text`

    Klientprogrammet får returvärdet för `Echo`-åtgärden, som är den ursprungliga texten, och skriver ut denna i sitt konsolfönster. Följande är ett utmatningsexempel från klientens konsolfönster.

    `Server echoed: My sample text`
12. Du kan fortsätta att skicka textmeddelanden från klienten till tjänsten på detta sätt. Tryck på Retur i konsolfönstren för klienten och tjänsten för att stänga båda programmen när du är klar.

## <a name="next-steps"></a>Nästa steg
Fortsätt till följande självstudie: 

> [!div class="nextstepaction"]
>[Exponera en lokal WCF REST-tjänst till en klient utanför ditt nätverk](service-bus-relay-rest-tutorial.md)

[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
