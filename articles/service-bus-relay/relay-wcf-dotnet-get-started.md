---
title: Kom igång med Azure Relay WCF-reläer i .NET | Microsoft Docs
description: Lär dig hur du använder Azure Relay WCF-reläer för att ansluta två appar som är värdbaserade på olika platser.
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 5493281a-c2e5-49f2-87ee-9d3ffb782c75
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/20/2017
ms.author: spelluru
ms.openlocfilehash: b9701eae026522238424a21ae3ecf2baa40334fa
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43701773"
---
# <a name="how-to-use-azure-relay-wcf-relays-with-net"></a>Hur du använder Azure Relay WCF-reläer med .NET
Den här artikeln beskriver hur du använder Azure Relay-tjänsten. Exemplen är skrivna i C# och använder API:et Windows Communication Foundation (WCF) med tillägg som finns i Service Bus-sammansättningen. Mer information om Azure relay finns i den [översikt över Azure Relay](relay-what-is-it.md).

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="what-is-wcf-relay"></a>Vad är WCF Relay?

Azure [ *WCF Relay* ](relay-what-is-it.md) service kan du skapa hybridprogram som körs i både ett Azure-datacenter och din egen lokala företagsmiljö. Tjänsten relay förenklar detta genom att på ett säkert sätt exponera tjänster för Windows Communication Foundation (WCF) som finns i ett företagsnätverk mot det offentliga molnet, utan att behöva öppna en brandväggsanslutning eller kräva störande ändringar i företagets nätverksinfrastruktur.

