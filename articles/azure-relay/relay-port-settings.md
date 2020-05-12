---
title: Azure Relay port inställningar | Microsoft Docs
description: Den här artikeln innehåller en tabell som beskriver den konfiguration som krävs för port värden för Azure Relay.
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
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211767"
---
# <a name="azure-relay-port-settings"></a>Azure Relay port inställningar

I följande tabell beskrivs den konfiguration som krävs för port värden för Azure Relay.

## <a name="hybrid-connections"></a>Hybridanslutningar

Hybridanslutningar använder WebSockets på port 443 med TLS som underliggande transport funktion, som endast använder **https** . 

## <a name="wcf-relays"></a>WCF-reläer
  
|Bindning|Transport säkerhet|Port|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding-klass](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (klient)|Ja|HTTPS| 
|" |Inga|HTTP|  
|[BasicHttpRelayBinding-klass](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (tjänst)|Antingen|9351/HTTP|  
|[NetEventRelayBinding-klass](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (klient)|Ja|9351/HTTPS|  
|" |Inga|9350/HTTP|  
|[NetEventRelayBinding-klass](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (tjänst)|Antingen|9351/HTTP|  
|[NetTcpRelayBinding-klass](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (klient/tjänst)|Antingen|5671/9352/HTTP (9352/9353 om du använder hybrid)|  
|[NetOnewayRelayBinding-klass](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (klient)|Ja|9351/HTTPS|  
|" |Inga|9350/HTTP|  
|[NetOnewayRelayBinding-klass](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (tjänst)|Antingen|9351/HTTP|  
|[WebHttpRelayBinding-klass](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (klient)|Ja|HTTPS|  
|" |Inga|HTTP|  
|[WebHttpRelayBinding-klass](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (tjänst)|Antingen|9351/HTTP|  
|[WS2007HttpRelayBinding-klass](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (klient)|Ja|HTTPS|  
|" |Inga|HTTP|  
|[WS2007HttpRelayBinding-klass](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (tjänst)|Antingen|9351/HTTP|

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om Azure Relay går du till följande länkar:
* [Vad är Azure Relay?](relay-what-is-it.md)
* [Vanliga frågor och svar om Relay](relay-faq.md)