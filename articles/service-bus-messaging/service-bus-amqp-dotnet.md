---
title: Service Bus med .NET och AMQP 1.0 | Microsoft Docs
description: "Med hjälp av Azure Service Bus från .NET med AMQP"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 332bcb13-e287-4715-99ee-3d7d97396487
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: 0eb68c97ca26a862a79de9ffb83b1fc630ba2af4
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2017
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>Använda Service Bus från .NET med AMQP 1.0

AMQP 1.0-support är tillgänglig i Service Bus paketets version 2.1 eller senare. Du kan kontrollera att du har den senaste versionen genom att hämta Service Bus-bits från [NuGet][NuGet].

## <a name="configure-net-applications-to-use-amqp-10"></a>Konfigurera .NET-program att använda AMQP 1.0

Som standard kommunicerar Service Bus .NET-klientbibliotek med tjänsten Service Bus via en dedikerad SOAP-baserat protokoll. Om du vill använda AMQP 1.0 istället för standard kräver protokollet explicit konfigurationen på Service Bus-anslutningssträng, enligt beskrivningen i nästa avsnitt. Förutom den här ändringen ändras programkod inte när du använder AMQP 1.0.

Det finns några API-funktioner som inte stöds när du använder AMQP i den aktuella versionen. Dessa funktioner som inte stöds visas senare i avsnittet [stöds inte funktioner, begränsningar och beteendebaserade skillnader](#unsupported-features-restrictions-and-behavioral-differences). Några av de avancerade inställningarna kan också ha en annan betydelse när du använder AMQP.

### <a name="configuration-using-appconfig"></a>Konfiguration med hjälp av App.config

Det är bra för program att använda konfigurationsfilen App.config för att lagra inställningar. Du kan använda App.config för att lagra Service Bus-anslutningssträng för Service Bus-program. En exempel App.config-fil är följande:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

Värdet för den `Microsoft.ServiceBus.ConnectionString` inställningen är Service Bus-anslutningssträng som används för att konfigurera anslutningen till Service Bus. Formatet är:

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Där `namespace` och `SAS key` hämtas från den [Azure-portalen] [ Azure portal] när du skapar en Service Bus-namnrymd. Mer information finns i [skapa ett namnområde för Service Bus med hjälp av Azure portal][Create a Service Bus namespace using the Azure portal].

När du använder AMQP lägga anslutningssträngen med `;TransportType=Amqp`. Notation instruerar klientbiblioteket för att kontrollera anslutningen till Service Bus använder AMQP 1.0.

## <a name="message-serialization"></a>Meddelande-serialisering

När du använder standardprotokoll standardbeteendet för serialisering av klientbiblioteket för .NET är att använda den [DataContractSerializer] [ DataContractSerializer] typ att serialisera en [BrokeredMessage] [ BrokeredMessage] instans för överföring mellan klientbiblioteket och Service Bus-tjänsten. När du använder AMQP-transportläge klientbiblioteket använder AMQP typsystemet för serialisering av den [asynkrona meddelanden] [ BrokeredMessage] i en AMQP-meddelande. Den här serialisering gör meddelandet som ska tas emot och tolkas av ett mottagande program som körs kan vara på en annan plattform, till exempel ett Java-program som använder JMS-API för att komma åt Service Bus.

När du skapar en [BrokeredMessage] [ BrokeredMessage] instans, kan du ange ett .NET-objekt som parameter till konstruktorn för att fungera som en del av meddelandet. För objekt som kan mappas till AMQP primitiva typer, serialiseras brödtexten till AMQP-datatyper. Om objektet inte kan mappas direkt till en primitiv typ AMQP; det vill säga en anpassad typ definieras av programmet och sedan objektet serialiseras med hjälp av den [DataContractSerializer][DataContractSerializer], och de serialiserade byte skickas i ett AMQP data visas.

Använd endast .NET-typer som kan serialiseras direkt till AMQP typer för innehållet i meddelandet för att underlätta samverkan med icke-.NET-klienter. I följande tabell beskrivs dessa typer och motsvarande mappningen till AMQP typsystemet.

| Objekttyp för .NET brödtext | Mappade AMQP typ | Typen av AMQP brödtext avsnitt |
| --- | --- | --- |
| bool |boolesk |AMQP värde |
| byte |ubyte |AMQP värde |
| ushort |ushort |AMQP värde |
| uint |uint |AMQP värde |
| ulong |ulong |AMQP värde |
| sbyte |byte |AMQP värde |
| kort |kort |AMQP värde |
| int |int |AMQP värde |
| lång |lång |AMQP värde |
| flyt |flyt |AMQP värde |
| dubbel |dubbel |AMQP värde |
| Decimal |decimal128 |AMQP värde |
| Char |Char |AMQP värde |
| DateTime |tidsstämpel |AMQP värde |
| GUID |UUID |AMQP värde |
| byte] |Binär |AMQP värde |
| sträng |sträng |AMQP värde |
| System.Collections.IList |lista |AMQP värde: objekt i samlingen får endast vara de som definieras i den här tabellen. |
| System.Array |matris |AMQP värde: objekt i samlingen får endast vara de som definieras i den här tabellen. |
| System.Collections.IDictionary |karta |AMQP värde: objekt i samlingen får endast vara de som definieras i den här tabellen. Obs: endast strängnycklar stöds. |
| URI |Beskrivs sträng (se nedan) |AMQP värde |
| DateTimeOffset |Beskrivs långt (se nedan) |AMQP värde |
| TimeSpan |Beskrivs långt (se följande) |AMQP värde |
| Stream |Binär |AMQP Data (kan vara flera). Data-avsnitt innehåller rå byte som läses från Stream-objektet. |
| Andra objekt |Binär |AMQP Data (kan vara flera). Innehåller serialiserade binärfilen i objektet som använder DataContractSerializer eller en serialiserare som tillhandahålls av programmet. |

