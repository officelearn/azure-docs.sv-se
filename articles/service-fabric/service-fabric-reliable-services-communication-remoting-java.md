---
title: Fjärrtjänst med Java i Azure Service Fabric | Microsoft Docs
description: Fjärrkommunikation för Service Fabric kan klienter och tjänster att kommunicera med Java-tjänster med hjälp av en RPC-anrop.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: eb991df64f0454fa6103c9104e5c0e9991503a43
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198277"
---
# <a name="service-remoting-in-java-with-reliable-services"></a>Fjärrkommunikation för tjänster i Java med Reliable Services
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java i Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

För tjänster som inte är kopplad till en viss kommunikationsprotokoll eller stack, till exempel WebAPI, Windows Communication Foundation (WCF) eller andra, tillhandahåller Reliable Services-ramverk en mekanism för fjärrkommunikation att snabbt och enkelt konfigurera RPC-anrop för tjänster.  Den här artikeln beskrivs hur du ställer in RPC-anrop för tjänster som skrivits med Java.

## <a name="set-up-remoting-on-a-service"></a>Ställa in fjärrstyrning på en tjänst
Hur du konfigurerar fjärrstyrning för en tjänst görs i två enkla steg:

1. Skapa ett gränssnitt för tjänsten att implementera. Det här gränssnittet definierar metoder som är tillgängliga för ett RPC-anrop till din tjänst. Metoderna måste vara aktiviteten returnerar asynkrona metoder. Gränssnittet måste implementera `microsoft.serviceFabric.services.remoting.Service` att signalera att tjänsten har ett gränssnitt för fjärrkommunikation.
2. Använd en lyssnare för fjärrkommunikation i din tjänst. Det här är en `CommunicationListener` implementering som tillhandahåller funktioner för fjärrkommunikation. `FabricTransportServiceRemotingListener` kan användas för att skapa en lyssnare för fjärrkommunikation med transportprotokollet standard fjärrkommunikation.

Till exempel exponerar följande tillståndslös tjänst en enkel metod för att få ”Hello World” med ett RPC-anrop.

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
> Argumenten och returtyper i tjänstegränssnittet kan vara några enkla, komplexa eller anpassade typer, men de måste kunna serialiseras.
>
>

## <a name="call-remote-service-methods"></a>Anropa fjärrtjänsten metoder
Anropa metoder på en tjänst med hjälp av fjärrkommunikation stack görs med hjälp av en lokal proxy till tjänsten via den `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` klass. Den `ServiceProxyBase` metoden skapar en lokal proxy genom att använda samma gränssnitt som implementerar tjänsten. Med den proxyn kan du helt enkelt anropa metoder på gränssnittet via en fjärranslutning.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

Fjärr-framework sprider undantag på tjänsten till klienten. Så undantagshantering logik på klienten med hjälp av `ServiceProxyBase` kan direkt hantera undantag som genereras av tjänsten.

## <a name="service-proxy-lifetime"></a>Tjänsten Proxylivslängden
Skapa en ServiceProxy är en enkel åtgärd så att du kan skapa så många som du behöver. Tjänstproxy instanser kan återanvändas så länge de behövs. Du kan återanvända samma proxy-instans även om ett RPC genererar ett undantag. Varje ServiceProxy innehåller en klient för kommunikation som används för att skicka meddelanden över nätverket. Samtidigt som du anropar fjärranrop utförs interna kontroller för att fastställa om kommunikation klienten är giltig. Baserat på resultatet av dessa kontroller, återskapas kommunikation klienten om det behövs. Därför, om ett undantag inträffar, du behöver inte återskapa `ServiceProxy`.

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory livslängd
[FabricServiceProxyFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.fabric_service_proxy_factory) är en fabrik som skapar proxy för olika fjärrkommunikation gränssnitt. Om du använder API `ServiceProxyBase.create` för att skapa proxy, framework skapar sedan en `FabricServiceProxyFactory`.
Det är bra att skapa ett manuellt när du behöver åsidosätta [ServiceRemotingClientFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.service_remoting_client_factory) egenskaper.
Factory är en kostsam åtgärd. `FabricServiceProxyFactory` underhåller cache av klienter för kommunikation.
Bra tips är att cacheminnet `FabricServiceProxyFactory` så länge som möjligt.

## <a name="remoting-exception-handling"></a>Fjärrkommunikation undantagshantering
Alla fjärranslutna undantag utlöstes av tjänsten API, skickas tillbaka till klienten som RuntimeException eller FabricException.

ServiceProxy hanterar alla redundans undantag för service-partition som den har skapats för. Det igen löser slutpunkterna om det finns Failover Exceptions(Non-Transient Exceptions) och försöker anropet med rätt slutpunkt. Antal återförsök för redundans undantag är obestämd.
När det gäller TransientExceptions, endast ett nytt försök görs anropet.

Återförsök standardparametrarna är etablerar genom att [OperationRetrySettings]. (https://docs.microsoft.com/java/api/microsoft.servicefabric.services.communication.client.operation_retry_settings) Du kan konfigurera dessa värden genom att skicka OperationRetrySettings objekt till ServiceProxyFactory konstruktorn.

## <a name="next-steps"></a>Nästa steg
* [Säker kommunikation för Reliable Services](service-fabric-reliable-services-secure-communication-java.md)
