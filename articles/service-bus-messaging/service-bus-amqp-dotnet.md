---
title: Azure Service Bus med .NET och AMQP 1,0 | Microsoft Docs
description: Den här artikeln beskriver hur du använder Azure Service Bus från ett .NET-program med hjälp av AMQP (Advanced Messaging Queuing Protocol).
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 332bcb13-e287-4715-99ee-3d7d97396487
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 536c315077cb74a1dfa8db457f0f0b3725edf7a1
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759255"
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>Använda Service Bus från .NET med AMQP 1,0

AMQP 1,0-support finns i Service Bus-paket version 2,1 eller senare. Du kan se till att du har den senaste versionen genom att ladda ned Service Bus bitar från [NuGet][NuGet].

## <a name="configure-net-applications-to-use-amqp-10"></a>Konfigurera .NET-program för att använda AMQP 1,0

Som standard kommunicerar Service Bus .NET-klient biblioteket med Service Bus-tjänsten med hjälp av ett dedikerat SOAP-baserat protokoll. Om du vill använda AMQP 1,0 i stället för standard protokollet måste du uttryckligen konfigurera den Service Bus anslutnings strängen, enligt beskrivningen i nästa avsnitt. Förutom den här ändringen förblir program koden oförändrad när du använder AMQP 1,0.

