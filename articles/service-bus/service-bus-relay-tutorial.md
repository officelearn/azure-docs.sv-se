<properties 
    pageTitle="Självstudiekurs om att använda den vidarebefordrande meddelandetjänsten i Service Bus | Microsoft Azure"
    description="Skapa ett klientprogram och en tjänst för Service Bus med hjälp av den vidarebefordrande meddelandetjänsten i Service Bus."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="05/17/2016"
    ms.author="sethm" />

# Självstudiekurs för den vidarebefordrande meddelandetjänsten i Service Bus

I den här självstudiekursen kommer vi att visa hur du skapar ett enkelt klientprogram och en tjänst genom att använda ”reläfunktionerna” i Service Bus. Om du vill gå igenom en självstudiekurs som använder den [asynkrona meddelandetjänsten](service-bus-messaging-overview.md#Brokered-messaging) för Service Bus, går du till [.NET-självstudiekurs om asynkrona meddelanden i Service Bus](service-bus-brokered-tutorial-dotnet.md).

När du går igenom den här självstudiekursen kommer du att förstå vilka åtgärder som måste vidtas för skapa ett klient- och tjänsteprogram för Service Bus. En tjänst är en konstruktion som exponerar en eller flera slutpunkter som i sin tur exponerar en eller flera tjänsteåtgärder, precis som deras motsvarigheter i WCF. Slutpunkten för en tjänst anger en adress där tjänsten ligger, en bindning som innehåller den information som en klient måste använda för att kommunicera med tjänsten och ett kontrakt som definierar de funktioner som tjänsten levererar till sina klienter. Den största skillnaden mellan en WCF- och en Service Bus-tjänst är att slutpunkten exponeras i molnet i stället för lokalt på din dator.

När du har gått igenom alla teman i den här självstudiekursen, kommer du att ha en tjänst om är redo att köras samt en klient som kan anropa tjänstens funktionsåtgärder. I det första avsnittet beskriver vi hur du skapar ett konto. Nästa steg beskriver hur du definierar en tjänst som använder ett kontrakt, hur du implementerar tjänsten och hur du konfigurerar tjänsten i kod. Här beskrivs också hur du hyser in och kör tjänsten. Tjänsten som skapas är egenvärdsbaserad (self-hosted) och klienten och tjänsten körs på samma dator. Du kan konfigurera tjänsten genom att antingen använda koden eller en konfigurationsfil.

I de tre sista stegen beskriver vi hur du skapar ett klientprogram, konfigurerar detta och skapar och använder en klient som har åtkomst till värdens funktioner.

Alla teman i den här kursen förutsätter att du använder Visual Studio som utvecklingsmiljö.

## Registrera dig för ett konto

Det första steget är att skapa ett namnområde för Service Bus-tjänsten och få en SAS-nyckel (nyckel för signatur för delad åtkomst). Ett namnområde ger en appgräns för varje app som exponeras via Service Bus. Kombinationen av namnområde och SAS-nyckel ger en referens för Service Bus som används för att tillåta åtkomst till ett program.

1. Du skapar ett namnområde i den [klassiska Azure-portalen][]. På den vänstra sidan klickar du på **Service Bus** och sedan på **Skapa**. Ange ett namn för namnområdet, godkänn standardinställningarna för alla andra värden och klicka sedan på kryssmarkeringen OK.

    >[AZURE.NOTE] Du behöver inte använda samma namnområde för både klient- och tjänsteprogrammen.

    ![][4]

1. Klicka på namnet på namnområdet som du skapade i det förra steget i portalens huvudfönster.

2. Klicka på fliken **Konfigurera** för att visa de förvalda och delade åtkomstprinciperna samt nycklarna för namnområdet.

    ![][1]

3. Skriv ned den primära nyckeln för principen **RootManagerSharedAccessKey** eller kopiera den till Urklipp. Du kommer att använda det här värdet senare under självstudiekursen.

## Definiera ett WCF-tjänstekontrakt som ska användas med Service Bus

Tjänstekontraktet anger vilka åtgärder (webbserviceterminologin för metoder eller funktioner) som tjänsten stödjer. Kontrakt skapas genom att definiera ett gränssnitt för C++, C# eller Visual Basic. Varje metod i gränssnittet motsvarar en viss tjänsteåtgärd. Attributet [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) måste tillämpas på varje gränssnitt och attributet [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) måste tillämpas på varje åtgärd. Om en metod i ett gränssnitt som har attributet [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) inte har attributet [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), är inte den metoden exponerad. Koden för dessa arbetsuppgifter visas in exemplet som följer efter proceduren. En mer utförlig beskrivning av kontrakt och tjänster finns i [Utforma och implementera tjänster](https://msdn.microsoft.com/library/ms729746.aspx) i WCF-dokumentationen.

### Skapa ett Service Bus-kontrakt med ett gränssnitt

1. Öppna Visual Studio som administratör genom att högerklicka på programmet i **Start**-menyn och välja **Kör som administratör**.

2. Skapa ett nytt konsolprogramsprojekt. Klicka på **Arkiv**-menyn, välj **Nytt** och klicka sedan på **Projekt**. Klicka på **Visual C#** i dialogrutan **Nytt projekt** (om **Visual C#** inte visas, tittar du under **Andra språk**). Klicka på mallen **Konsolprogram** och ger den namnet **EchoService**. Klicka på **OK** för att skapa projektet.

    ![][2]

3. Installera Service Bus NuGet-paketet. Det här paketet lägger automatiskt till referenser till Service Bus-bibliotek, samt även WCF **System.ServiceModel**. [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) är det namnområde som ger dig programmatisk åtkomst till de grundläggande funktionerna i WCF. Service Bus använder många av WFC:s objekt och attribut för att definiera tjänstekontrakt.

    Högerklicka på lösningen i Solution Explorer och klicka sedan på **Hantera NuGet-paket för lösningen**. Klicka på **Bläddra**-fliken och sök sedan efter `Microsoft Azure Service Bus`. Kontrollera att projektnamnet är markerat i rutan **Versioner**. Klicka på **Installera** och godkänn användningsvillkoren.

    ![][3]

3. Dubbelklicka på filen Program.cs i Solution Explorer för att öppna den i redigeraren, om den inte redan är öppen.

4. Lägg till följande using-uttryck högst upp i filen:

    ```
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Ändra namnet för namnområdet från dess standardnamn **EchoService** till **Microsoft.ServiceBus.Samples**.

    >[AZURE.IMPORTANT] I den här kursen använder vi C#-namnområdet **Microsoft.ServiceBus.Samples**. Detta är namnområdet för den hanterade typen i kontraktet, den som används i konfigurationsfilen i steget [Konfigurerar WCF-klienten](#configure-the-wcf-client). Du kan ange vilken namnområden du vill när du skapar det här exemplet. Men självstudiekursen kommer inte att fungera om du inte sedan ändrar kontraktets namnområden och tjänster därefter. Det här görs i programmets konfigurationsfil. Det namnområde som anges i filen App.config måste vara samma som det namnområde som angavs i C#-filerna.

1. Direkt efter namnområdesdeklarationen `Microsoft.ServiceBus.Samples`, men inom namnområdet, definierar du ett nytt gränssnitt med namnet `IEchoContract`. Sedan tillämpar du attributet `ServiceContractAttribute` på gränssnittet med ett namnområdesvärde på **http://samples.microsoft.com/ServiceModel/Relay/**. Namnområdesvärdet skiljer sig från det namnområde som du använder under hela intervallet för din kod. Istället används namnområdesvärdet som en unik identifierare för det här kontraktet. Genom att ange namnområdet uttryckligen förhindrar du att det förvalda namnområdesvärdet läggs till i kontraktnamnet.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    >[AZURE.NOTE] Namnområdet för tjänstekontraktet innehåller vanligtvis ett namngivningsschema som inkluderar information om versionen. Om du tar med versionsinformation i namnområdet för tjänstekontraktet kan tjänsterna isolera större ändringar genom att definiera ett nytt tjänstkontrakt med ett nytt namnområde och sedan exponera det på en ny slutpunkt. På detta sätt kan klienter fortsätta att använda det gamla tjänstkontraktet utan att behöva uppdatera det. Versionsinformation kan bestå av ett datum eller ett build-nummer. Mer information finns i [Versionhantering för tjänster](http://go.microsoft.com/fwlink/?LinkID=180498). I just den här självstudiekursen innehåller namngivningsschemat för tjänstekontraktets namnområde inte någon information om versionerna.

1. Inne i gränssnittet `IEchoContract` deklarerar du en metod för den enkla åtgärd som `IEchoContract`-kontraktet exponerar i gränssnittet. Sedan tillämpar du attributet `OperationContractAttribute` på den metod som du vill exponera som en del av det offentliga Service Bus-kontraktet.

    ```
    [OperationContract]
    string Echo(string text);
    ```

1. Direkt efter gränssnittsdefinitionen `IEchoContract` deklarerar du en kanal som ärver egenskaper från båda `IEchoContract` och även från gränssnittet `IClientChannel`, som visas här:

    ```
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    En kanal är det WCF-objekt via vilken värden och klienten skickar information till varandra. Lite längre fram ska du skriva kod mot kanalen för att skapa ett eko av information mellan de två programmen.

1. Från menyn **Skapa** klickar du på **Skapa lösning** eller trycker på **Ctrl + Skift + B** för att bekräfta att det arbete du gjort hittills är korrekt.

### Exempel

Följande kod visar ett grundläggande gränssnitt som definierar ett Service Bus-kontrakt.

```
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

## Implementera WC- kontraktet för att använda Service Bus

För att kunna skapa ett Service Bus Relay måste du först skapa det kontrakt som definieras med hjälp av ett gränssnitt. Mer information om hur du skapar gränssnittet finns i det förra steget. Nästa steg är att implementera gränssnittet. Detta innebär att du skapar en klass som heter `EchoService` och som implementerar det användardefinierade gränssnittet `IEchoContract`. Efter att du har implementerat gränssnittet, konfigurerar du det genom att använda konfigurationsfilen App.config. Konfigurationsfilen innehåller information som appen behöver, till exempel namnet på tjänsten, namnet på kontraktet och vilken typ av protokoll som används för att kommunicera med Service Bus. Den kod som används för dessa arbetsuppgifter visas in exemplet som följer efter proceduren. En mer allmän diskussion om hur du implementerar ett tjänstekontrakt finns i [Implementera tjänstekontrakt](https://msdn.microsoft.com/library/ms733764.aspx) i WCF-dokumentationen.

1. Skapa en ny klass med namnet `EchoService` direkt efter definitionen av gränssnittet `IEchoContract`. Klassen `EchoService` implementerar gränssnittet `IEchoContract`. 

    ```
    class EchoService : IEchoContract
    {
    }
    ```
    
    Precis som med andra gränssnittsimplementeringar kan du implementera definitionen i en annan fil. Men i den här självstudiekursen ligger implementeringen i samma fil som gränssnittsdefinitionen och `Main`-metoden.

1. Tillämpa attributet [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) på gränssnittet `IEchoContract`. Attributet anger namnet på tjänsten och på namnområdet. När du har gjort det, visas klassen `EchoService` på följande sätt:

    ```
    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. Implementera metoden `Echo` som definierats i gränssnittet `IEchoContract` i klassen `EchoService`. 

    ```
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. Klicka på **Skapa** och sedan på **Skapa lösning** för att bekräfta att det arbete du utfört är korrekt.

### Så här definierar du konfigurationen för tjänstevärden

1. Konfigurationsfilen liknar till stora delar en WCF-konfigurationsfil. Den innehåller namnet på tjänsten, slutpunkten (det vill säga, den plats som Service Bus visar för klienter och värdar för att kommunicera med varandra) och bindningen (typ av protokoll som används för att kommunicera). Den största skillnaden är att den här konfigurerade tjänstslutpunkten refererar till en [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx)-bindning och denna är inte en del av .NET Framework. [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) är en av de bindningar som är definierade i Service Bus.

1. Dubbelklicka på filen App.config i **Solution Explorer** för att öppna den i Visual Studio-redigeraren.

2. I elementet `<appSettings>` ersätter du platshållarna med namnet på ditt namnområde för tjänsten och den SAS-nyckel som du kopierade i ett av de föregående stegen. 

1. Lägg till ett `<services>`-element inom taggarna `<system.serviceModel>`. Du kan definiera flera Service Bus-appar i en enda konfigurationsfil. I den här självstudiekursen definieras dock bara en.
 
    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. Inne i elementet `<services>` element lägger du till ett `<service>`-element för att definiera namnet på tjänsten.

    ```
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. Definiera platsen för slutpunktskontraktet inne i elementet `<service>` och även typen av bindning för slutpunkten.

    ```
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    Slutpunkten definierar var klienten ska söka efter värdprogrammet. Lite längre fram i självstudiekursen använder vi det här steget för att skapa en URI som exponerar värden helt via Service Bus. Bindningen deklarerar att vi använder TCP som protokoll för att kommunicera med Service Bus.

1. Från menyn **Skapa** klickar du på **Skapa lösning** för att bekräfta att det arbete du utfört är korrekt.

### Exempel

Följande kod visar implementeringen av tjänstekontraktet.

```
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

```
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

## Installera och använda en grundläggande webbtjänst för att registrera med Service Bus

I det här steget beskriver vi hur du kör en grundläggande Service Bus-tjänst.

### Så här skapar du autentiseringsuppgifter för Service Bus

1. Skapa två variabler i `Main()` där du ska lagra namnområdet och SAS-nyckeln som läses från konsolfönstret.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    SAS-nyckeln kommer att användas senare för att ge åtkomst till ditt Service Bus-projekt. Namnområdet skickas som en parameter till `CreateServiceUri` för att skapa en URI för tjänsten.

4. Med hjälp av ett objekt av typen [TransportClientEndpointBehavior](https://msdn.microsoft.com/library/microsoft.servicebus.transportclientendpointbehavior.aspx) anger du att du kommer att använda en SAS-nyckel som autentiseringstyp. Lägg till följande kod direkt efter den som lades till i det senaste steget.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### Så här skapar du en basadress för tjänsten

1. Skapa en `Uri`-instans för tjänstens basadress efter den kod som du lade till i det sista steget. Den här URI anger Service Bus-schemat, namnområdet och sökvägen för tjänstegränssnittet.

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

    "sb" är en förkortning för Service Bus-schemat och anger att vi använder TCP som protokoll. Detta har även tidigare angetts i konfigurationsfilen när [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) angavs som bindning.
    
    URI är `sb://putServiceNamespaceHere.windows.net/EchoService` för den här självstudiekursen.

### Så här skapar och konfigurerar du tjänstevärden

1. Ställ in anslutningsläget på `AutoDetect`.

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    Anslutningsläget beskriver det protokoll som tjänsten använder för att kommunicera med Service Bus. Detta är antingen HTTP eller TCP. Genom att använda standardinställningen `AutoDetect`, försöker tjänsten ansluta till Service Bus via TCP, om den är tillgänglig, och via HTTP om TCP inte är tillgänglig. Observera att detta skiljer sig från det protokoll som tjänsten anger för klientkommunikation. Det här protokollet bestäms av den bindning som används. En tjänst kan till exempel använda bindningen [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx). Denna anger att dess slutpunkt (som är exponerad på Service Bus) kommunicerar med klienterna via HTTP. Samma tjänst skulle kunna ange **ConnectivityMode.AutoDetect** så att tjänsten istället kommunicerar med Service Bus via TCP.

1. Skapa tjänstevärden med hjälp av den URI som du skapade tidigare i det här avsnittet.

    ```
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    Tjänstevärden är det WCF-objekt som instantierar tjänsten. Här kan du skicka över den typ av tjänst som du vill skapa (en `EchoService`-typ) samt även den adress som du vill exponera tjänsten på.

1. Lägg till referenser i [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) och [Microsoft.ServiceBus.Description](https://msdn.microsoft.com/library/microsoft.servicebus.description.aspx) högst upp i filen Program.cs.

    ```
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. När du är tillbaka i `Main()`, konfigurerar du slutpunkten för att ge allmän åtkomst till den.

    ```
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Det här steget meddelar Service Bus om att din app kan hittas offentligt genom att undersöka Service Bus ATOM-flödet för projektet. Om du ställer in **DiscoveryType** till **private**, kommer en klient fortfarande att kunna komma åt tjänsten. Tjänsten skulle dock inte visas när den söker efter namnområdet för Service Bus. Klienten måste då i stället känna till sökvägen för slutpunkten på förhand.

1. Tillämpa autentiseringsuppgifterna för tjänsten på de slutpunkter för tjänsten som finns angivna i filen App.config:

    ```
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Som vi sa i det förra steget kan du ha deklarerat flera tjänster och slutpunkter i konfigurationsfilen. Om du hade gjort detta skulle den här koden ha bläddrat igenom hela konfigurationsfilen och ha sökt efter varje slutpunkt som den skulle ha tillämpat på dina autentiseringsuppgifter. Men i den här självstudiekursen har konfigurationsfilen endast en slutpunkt.

### Så här gör du för att öppna tjänstevärden

1. Öppna tjänsten.

    ```
    host.Open();
    ```

1. Informera användaren om att tjänsten körs och förklara hur tjänsten stängs av.

    ```
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Stäng tjänstevärden när du är klar.

    ```
    host.Close();
    ```

1. Tryck på **Ctrl+Shift+B** för att skapa projektet.

### Exempel

Följande exempel innehåller tjänstkontraktet och implementeringen från föregående steg i självstudiekursen och hyser in tjänsten i ett konsolprogram. Kompilera följande till en körbar fil med namnet EchoService.exe.

```
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

            // Add the Service Bus credentials to all endpoints specified in configuration.
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

## Skapa en WCF-klient för tjänstekontraktet

Nästa steg är att skapa ett grundläggande klientprogram för Service Bus och definiera det tjänstekontrakt som du kommer att implementera i senare steg. Observera att många av de här stegen liknar de steg som används för att skapa en tjänst: definiera ett kontrakt, redigera en App.config-fil, använda autentiseringsuppgifter för att ansluta till Service Bus och så vidare. Den kod som används för dessa arbetsuppgifter visas in exemplet som följer efter proceduren.

1. Skapa ett nytt projekt i den befintliga Visual Studio-lösningen för klienten genom att göra följande:
    1. Högerklicka på den aktuella lösningen (inte projektet) i Solution Explorer, i samma lösning som innehåller tjänsten, och klicka sedan på **Lägg till**. Klicka sedan på **Nytt projekt**.
    2. I dialogrutan **Lägg till nytt projekt** klickar du på **Visual C#** (om **Visual C#** inte visas tittar du under **Andra språk**). Sedan väljer du mallen **Konsolprogram** och ger den namnet **EchoClient**.
    3. Klicka på **OK**.
<br />

1. Dubbelklicka på filen Program.cs i projektet **EchoClient** i Solution Explorer för att öppna den i redigeraren, om den inte redan är öppen.

1. Ändra namnet på namnområdet från standardnamnet `EchoClient` till `Microsoft.ServiceBus.Samples`.

1. Installera [Service Bus NuGet-paketet](https://www.nuget.org/packages/WindowsAzure.ServiceBus). Högerklicka på projektet **EchoClient** i Solution Explorer och klicka sedan på **Hantera NuGet-paket**. Klicka på **Bläddra**-fliken och sök sedan efter `Microsoft Azure Service Bus`. Klicka på **Installera** och godkänn användningsvillkoren.

    ![][3]

1. Lägg till ett `using`-uttryck för namnområdet [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) i filen Program.cs. 

    ```
    using System.ServiceModel;
    ```

1. Lägga till en definition för tjänstekontraktet i namnområdet, som visas i följande exempel. Observera att den här definitionen är identisk med den som används i projektet **Service**. Du måste lägga till den här koden längst upp i namnområdet `Microsoft.ServiceBus.Samples`.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Tryck på **Ctrl+Shift+B** för att skapa klienten.

### Exempel

Följande kod visar den aktuella statusen för filen Program.cs i projektet EchoClient.

```
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

## Konfigurera WCF-klienten

I det här steget ska du skapa en App.config-fil för ett grundläggande klientprogrammet som ansluter till den tjänst som skapades tidigare under självstudiekursen. Den här App.config-filen definierar kontraktet, bindningen och namnet på slutpunkten. Den kod som används för dessa arbetsuppgifter visas in exemplet som följer efter proceduren.

1. Dubbelklicka på filen **App.config** i projektet **EchoClient** i Solution Explorer för att öppna filen i Visual Studio-redigeraren.

2. I elementet `<appSettings>` ersätter du platshållarna med namnet på ditt namnområde för tjänsten och den SAS-nyckel som du kopierade i ett av de föregående stegen.

1. Lägg till ett `<client>`-element inom elementet  system.serviceModel.

    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    I det här steget anger du att du definierar ett klientprogram i WCF-format.

1. Definiera namnet, kontraktet och bindningstypen för slutpunkten inom elementet `client`.

    ```
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Det här steget definierar namnet på slutpunkten, det kontrakt som definierats i tjänsten och det faktum att klientprogrammet använder TCP för att kommunicera med Service Bus. Namnet på slutpunkten används i nästa steg för att länka samman denna slutpunktskonfiguration med URI:n för tjänsten.

1. Klicka på **Arkiv** och sedan på **Spara alla**.

## Exempel

Följande kod visar filen App.config för Echo-klienten.

```
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

## Implementera WCF-klienten för att anropa Service Bus

I det här steget ska du implementera ett grundläggande klientprogrammet som ansluter till den tjänst som du skapade tidigare under självstudiekursen. På ett liknande sätt som med tjänsten, utför klienten många av samma åtgärder för att få tillgång till Service Bus:

1. Ställer in anslutningsläget.
1. Skapar den URI som lokaliserar värdtjänsten.
1. Definierar säkerhetsautentiseringen.
1. Tillämpar autentiseringsuppgifterna på anslutningen.
1. Öppnar anslutningen.
1. Utför de programspecifika uppgifterna.
1. Stänger ned anslutningen.

En av de viktigaste skillnaderna är dock att klientprogrammet använder en kanal för att ansluta till Service Bus, medan tjänsten använder ett anrop till **ServiceHost**. Den kod som används för dessa arbetsuppgifter visas in exemplet som följer efter proceduren.

### Så här implementerar du ett klientprogram

1. Ställ in anslutningsläget på **AutoDetect**. Lägg till följande kod inne i `Main()`-metoden för **EchoClient**-programmet.

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Definiera variabler för att lagra värdena för tjänstens namnområde och den SAS-nyckel som läses från konsolen.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Skapa den URI som definierar värdens plats i ditt Service Bus-projekt.

    ```
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Skapa autentiseringsobjektet för slutpunkten för tjänstens namnområde.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Skapa den kanalfabrik som läser in konfigurationen som beskrivs i filen App.config.

    ```
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    En kanalfabriken är ett WCF-objekt som skapar en kanal via vilken tjänsten och klientprogrammet kan kommunicera.

1. Tillämpa autentiseringsuppgifterna för Service Bus.

    ```
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Skapa och öppna kanalen till tjänsten.

    ```
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Skriv det grundläggande gränssnittet och funktionerna för ekot.

    ```
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

1. Stäng kanalen och stäng fabriken.

    ```
    channel.Close();
    channelFactory.Close();
    ```

## Så här gör du för att köra programmen

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

    `Your Service Namespace: myNamespace`
    `Your SAS Key: <SAS key value>`

    Tjänsteprogrammet skriver till konsolfönstret, till adressen som den lyssnar på, som visas i följande exempel.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`
    `Press [Enter] to exit`
    
10. Ange samma information som du angav tidigare för tjänsteprogrammet i konsolfönstret för **EchoClient**. Följ de föregående stegen för att ange samma värden för namnområde för tjänsten och SAS-nyckeln för klientprogrammet.

11. När du har angett dessa värden, öppnar klienten en kanal till tjänsten och du uppmanas att ange lite text. Följ anvisningarna i följande utmatningsexempel för konsolen.

    `Enter text to echo (or [Enter] to exit):` 

    Ange text för att skicka den till tjänsteprogrammet och tryck på Retur. Texten skickas till tjänsten via tjänsteåtgärden Echo och visas i tjänstekonsolfönstret, som i följande exempelutmatning.

    `Echoing: My sample text`

    Klientprogrammet får returvärdet för `Echo`-åtgärden, som är den ursprungliga texten, och skriver ut denna i sitt konsolfönster. Följande är ett utmatningsexempel från klientens konsolfönster.

    `Server echoed: My sample text`

12. Du kan fortsätta att skicka textmeddelanden från klienten till tjänsten på detta sätt. Tryck på Retur i konsolfönstren för klienten och tjänsten för att stänga båda programmen när du är klar.

## Exempel

I följande exempel visas hur du skapar ett klientprogram, hur du anropar tjänstens funktioner och hur du stänger klienten när funktionsanropet har slutförts.

```
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

## Nästa steg

I den här självstudiekursen visades hur du skapar ett klientprogram och en tjänst för Service Bus med ”reläfunktioner” för Service Bus (Relay). För en liknande självstudiekurs där man använder en [asynkron meddelandetjänst](service-bus-messaging-overview.md#Brokered-messaging), för Service Bus, kan du gå till [.NET-självstudiekurs för den asynkrona meddelandetjänsten i Service Bus](service-bus-brokered-tutorial-dotnet.md).

I följande avsnitt kan du lära dig mer om Service Bus.

- [Översikt över meddelandetjänsten i Service Bus](service-bus-messaging-overview.md)
- [Grunderna om Service Bus](service-bus-fundamentals-hybrid-solutions.md)
- [Service Bus-arkitektur](service-bus-architecture.md)

[klassiska Azure-portalen]: http://manage.windowsazure.com

[1]: ./media/service-bus-relay-tutorial/service-bus-policies.png
[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[4]: ./media/service-bus-relay-tutorial/create-ns.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png



<!--HONumber=Jun16_HO2-->