![WCF Relay-begrepp](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

Azure Relay kan du värden WCF-tjänster i din befintliga företagsmiljö. Du kan sedan delegera lyssningen för inkommande sessioner och förfrågningar till de här WCF-tjänster till den vidarebefordrande tjänsten som körs i Azure. Tack vare detta kan du exponera dessa tjänster för programkoden som körs i Azure, eller för mobila arbetare eller miljöer för extranätpartner. Du kan på ett säkert sätt styra åtkomsten till dessa tjänster på en detaljerad nivå Relay. Det ger dig ett kraftfullt och säkert sätt att exponera programfunktioner och data från dina befintliga företagslösningar och dra nytta av dessa från molnet.

Den här artikeln beskriver hur du använder Azure Relay för att skapa en WCF-webbtjänst, tillgängliga via en TCP-kanal som binder, som implementerar en säker konversation mellan två parter.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="get-the-service-bus-nuget-package"></a>Hämta Service Bus-NuGet-paketet
[Service Bus-NuGet-paketet](https://www.nuget.org/packages/WindowsAzure.ServiceBus) är det enklaste sättet att komma åt Service Bus-API:et och att konfigurera din app med alla Service Bus-beroenden. Om du vill installera NuGet-paketet i ditt projekt gör du följande:

1. Högerklicka på **Referenser** i Solution Explorer och klicka sedan på **Hantera NuGet-paket**.
2. Sök efter "Service Bus" och markera posten **Microsoft Azure Service Bus**. Klicka på **Installera** för att slutföra installationen och stäng sedan följande dialogruta:
   
   ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## <a name="expose-and-consume-a-soap-web-service-with-tcp"></a>Exponera och använda ett SOAP-webbtjänst med TCP
Om du vill exponera en befintlig WCF SOAP-webbtjänst för extern användning, måste du göra ändringar i tjänstebindningarna och i adresserna. Det här kan kräva att du även gör ändringar i konfigurationsfilen, eller i koden, beroende på hur du har skapat och konfigurerat dina WCF-tjänster. Observera att WCF gör att du kan ha flera nätverksslutpunkter inom samma tjänst, så du kan behålla de befintliga interna slutpunkterna när du lägger till relay slutpunkter för extern åtkomst på samma gång.

I den här uppgiften ska du skapa en enkel WCF-tjänst och Lägg till en relälyssnaren. Den här övningen förutsätter att du är bekant med Visual Studio och går därför inte igenom alla detaljer om hur man skapar ett projekt. Övningen fokuserar istället på koden.

Innan du börjar med dessa steg måste du slutföra följande procedur för att göra justeringar i din miljö:

1. Skapa ett konsolprogram i Visual Studio som innehåller två projekt, ”Klient” och ”Tjänst”, inne i lösningen.
2. Lägg till Service Bus NuGet-paketet i båda projekten. Detta paket lägger till alla nödvändiga sammansättningsreferenser i dina projekt.

### <a name="how-to-create-the-service"></a>Så här skapar du tjänsten
Först skapar du själva tjänsten. Alla WCF-tjänster består av minst tre separata delar:

* Definitionen av ett kontrakt, som beskriver vilka meddelanden som ska bytas ut och vilka åtgärder som ska anropas.
* Implementeringen av kontraktet.
* Värden som används som värd för WCF-tjänsten och exponerar ett flera slutpunkter.

Kodexemplet i det här avsnittet tar upp var och en av dessa komponenter i detalj.

Kontraktet definierar en enda åtgärd `AddNumbers`, som adderar två tal och returnerar resultatet. Gränssnittet `IProblemSolverChannel` gör att klienten enklare kan hantera proxylivslängden. Att skapa ett sådant gränssnitt anses vara bästa praxis. Det är en bra idé att placera kontraktdefinitionen i en separat fil så att du kan referera till filen från både ”Klient”- och ”Tjänst”-projektet, men du kan också kopiera koden till båda projekten.

```csharp
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

Kontraktet på plats är implementeringen på följande sätt:

```csharp
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### <a name="configure-a-service-host-programmatically"></a>Konfigurera en tjänstevärd programmässigt
När kontraktet är på plats och implementeringen har genomförts, kan du nu vara värd för tjänsten. Värdhanteringen utförs inne i ett [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/system.servicemodel.servicehost.aspx)-objekt som tar hand om hanteringen av tjänsteinstanserna och fungerar som värd för de slutpunkter som lyssnar efter meddelanden. Följande kod konfigurerar tjänsten med både en vanlig, lokal slutpunkt och en relay-slutpunkt för att visa utseende, sida vid sida, för interna och externa slutpunkter. Ersätt strängen *namnområde* med ditt namnområdesnamn och *yourKey* med den SAS-nyckel som du fick i det förra steget av installationen.

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "<yourKey>")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

I det här exemplet skapar du två slutpunkter som ligger på samma kontraktsimplementering. En är lokal och en projiceras via Azure Relay. De viktigaste skillnaderna mellan dem är bindningarna: [NetTcpBinding](https://msdn.microsoft.com/library/system.servicemodel.nettcpbinding.aspx) för den lokala punkten och [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding#microsoft_servicebus_nettcprelaybinding) för relay-slutpunkten och adresserna. Den lokala slutpunkten har en lokal nätverksadress med en separat port. Relay-slutpunkten har en slutpunktsadress som består av strängen `sb`, ditt namnområdesnamn och sökvägen ”solver”. Detta resulterar i URI: N `sb://[serviceNamespace].servicebus.windows.net/solver`, identifierar tjänsteslutpunkten som en Service Bus (relay) TCP-slutpunkt med ett fullständigt kvalificerat externa DNS-namn. Om du placerar ut koden som ska ersätta platshållarna i `Main`-funktionen i **Tjänsteprogrammet** kommer du att få en fungerande tjänst. Om du vill att din tjänst endast ska lyssna på relay, kan du ta bort den lokala slutpunktsdeklarationen.

### <a name="configure-a-service-host-in-the-appconfig-file"></a>Konfigurera en tjänstevärd i filen App.config
Du kan också konfigurera värden med hjälp av filen App.config. Koden för tjänstevärden i det här fallet visas i nästa exempel.

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

Slutpunktsdefinitionerna flyttas till filen App.config. NuGet-paketet har redan lagt till en uppsättning definitioner i filen App.config, som är nödvändig konfiguration-tillägg för Azure Relay. Följande exempel, som är en exakt motsvarighet till föregående kod, bör visas direkt under elementet **system.serviceModel**. I det här kodexemplet förutsätter vi att namnområdet för ditt C#-projekt har namnet **Tjänst**.
Ersätt platshållarna med dina relay namn för namnområdet och SAS-nyckel.

```xml
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://<namespaceName>.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

När du har gjort dessa ändringar, startar tjänsten som förut men med två live-slutpunkter: en lokal och en som lyssnar i molnet.

### <a name="create-the-client"></a>Skapa klienten
#### <a name="configure-a-client-programmatically"></a>Konfigurera en klient programmässigt
Om du vill använda tjänsten, kan du skapa en WCF-klient som använder ett [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx)-objekt. Service Bus använder en tokenbaserad säkerhetsmodell som implementeras med hjälp av SAS. Klassen [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) representerar en leverantör av säkerhetstoken med inbyggda fabriksmetoder som returnerar ett antal välkända tokenleverantörer. I följande exempel används metoden [CreateSharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#Microsoft_ServiceBus_TokenProvider_CreateSharedAccessSignatureTokenProvider_System_String_) för att hantera förvärvet av lämplig SAS-token. Namnet och nyckeln är de som du fick från portalen, enligt beskrivningen i det förra avsnittet.

Först måste du referera eller kopiera kontraktkoden `IProblemSolver` från tjänsten till ditt klientprojekt.

Ersätt sedan koden i den `Main` metoden för klienten, Ersätt återigen platshållartexten med relay-namnområde och SAS-nyckeln.

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "<namespaceName>", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","<yourKey>") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Du kan nu skapa klienten och tjänsten och köra dem (kör tjänsten först). Klienten anropar tjänsten och skriver ut **9**. Du kan köra klient och servern på olika datorer, även över nätverk. Kommunikationen kommer ändå att fungera. Klientkoden kan också köras i molnet eller lokalt.

#### <a name="configure-a-client-in-the-appconfig-file"></a>Konfigurera en klient i filen App.config
Följande kod visar hur du konfigurerar klienten med hjälp av filen App.config.

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Slutpunktsdefinitionerna flyttas till filen App.config. I följande exempel, som är samma som koden som listades tidigare, bör visas direkt under den `<system.serviceModel>` element. Här, som tidigare måste du ersätta platshållarna med dina relay-namnområde och SAS-nyckel.

```xml
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://<namespaceName>.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Azure Relay, kan du följa dessa länkar om du vill veta mer.

* [Vad är Azure Relay?](relay-what-is-it.md)
* [Azure Service Bus översikt över arkitekturen](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
* Hämta Service Bus-exempel från [Azure-exempel] [ Azure samples] eller finns i den [översikt över Service Bus-exempel][overview of Service Bus samples].

[Shared Access Signature Authentication with Service Bus]: ../service-bus-messaging/service-bus-shared-access-signature-authentication.md
[Azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
[overview of Service Bus samples]: ../service-bus-messaging/service-bus-samples.md