I den aktuella versionen finns det några API-funktioner som inte stöds när du använder AMQP. Dessa funktioner som inte stöds visas i avsnittet [beteende skillnader](#behavioral-differences). Några av de avancerade konfigurations inställningarna har också en annan betydelse när du använder AMQP.

### <a name="configuration-using-appconfig"></a>Konfiguration med app. config

Det är en bra idé för program att använda konfigurations filen app. config för att lagra inställningar. För Service Bus-program kan du använda app. config för att lagra Service Bus anslutnings sträng. Ett exempel på en app. config-fil är följande:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

Värdet för inställningen `Microsoft.ServiceBus.ConnectionString` är den Service Bus anslutnings sträng som används för att konfigurera anslutningen till Service Bus. Formatet är följande:

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Där `namespace` och `SAS key` hämtas från [Azure Portal][Azure portal] när du skapar ett Service Bus-namnområde. Mer information finns i [skapa ett Service Bus-namnområde med hjälp av Azure Portal][Create a Service Bus namespace using the Azure portal].

När du använder AMQP lägger du till anslutnings strängen med `;TransportType=Amqp`. Den här notationen instruerar klient biblioteket att göra anslutningen till Service Bus med AMQP 1,0.

## <a name="message-serialization"></a>Meddelande serialisering

När du använder standard protokollet är standard sättet för serialisering av .NET-klient biblioteket att använda typen [DataContractSerializer][DataContractSerializer] för att serialisera en [BrokeredMessage][BrokeredMessage] -instans för transport mellan klient biblioteket och tjänsten Service Bus. När du använder AMQP transport läge använder klient biblioteket AMQP-typ systemet för serialisering av det sammanslagna [meddelandet][BrokeredMessage] i ett AMQP-meddelande. Den här serialiseringen gör att meddelandet kan tas emot och tolkas av ett mottagnings program som kan köras på en annan plattform, till exempel ett Java-program som använder JMS-API: et för att få åtkomst till Service Bus.

När du skapar en [BrokeredMessage][BrokeredMessage] -instans kan du ange ett .net-objekt som en parameter för konstruktorn som ska användas som brödtext i meddelandet. För objekt som kan mappas till primitiva typer av AMQP serialiseras bröd texten i AMQP-datatyper. Om objektet inte kan mappas direkt till en AMQP-primitiv typ; det vill säga en anpassad typ som definieras av programmet, därefter serialiseras objektet med hjälp av [DataContractSerializer][DataContractSerializer]och de serialiserade byte skickas i ett AMQP-datameddelande.

För att under lätta samverkan med non-.NET-klienter använder du bara .NET-typer som kan serialiseras direkt till AMQP-typer för meddelandets brödtext. I följande tabell beskrivs dessa typer och motsvarande mappning till AMQP-typ systemet.

| .NET-text objekts typ | Mappad AMQP-typ | Avsnitts typ för AMQP-brödtext |
| --- | --- | --- |
| bool |boolean |AMQP-värde |
| stor |ubyte |AMQP-värde |
| ushort |ushort |AMQP-värde |
| uint |uint |AMQP-värde |
| ulong |ulong |AMQP-värde |
| SByte |stor |AMQP-värde |
| short |short |AMQP-värde |
| int |int |AMQP-värde |
| long |long |AMQP-värde |
| float |float |AMQP-värde |
| double |double |AMQP-värde |
| decimal |decimal128 |AMQP-värde |
| char |char |AMQP-värde |
| DateTime |timestamp |AMQP-värde |
| GUID |uuid |AMQP-värde |
| byte[] |binary |AMQP-värde |
| sträng |sträng |AMQP-värde |
| System.Collections.IList |lista |AMQP-värde: objekt som ingår i samlingen kan bara vara de som har definierats i den här tabellen. |
| System. array |matris |AMQP-värde: objekt som ingår i samlingen kan bara vara de som har definierats i den här tabellen. |
| System.Collections.IDictionary |map |AMQP-värde: objekt som ingår i samlingen kan bara vara de som har definierats i den här tabellen. Obs! endast sträng nycklar stöds. |
| URI |Beskrivnings sträng (se följande tabell) |AMQP-värde |
| DateTimeOffset |Beskrivet långt (se följande tabell) |AMQP-värde |
| TimeSpan |Beskrivet långt (se följande) |AMQP-värde |
| Stream |binary |AMQP-data (kan vara flera). Data avsnitten innehåller rå byte som lästs från Stream-objektet. |
| Annat objekt |binary |AMQP-data (kan vara flera). Innehåller den serialiserade binärfilen för det objekt som använder DataContractSerializer eller en serialiserare som tillhandahålls av programmet. |

| .NET-typ | Mappad AMQP-beskrivande typ | Anteckningar |
| --- | --- | --- |
| URI |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |URI. AbsoluteUri |
| DateTimeOffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| TimeSpan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>` |TimeSpan. Tick |

## <a name="behavioral-differences"></a>Beteende skillnader

Det finns vissa små skillnader i hur Service Bus .NET API fungerar när du använder AMQP, jämfört med standard protokollet:

* Egenskapen [OperationTimeout][OperationTimeout] ignoreras.
* `MessageReceiver.Receive(TimeSpan.Zero)` implementeras som `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.
* Att slutföra meddelanden genom att låsa tokens kan bara utföras av de meddelande mottagare som ursprungligen tog emot meddelandena.

## <a name="control-amqp-protocol-settings"></a>Kontrol lera AMQP-protokoll inställningar

[.NET-API: erna](/dotnet/api/) visar flera inställningar för att kontrol lera beteendet för AMQP-protokollet:

* **[MessageReceiver. PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)** : styr den inledande kredit som tillämpas på en länk. Standardvärdet är 0.
* **[MessagingFactorySettings. AmqpTransportSettings. MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)** : styr den maximala storleken för AMQP som erbjuds under förhandlingen vid anslutnings tillfället. Standardvärdet är 65 536 byte.
* **[MessagingFactorySettings. AmqpTransportSettings. BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)** : om överföringar är batchable fastställer det här värdet den maximala fördröjningen för att skicka dispositioner. Ärvs av avsändare/mottagare som standard. Enskild avsändare/mottagare kan åsidosätta standardvärdet, vilket är 20 millisekunder.
* **[MessagingFactorySettings. AmqpTransportSettings. UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)** : styr om AMQP anslutningar upprättas via en SSL-anslutning. Standardvärdet är **True**.

## <a name="next-steps"></a>Nästa steg

Är du redo att lära dig mer? Besök följande länkar:

* [Översikt över Service Bus AMQP]
* [AMQP 1.0-protokollguide]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azureservicebus-4.0.0
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: https://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Översikt över Service Bus AMQP]: service-bus-amqp-overview.md
[AMQP 1.0-protokollguide]: service-bus-amqp-protocol-guide.md

