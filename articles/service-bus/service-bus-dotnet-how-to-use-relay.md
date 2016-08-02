<properties
    pageTitle="Använda Service Bus Relay med .NET | Microsoft Azure"
    description="Lär dig mer om att använda tjänsten Azure Service Bus Relay för att ansluta två appar som är värdbaserade på olika platser."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="05/06/2016"
    ms.author="sethm"/>


# Använda tjänsten Azure Service Bus Relay

Den här artikeln beskriver hur du använder tjänsten Service Bus Relay. Exemplen är skrivna i C# och använder API:et Windows Communication Foundation (WCF) med tillägg som finns i Service Bus-sammansättningen. Mer information om Service Bus Relay finns i översikten [Vidarebefordrande meddelandetjänst i Service Bus](service-bus-relay-overview.md).

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## Vad är Service Bus Relay?

[Service Bus *Relay*-tjänsten ](service-bus-relay-overview.md) gör att du kan skapa hybridprogram som körs både i ett datacenter för Azure och i din egen lokala företagsmiljö. Service Bus Relay förenklar detta genom att på ett säkert sätt exponera tjänster för Windows Communication Foundation (WCF). Dessa finns i ett företagsnätverk som kopplar upp sig mot det offentliga molnet, utan att behöva öppna en brandväggsanslutning eller kräva störande ändringar i företagets nätverksinfrastruktur.

![Relay-begrepp](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

Service Bus Relay gör att du kan vara värd för WCF-tjänster inom ramen för din befintliga företagsmiljö. Du kan sedan delegera lyssningen för inkommande sessioner och förfrågningar till de här WCF-tjänsterna för de Service Bus-tjänster som körs i Azure. Tack vare detta kan du exponera dessa tjänster för programkoden som körs i Azure, eller för mobila arbetare eller miljöer för extranätpartner. Med hjälp av Service Bus kan du på ett säkert sätt styra åtkomsten till tjänsterna på en detaljerad nivå. Det ger dig ett kraftfullt och säkert sätt att exponera programfunktioner och data från dina befintliga företagslösningar och dra nytta av dessa från molnet.

I den här artikeln visas hur du använder Service Bus Relay för att skapa en WCF-webbtjänst som exponeras med hjälp av en TCP-kanalbindning och som implementerar en säker konversation mellan två parter.

## Skapa ett namnområde för tjänsten

Om du vill börja använda Service Bus Relay i Azure, måste du först skapa ett namnområde. Ett namnområde innehåller en omfattningsbehållare för adressering av Service Bus-resurser i ditt program.

Gör så här för att skapa ett namnområde för tjänsten:

1.  Logga in på den [klassiska Azure-portalen][].

2.  I portalens vänstra navigationsfält klickar du på **Service Bus**.

3.  I den understa rutan i portalen klickar du på **Skapa**.

    ![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-13.png)

4.  I dialogrutan **Lägg till ett nytt namnområde** anger du ett namn för namnområdet.
    Systemet kontrollerar omedelbart om namnet är tillgängligt.

    ![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-04.png)

5.  Efter att du kontrollerat att namnet är tillgängligt, väljer du land eller region där namnområdet ska vara beläget (se till att använda samma land/region som du distribuerar dina beräkningsresurser i).

    > [AZURE.IMPORTANT] Välj *samma region* som du tänker välja när du distribuerar ditt program. Så får du bäst prestanda.

6.  Lämna de andra fälten i dialogrutan med sina standardvärden (**Meddelandetjänst** och **Standardnivå**) och klicka sedan på kryssmarkeringen. Systemet skapar namnområdet och aktiverar det. Du kan behöva vänta några minuter medan systemet tilldelar resurser till ditt konto.

    ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-27.png)

    Det namnområde som du skapat visas sedan i portalen. Det tar dock en stund innan det aktiveras. Vänta tills statusen blir **Aktiv** innan du fortsätter.

## Skaffa standardautentiseringsuppgifter för hantering av namnområdet

För att kunna utföra hanteringsåtgärder – till exempel skapa en relay-anslutning – på det nya namnområdet måste du konfigurera auktoriseringsregeln för SAS (signatur för delad åtkomst) för namnområdet. Mer information om SAS finns i [Autentisering med signatur för delad åtkomst med Service Bus][].

1.  I det vänstra navigationsfönstret klickar du på **Service Bus**-noden för att visa listan över tillgängliga namnområden.
    ![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-13.png)

