---
title: Azure Service Bus med .NET och AMQP 1.0 | Microsoft Docs
description: Med hjälp av Azure Service Bus från .NET med AMQP
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 332bcb13-e287-4715-99ee-3d7d97396487
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2018
ms.author: spelluru
ms.openlocfilehash: ad789b7a65fd12abb2a6e92c7c8896677de80cec
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702246"
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>Använda Service Bus från .NET med AMQP 1.0

Stöd för AMQP 1.0 är tillgänglig i Service Bus-Paketversion 2.1 eller senare. Du kan kontrollera att du har den senaste versionen genom att hämta Service Bus-bits från [NuGet][NuGet].

## <a name="configure-net-applications-to-use-amqp-10"></a>Konfigurera .NET-program att använda AMQP 1.0

Service Bus .NET-klientbiblioteket kommunicerar med Service Bus-tjänsten med hjälp av en dedikerad SOAP-baserat protokoll som standard. Om du vill använda AMQP 1.0 i stället för standardvärdet kräver protokollet explicit konfiguration av Service Bus-anslutningssträngen, enligt beskrivningen i nästa avsnitt. Förutom den här ändringen ändras programkod inte när du använder AMQP 1.0.

Det finns några API-funktioner som inte stöds när du använder AMQP i den aktuella versionen. Dessa funktioner som inte stöds finns i avsnittet [Beteendeanalys skillnader](#behavioral-differences). Några av inställningarna för avancerad konfiguration kan också ha en annan betydelse när du använder AMQP.

### <a name="configuration-using-appconfig"></a>Konfiguration med hjälp av App.config

Det är bra för program att använda konfigurationsfilen App.config för att lagra inställningar. Du kan använda App.config för att lagra Service Bus-anslutningssträng för Service Bus-appar. En exempel App.config-fil är följande:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

Värdet för den `Microsoft.ServiceBus.ConnectionString` inställningen är Service Bus-anslutningssträngen som används för att konfigurera anslutningen till Service Bus. Formatet är följande:

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Där `namespace` och `SAS key` hämtas från den [Azure-portalen] [ Azure portal] när du skapar ett Service Bus-namnområde. Mer information finns i [skapa ett Service Bus-namnområde med Azure-portalen][Create a Service Bus namespace using the Azure portal].

När du använder AMQP, lägger du till anslutningssträngen med `;TransportType=Amqp`. Den här notationen instruerar klientbibliotek för att kontrollera anslutningen till Service Bus med hjälp av AMQP 1.0.

## <a name="message-serialization"></a>Meddelande-serialisering

När du använder standardprotokoll, standardbeteendet för serialisering av klientbiblioteket för .NET är att använda den [DataContractSerializer] [ DataContractSerializer] typ att serialisera en [BrokeredMessage] [ BrokeredMessage] instans för transport mellan klientbiblioteket och Service Bus-tjänsten. När du använder AMQP-transportläge klientbiblioteket använder AMQP-typsystemet för serialisering av den [asynkrona meddelanden] [ BrokeredMessage] i en AMQP-meddelande. Den här serialiseringen gör det möjligt för meddelandet som ska tas emot och tolkas av ett mottagande program som potentiellt som körs på en annan plattform, till exempel ett Java-program som använder JMS API för att få åtkomst till Service Bus.

När du skapar en [BrokeredMessage] [ BrokeredMessage] instans kan du ange ett .NET-objekt som en parameter till konstruktorn som fungerar som meddelandets brödtext. För objekt som kan mappas till AMQP primitiva typer, serialiseras brödtexten till AMQP-datatyper. Om objektet inte kan mappas direkt till en AMQP primitiv typ. det vill säga en anpassad typ definieras av programmet och sedan objektet serialiseras med hjälp av den [DataContractSerializer][DataContractSerializer], och de serialiserade byte skickas ett meddelande för AMQP-data.

Använd endast .NET-typer som kan serialiseras direkt till AMQP-typer för brödtexten i meddelandet för att underlätta samverkan med icke-.NET-klienter. I följande tabell beskrivs dessa typer och motsvarande mappningen till AMQP-typsystemet.

| .NET body objekttyp | Mappade AMQP-typ | AMQP-avsnittet strängtypen |
| --- | --- | --- |
| Bool |boolesk |AMQP-värde |
| byte |ubyte |AMQP-värde |
| ushort |ushort |AMQP-värde |
| uint |uint |AMQP-värde |
| ulong |ulong |AMQP-värde |
| sbyte |byte |AMQP-värde |
| kort |kort |AMQP-värde |
| int |int |AMQP-värde |
| lång |lång |AMQP-värde |
| flyt |flyt |AMQP-värde |
| double |double |AMQP-värde |
| decimaltal |decimal128 |AMQP-värde |
| Char |Char |AMQP-värde |
| DateTime |tidsstämpel |AMQP-värde |
| GUID |UUID |AMQP-värde |
| byte |binär |AMQP-värde |
| sträng |sträng |AMQP-värde |
| System.Collections.IList |lista |AMQP-värde: objekt som ingår i samlingen får bara vara de som definieras i den här tabellen. |
| System.Array |matris |AMQP-värde: objekt som ingår i samlingen får bara vara de som definieras i den här tabellen. |
| System.Collections.IDictionary |Karta |AMQP-värde: objekt som ingår i samlingen får bara vara de som definieras i den här tabellen. Obs: endast strängnycklar stöds. |
| URI |Beskrivningen sträng (se nedan) |AMQP-värde |
| DateTimeOffset |Beskrivningen länge (se nedan) |AMQP-värde |
| Tidsintervall |Beskrivningen länge (se följande) |AMQP-värde |
| Stream |binär |AMQP-Data (kan vara flera). Data-avsnitt innehåller rå byte som läses från Stream-objektet. |
| Andra objekt |binär |AMQP-Data (kan vara flera). Innehåller den serialiserade binära filen på det objekt som använder DataContractSerializer eller en serialiserare som tillhandahålls av programmet. |

| .NET-typ | Mappade AMQP beskrivs typ | Anteckningar |
| --- | --- | --- |
| URI |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.AbsoluteUri |
| DateTimeOffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| Tidsintervall |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> ` |TimeSpan.Ticks |

## <a name="behavioral-differences"></a>Beteendeanalys skillnader

Det finns några mindre skillnader i beteendet för Service Bus .NET API när du använder AMQP, jämfört med standardprotokoll:

* Den [OperationTimeout] [ OperationTimeout] egenskapen ignoreras.
* `MessageReceiver.Receive(TimeSpan.Zero)` implementeras som `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.
* Slutföra meddelanden genom att Lås token kan bara utföras av meddelande-mottagare som ursprungligen emot meddelanden.

## <a name="control-amqp-protocol-settings"></a>Kontrollinställningar för AMQP-protokollet

Den [.NET API: er](/dotnet/api/) exponera flera inställningar för att styra beteendet för AMQP-protokollet:

* **[MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)**: styr initial kredit som tillämpas på en länk. Standardvärdet är 0.
* **[MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)**: kontroller maxstorleken för AMQP-ram som erbjuds under förhandling vid anslutning öppna tid. Standardvärdet är 65 536 byte.
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)**: om överföringar är batchable, det här värdet fastställer den längsta tillåtna fördröjningen för att skicka disposition. Ärvs av avsändare/mottagare som standard. Enskilda avsändare/mottagare kan åsidosätta standardvärdet, vilket är 20 millisekunder.
* **[MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)**: styr om AMQP-anslutningar upprättas via en SSL-anslutning. Standardvärdet är **SANT**.

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