| .NET-typ | Mappade AMQP beskrivs typen | Anteckningar |
| --- | --- | --- |
| URI |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.AbsoluteUri |
| DateTimeOffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| TimeSpan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> ` |TimeSpan.Ticks |

## <a name="unsupported-features-restrictions-and-behavioral-differences"></a>Funktioner som inte stöds, begränsningar och beteendebaserade skillnader

Följande funktioner i Service Bus .NET-API stöds inte för närvarande när du använder AMQP:

* Transaktioner
* Skicka via överföring mål

Det finns också några mindre skillnader i beteendet för Service Bus .NET-API när du använder AMQP, jämfört med standardprotokoll:

* Den [OperationTimeout] [ OperationTimeout] egenskapen ignoreras.
* `MessageReceiver.Receive(TimeSpan.Zero)`implementeras som `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.
* Slutför meddelanden av Lås token kan bara utföras av mottagarna meddelandet som ursprungligen emot meddelanden.

## <a name="control-amqp-protocol-settings"></a>Inställningar för åtkomstkontroll AMQP-protokollet

Den [.NET API: er](/dotnet/api/) exponera flera inställningar för att styra beteendet för AMQP-protokollet:

* **[MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)**: styr den inledande kredit som tillämpas på en länk. Standardvärdet är 0.
* **[MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)**: kontroller som den maximala storleken för AMQP ram erbjuds under förhandling vid anslutning öppna tid. Standardvärdet är 65 536 byte.
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)**: om överföringar är batchable det här värdet anger den längsta tillåtna fördröjningen för att skicka disposition. Ärvs av avsändare/mottagare som standard. Enskilda avsändare/mottagare kan åsidosätta standard, vilket är 20 millisekunder.
* **[MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)**: styr om AMQP anslutningar upprättas via en SSL-anslutning. Standardvärdet är **SANT**.

## <a name="next-steps"></a>Nästa steg

Är du redo att lära dig mer? Besök följande länkar:

* [Översikt över Service Bus AMQP]
* [AMQP 1.0-protokollguide]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azureservicebus-4.0.0
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Översikt över Service Bus AMQP]: service-bus-amqp-overview.md
[AMQP 1.0-protokollguide]: service-bus-amqp-protocol-guide.md