2.  Dubbelklicka på namnet för det namnområde som du nyss skapat från listan som visas.
    ![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-09.png)

3.  Klicka på fliken **Konfigurera** högst upp på sidan.

4.  När ett namnområde för Service Bus etableras, skapas en **SharedAccessAuthorizationRule**, med **KeyName** inställt på **RootManageSharedAccessKey**, som standard. Den här sidan visar denna nyckel, samt även de primära och sekundära nycklarna för standardregeln.

## Hämta Service Bus-NuGet-paketet

[Service Bus-NuGet-paketet](https://www.nuget.org/packages/WindowsAzure.ServiceBus) är det enklaste sättet att komma åt Service Bus-API:et och att konfigurera din app med alla Service Bus-beroenden. Om du vill installera NuGet-paketet i din app, ska du göra följande:

1.  Högerklicka på **Referenser** i Solution Explorer och klicka sedan på **Hantera NuGet-paket**.
2.  Sök efter ”Service Bus” och markera posten **Microsoft Azure Service Bus**. Klicka på **Installera** för att slutföra installationen och stäng sedan följande dialogruta.

    ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)


## Använd Service Bus för att exponera och använda en SOAP-webbtjänst med TCP

Om du vill exponera en befintlig WCF SOAP-webbtjänst för extern användning, måste du göra ändringar i tjänstebindningarna och i adresserna. Det här kan kräva att du även gör ändringar i konfigurationsfilen, eller i koden, beroende på hur du har skapat och konfigurerat dina WCF-tjänster. Observera att WCF gör att du kan ha flera nätverksslutpunkter inom samma tjänst. Det innebär att du kan behålla de befintliga, interna slutpunkterna samtidigt som du lägger till Service Bus-slutpunkter för extern åtkomst.

I det här steget ska du skapa en enkel WCF-tjänst och lägga till en Service Bus-lyssnare i den. Den här övningen förutsätter att du är bekant med Visual Studio och går därför inte igenom alla detaljer om hur man skapar ett projekt. Övningen fokuserar istället på koden.

Innan du börjar gå igenom stegen nedan måste du slutföra följande procedur för att göra justeringar i din miljö:

1.  Skapa ett konsolprogram i Visual Studio som innehåller två projekt, ”Klient” och ”Tjänst”, inne i lösningen.
2.  Lägg till Microsoft Azure Service Bus-NuGet-paketet i båda projekten. Detta lägger till alla nödvändiga sammansättningsreferenser i dina projekt.

### Så här skapar du tjänsten

Först skapar du själva tjänsten. Alla WCF-tjänster består av minst tre separata delar:

-   Definitionen av ett kontrakt, som beskriver vilka meddelanden som ska bytas ut och vilka åtgärder som ska anropas.
-   Implementeringen av det nämnda kontraktet.
-   Värden som används som värd för WCF-tjänsten och exponerar ett antal slutpunkter.

Kodexemplet i det här avsnittet tar upp var och en av dessa komponenter i detalj.

Kontraktet definierar en enda åtgärd `AddNumbers`, som adderar två tal och returnerar resultatet. Gränssnittet `IProblemSolverChannel` gör att klienten enklare kan hantera proxylivslängden. Att skapa ett sådant gränssnitt anses vara bästa praxis. Det är en bra idé att placera kontraktdefinitionen i en separat fil så att du kan referera till filen från både ”Klient”- och ”Tjänst”-projektet, men du kan också kopiera koden till båda projekten.

```
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

När kontraktet väl är på plats är implementeringen enkel.

```
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### Konfigurera en tjänstevärd programmässigt

När kontraktet är på plats och implementeringen har genomförts, kan du nu vara värd för tjänsten. Värdhanteringen utförs inne i ett [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/azure/system.servicemodel.servicehost.aspx)-objekt som tar hand om hanteringen av tjänsteinstanserna och fungerar som värd för de slutpunkter som lyssnar efter meddelanden. Följande kod konfigurerar tjänsten med både en vanlig, lokal slutpunkt och en Service Bus-slutpunkt för att visa utseende, sida vid sida, för interna och externa slutpunkter. Ersätt strängen *namnområde* med ditt namnområdesnamn och *yourKey* med den SAS-nyckel som du fick i det förra steget av installationen.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "yourKey")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

