---
title: Inställningar för Azure Relay-port | Microsoft-dokument
description: Den här artikeln innehåller en tabell som beskriver den konfiguration som krävs för portvärden för Azure Relay.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 532e3c297c42ea16b1f462a01699fc2fd71c6cce
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529116"
---
# <a name="azure-relay-port-settings"></a>Inställningar för Azure Relay-port

I följande tabell beskrivs den konfiguration som krävs för portvärden för Azure Relay.

## <a name="hybrid-connections"></a>Hybridanslutningar

Hybridanslutningar använder WebSockets på port 443 med TLS som underliggande transportmekanism, som endast använder **HTTPS.** 

## <a name="wcf-relays"></a>WCF-reläer
  
|Bindning|Transportsäkerhet|Port|  
|-------------|------------------------|----------|  
|[Grundläggande klass förhttprelaybinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (klient)|Ja|HTTPS| 
|" |Inga|HTTP|  
|[Grundläggande klass förhttprelaybinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (tjänst)|Antingen|9351/HTTP|  
|[NetEventRelayBinding-klass](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (klient)|Ja|9351/HTTPS|  
|" |Inga|9350/HTTP|  
|[NetEventRelayBinding-klass](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (tjänst)|Antingen|9351/HTTP|  
|[NetTcpRelayBinding-klass](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (klient/tjänst)|Antingen|5671/9352/HTTP (9352/9353 om du använder hybrid)|  
|[NetOnewayRelayBinding-klass](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (klient)|Ja|9351/HTTPS|  
|" |Inga|9350/HTTP|  
|[NetOnewayRelayBinding-klass](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (tjänst)|Antingen|9351/HTTP|  
|[WebbhttpRelayBinding-klass](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (klient)|Ja|HTTPS|  
|" |Inga|HTTP|  
|[WebbhttpRelayBinding-klass](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (tjänst)|Antingen|9351/HTTP|  
|[WS2007HttpRelayBinding-klass](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (klient)|Ja|HTTPS|  
|" |Inga|HTTP|  
|[WS2007HttpRelayBinding-klass](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (tjänst)|Antingen|9351/HTTP|

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Relay finns i följande länkar:
* [Vad är Azure Relay?](relay-what-is-it.md)
* [Vanliga frågor och svar om Relay](relay-faq.md)