---
title: Azure Service Bus med .NET och AMQP 1.0 | Microsoft-dokument
description: I den hÃ¤r artikeln beskrivs hur du anvÃ¤nder Azure Service Bus frÃ¤r ett .NET-program med AMQP (Advanced Messaging Queuing Protocol).
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
ms.openlocfilehash: 8157efac5ff1fc135659a84b4f4825ff36307480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297658"
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>Använd servicebuss från .NET med AMQP 1.0

AMQP 1.0-supporten finns i Service Bus-paketversionen 2.1 eller senare. Du kan se till att du har den senaste versionen genom att ladda ner Service Bus bitar från [NuGet][NuGet].

## <a name="configure-net-applications-to-use-amqp-10"></a>Konfigurera .NET-program för att använda AMQP 1.0

Som standard kommunicerar Service Bus .NET-klientbiblioteket med Service Bus-tjänsten med ett dedikerat SOAP-baserat protokoll. Om du vill använda AMQP 1.0 i stället för standardprotokollet krävs explicit konfiguration på anslutningssträngen för Service Bus, enligt beskrivningen i nästa avsnitt. Förutom den här ändringen förblir programkoden oförändrad när AMQP 1.0 med hjälp av AMQP 1.0.

I den aktuella versionen finns det några API-funktioner som inte stöds när du använder AMQP. Dessa funktioner som inte stöds visas i avsnittet [Beteendeskillnader](#behavioral-differences). Några av de avancerade konfigurationsinställningarna har också en annan innebörd när du använder AMQP.

### <a name="configuration-using-appconfig"></a>Konfiguration med App.config

Det är en bra idé för program att använda konfigurationsfilen App.config för att lagra inställningar. För Service Bus-program kan du använda App.config för att lagra anslutningssträngen för servicebuss. Ett exempel på App.config-fil är följande:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

Värdet för `Microsoft.ServiceBus.ConnectionString` inställningen är anslutningssträngen för Service Bus som används för att konfigurera anslutningen till Service Bus. Formatet är följande:

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Var `namespace` `SAS key` och hämtas från [Azure-portalen][Azure portal] när du skapar ett servicebussnamnområde. Mer information finns i [Skapa ett tjänstbussnamnområde med Azure-portalen][Create a Service Bus namespace using the Azure portal].

När du använder AMQP lägger `;TransportType=Amqp`du till anslutningssträngen med . Den här notationen instruerar klientbiblioteket att ansluta till Service Bus med AMQP 1.0.

## <a name="message-serialization"></a>Seriellning av meddelanden

När standardprotokollet används är standardserieringsbeteendet för .NET-klientbiblioteket att använda [datacontractserializer-typen][DataContractSerializer] för att serialisera en [BrokeredMessage-instans][BrokeredMessage] för transport mellan klientbiblioteket och tjänsten Service Bus. När du använder AMQP-transportsättet använder klientbiblioteket amqp-typsystemet för serialisering av det [förmedlade meddelandet][BrokeredMessage] i ett AMQP-meddelande. Den här serialiseringen gör att meddelandet kan tas emot och tolkas av ett mottagande program som eventuellt körs på en annan plattform, till exempel ett Java-program som använder JMS API för att komma åt Service Bus.

När du skapar en [BrokeredMessage-förekomst][BrokeredMessage] kan du ange ett .NET-objekt som en parameter till konstruktorn som ska fungera som brödtext i meddelandet. För objekt som kan mappas till AMQP primitiva typer serialiseras brödtexten till AMQP-datatyper. Om objektet inte kan mappas direkt till en primitiv AMQP-typ. det vill ha en anpassad typ som definieras av programmet, sedan serialiseras objektet med [DataContractSerializer][DataContractSerializer]och serialiserade byte skickas i ett AMQP-datameddelande.

Om du vill underlätta interoperabilitet med non-.NET klienter använder du endast .NET-typer som kan serialiseras direkt i AMQP-typer för meddelandets brödtext. I följande tabell beskrivs dessa typer och motsvarande mappning till AMQP-typsystemet.

| .NET-brödtextobjekttyp | Mappad AMQP-typ | Amqp-brödtextavsnittstyp |
| --- | --- | --- |
| bool |boolean |AMQP-värde |
| byte |ubyte (på) |AMQP-värde |
| ushort (ushort) |ushort (ushort) |AMQP-värde |
| uint |uint |AMQP-värde |
| ulong (ulong) |ulong (ulong) |AMQP-värde |
| sbyte (sbyte) |byte |AMQP-värde |
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
| System.Collections.IList |lista |AMQP-värde: objekt som ingår i samlingen kan bara vara de som definieras i den här tabellen. |
| System.Array |matris |AMQP-värde: objekt som ingår i samlingen kan bara vara de som definieras i den här tabellen. |
| System.Collections.IDictionary |map |AMQP-värde: objekt som ingår i samlingen kan bara vara de som definieras i den här tabellen. Endast strängtangenter stöds. |
| Uri |Beskriven sträng(se följande tabell) |AMQP-värde |
| DateTimeOffset |Beskriven long(se följande tabell) |AMQP-värde |
| TimeSpan |Beskriven long(se följande) |AMQP-värde |
| Strömma |binary |AMQP-data (kan vara flera). Dataavsnitten innehåller de råa byte som lästs från Stream-objektet. |
| Annat objekt |binary |AMQP-data (kan vara flera). Innehåller den serialiserade binärfilen för objektet som använder DataContractSerializer eller en serialiserare som tillhandahålls av programmet. |

| .NET-typ | Beskrivd typ av mappad AMQP | Anteckningar |
| --- | --- | --- |
| Uri |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.AbsoluteUri |
| DateTimeOffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| TimeSpan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>` |TimeSpan.Fästingar |

## <a name="behavioral-differences"></a>Beteendemässiga skillnader

Det finns några små skillnader i beteendet för Service Bus .NET API när du använder AMQP, jämfört med standardprotokollet:

* Egenskapen [OperationTimeout][OperationTimeout] ignoreras.
* `MessageReceiver.Receive(TimeSpan.Zero)`genomförs som `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.
* Slutföra meddelanden med låstoken kan endast göras av meddelandemottagare som ursprungligen tog emot meddelandena.

## <a name="control-amqp-protocol-settings"></a>Styra AMQP-protokollinställningar

[.NET API:erna](/dotnet/api/) visar flera inställningar för att styra amqp-protokollets beteende:

* **[MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)**: Styr den ursprungliga krediten som tillämpas på en länk. Standardvärdet är 0.
* **[MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)**: Styr den maximala AMQP-bildrutan som erbjuds under förhandlingen vid öppen anslutningstid. Standardvärdet är 65 536 byte.
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)**: Om överföringar är batchable, bestämmer det här värdet den maximala fördröjningen för att skicka dispositioner. Ärvt av avsändare/mottagare som standard. Enskild avsändare/mottagare kan åsidosätta standardvärdet, som är 20 millisekunder.
* **[MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)**: Styr om AMQP-anslutningar upprättas via en TLS-anslutning. Standardinställningen är **sann**.

## <a name="next-steps"></a>Nästa steg

Vill du lära dig mer? Besök följande länkar:

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