I det här exemplet skapar du två slutpunkter som ligger på samma kontraktsimplementering. En är lokal och en projiceras via Service Bus. De viktigaste skillnaderna mellan dem är bindningarna: [NetTcpBinding](https://msdn.microsoft.com/library/azure/system.servicemodel.nettcpbinding.aspx) för den lokala punkten och [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) för Service Bus-slutpunkten och adresserna. Den lokala slutpunkten har en lokal nätverksadress med en separat port. Service Bus-slutpunkten har en slutpunktsadress som består av strängen `sb`, ditt namnområdesnamn och sökvägen ”solver”. Detta leder till att URI:n `sb://[serviceNamespace].servicebus.windows.net/solver` identifierar tjänsteslutpunkten som en Service Bus-TCP-slutpunkt med ett fullständigt kvalificerat, externt DNS-namn. Om du placerar ut koden som ska ersätta platshållarna så som beskrivs ovan i `Main`-funktionen i **Tjänsteprogrammet**, kommer du att få en fungerande tjänst. Om du vill att din tjänst endast ska lyssna på Service Bus, tar du bort den lokala slutpunktsdeklarationen.

### Konfigurera en tjänstevärd i filen App.config

Du kan också konfigurera värden med hjälp av filen App.config. Koden för tjänstevärden i det här fallet visas i nästa exempel.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

Slutpunktsdefinitionerna flyttas till filen App.config. Observera att NuGet-paketet redan har lagt till en uppsättning definitioner i filen App.config. Dessa är de konfigurationstillägg som krävs för Service Bus. Följande exempel, som är en exakt motsvarighet till föregående kod, bör visas direkt under elementet **system.serviceModel**. I det här kodexemplet förutsätter vi att namnområdet för ditt C#-projekt har namnet **Tjänst**.
Ersätt platshållaren med namnområdet och SAS-nyckeln för Service Bus-tjänsten.

```
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://namespace.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

När du har gjort dessa ändringar, startar tjänsten som förut men med två live-slutpunkter: en lokal och en som lyssnar i molnet.

### Skapa klienten

#### Konfigurera en klient programmässigt

Om du vill använda tjänsten, kan du skapa en WCF-klient som använder ett [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx)-objekt. Service Bus använder en tokenbaserad säkerhetsmodell som implementeras med hjälp av SAS. Klassen [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) representerar en leverantör av säkerhetstoken med inbyggda fabriksmetoder som returnerar ett antal välkända tokenleverantörer. I exemplet nedan används metoden [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) för att hantera förvärvet av lämplig SAS-token. Namnet och nyckeln är de som du fick från portalen, enligt beskrivningen i det förra avsnittet.

Först måste du referera eller kopiera kontraktkoden `IProblemSolver` från tjänsten till ditt klientprojekt.

Byt sedan ut koden i `Main`-metoden för klienten. Ersätt återigen platshållartexten med namnområdet och SAS-nyckeln för din Service Bus.

```
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","yourKey") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Du kan nu skapa klienten och tjänsten och köra dem (kör tjänsten först). Klienten anropar tjänsten och skriver ut **9**. Du kan köra klient och servern på olika datorer, även över nätverk. Kommunikationen kommer ändå att fungera. Klientkoden kan också köras i molnet eller lokalt.

#### Konfigurera en klient i filen App.config

Följande kod visar hur du konfigurerar klienten med hjälp av filen App.config.

```
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Slutpunktsdefinitionerna flyttas till filen App.config. Följande exempel, som är samma som koden som listades tidigare, bör visas direkt under elementet **system.serviceModel**. Här, precis som tidigare, måste du ersätta platshållaren med namnområdet och SAS-nyckeln för Service Bus-tjänsten.

```
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://namespace.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## Nästa steg

Nu när du har lärt dig grunderna om tjänsten Service Bus Relay, kan du följa dessa länkar om du vill veta mer.

- [Översikt över den vidarebefordrande meddelandetjänsten i Service Bus](service-bus-relay-overview.md)
- [Översikt över arkitekturen i Azure Service Bus](service-bus-fundamentals-hybrid-solutions.md)
- Hämta Service Bus-exempel från [Azure-exempel][] eller gå till [översikt över Service Bus-exempel][].

  [klassiska Azure-portalen]: http://manage.windowsazure.com
  [Autentisering med signatur för delad åtkomst med Service Bus]: service-bus-shared-access-signature-authentication.md
  [Azure-exempel]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [översikt över Service Bus-exempel]: service-bus-samples.md


<!--HONumber=Jun16_HO2-->


