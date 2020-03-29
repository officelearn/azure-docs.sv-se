---
title: Tjänståterbetering med Java i Azure Service Fabric
description: Med omstÃ¤nde av service fabric-omsÃ¤nde kan klienter och tjänster kommunicera med Java-tjänster med hjälp av ett fjÃ¤rrproceduranrop.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: eef63d7a2c8a4b15938dfbffd7db5f9d1b22d426
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426642"
---
# <a name="service-remoting-in-java-with-reliable-services"></a>Serviceåterbetering i Java med tillförlitliga tjänster
> [!div class="op_single_selector"]
> * [C# på Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java i Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

För tjänster som inte är kopplade till ett visst kommunikationsprotokoll eller en viss stack, till exempel WebAPI, Windows Communication Foundation (WCF) eller andra, tillhandahåller ramverket för tillförlitliga tjänster en remoting-mekanism för att snabbt och enkelt konfigurera fjärrprocedursamtal för Tjänster.  I den här artikeln beskrivs hur du ställer in fjärrproceduranrop för tjänster som är skrivna med Java.

## <a name="set-up-remoting-on-a-service"></a>Konfigurera ommotsättning på en tjänst
Ställa in ommotsättning för en tjänst görs i två enkla steg:

1. Skapa ett gränssnitt som tjänsten kan implementera. Det här gränssnittet definierar de metoder som är tillgängliga för ett fjärrproceduranrop på din tjänst. Metoderna måste vara asynkrona metoder som returneras av uppgift. Gränssnittet måste `microsoft.serviceFabric.services.remoting.Service` implementeras för att signalera att tjänsten har ett remoting-gränssnitt.
2. Använd en remoting lyssnare i din tjänst. Det här `CommunicationListener` är en implementering som tillhandahåller remoting-funktioner. `FabricTransportServiceRemotingListener`kan användas för att skapa en remoting lyssnare med hjälp av standard remoting transport protokoll.

Följande tillståndslösa tjänst exponerar till exempel en enda metod för att få "Hello World" över ett fjärrproceduranrop.

```java
import java.util.ArrayList;
import java.util.concurrent.CompletableFuture;
import java.util.List;
import microsoft.servicefabric.services.communication.runtime.ServiceInstanceListener;
import microsoft.servicefabric.services.remoting.Service;
import microsoft.servicefabric.services.runtime.StatelessService;

public interface MyService extends Service {
    CompletableFuture<String> helloWorldAsync();
}

class MyServiceImpl extends StatelessService implements MyService {
    public MyServiceImpl(StatelessServiceContext context) {
       super(context);
    }

    public CompletableFuture<String> helloWorldAsync() {
        return CompletableFuture.completedFuture("Hello!");
    }

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
        listeners.add(new ServiceInstanceListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));
        return listeners;
    }
}
```

> [!NOTE]
> Argumenten och returtyperna i tjänstgränssnittet kan vara enkla, komplexa eller anpassade typer, men de måste vara serialiserbara.
>
>

## <a name="call-remote-service-methods"></a>Anropa fjärrtjänstmetoder
Anropa metoder för en tjänst med hjälp av remoting stacken görs `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` med hjälp av en lokal proxy till tjänsten via klassen. Metoden `ServiceProxyBase` skapar en lokal proxy med samma gränssnitt som tjänsten implementerar. Med den proxyn kan du helt enkelt anropa metoder på gränssnittet på distans.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

Remoting-ramverket sprider undantag som genereras vid tjänsten till klienten. Så undantagshantering logik på `ServiceProxyBase` klienten med hjälp kan direkt hantera undantag som tjänsten kastar.

## <a name="service-proxy-lifetime"></a>Livslängd för tjänstproxy
ServiceProxy skapas är en lätt operation, så du kan skapa så många du behöver. Tjänstproxyinstanser kan återanvändas så länge de behövs. Om ett fjärrproceduranrop genererar ett undantag kan du fortfarande återanvända samma proxyinstans. Varje ServiceProxy innehåller en kommunikationsklient som används för att skicka meddelanden via kabeln. När fjärranrop anropas utförs interna kontroller för att avgöra om kommunikationsklienten är giltig. Baserat på resultaten av dessa kontroller återskapas kommunikationsklienten om det behövs. Om ett undantag inträffar behöver du `ServiceProxy`därför inte återskapa .

### <a name="serviceproxyfactory-lifetime"></a>Livslängd för ServiceProxyFactory
[FabricServiceProxyFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.fabricserviceproxyfactory) är en fabrik som skapar proxy för olika remoting-gränssnitt. Om du `ServiceProxyBase.create` använder API för att skapa `FabricServiceProxyFactory`proxy skapas ett .
Det är användbart att skapa en manuellt när du behöver åsidosätta Egenskaper för [ServiceRemotingClientFactory.](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.serviceremotingclientfactory)
Fabriken är en dyr operation. `FabricServiceProxyFactory`upprätthåller cache av kommunikationsklienter.
Bästa praxis är `FabricServiceProxyFactory` att cachelagra så länge som möjligt.

## <a name="remoting-exception-handling"></a>Hantering av ommotsättningsundantag
Alla fjärrundantag som genereras av tjänst-API skickas tillbaka till klienten antingen som RuntimeException eller FabricException.

ServiceProxy hanterar alla failover-undantag för den tjänstpartition som skapas för. Den löser slutpunkterna igen om det finns undantag för redundans (icke-tillfälliga undantag) och försöker igen samtalet med rätt slutpunkt. Antalet återförsök för failover Exception är obestämd.
Vid TransientExceptions försöker det bara samtalet.

Standardparametrar för återförsök är villkorade av [OperationRetrySettings](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.communication.client.operationretrysettings).
Du kan konfigurera dessa värden genom att skicka OperationRetrySettings-objektet till ServiceProxyFactory-konstruktor.

## <a name="next-steps"></a>Nästa steg
* [Säkra kommunikation för tillförlitliga tjänster](service-fabric-reliable-services-secure-communication-java.md)
