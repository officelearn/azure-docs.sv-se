---
title: Azure Relay-portinställningar | Microsoft Docs
description: Information om Azure Relay-portarna.
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
ms.date: 06/26/2018
ms.author: spelluru
ms.openlocfilehash: 9d11179a8518ebf48f68f8607f94e0253d4edb80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789934"
---
# <a name="azure-relay-port-settings"></a>Azure Relay-portinställningar

I följande tabell beskrivs nödvändig konfiguration av portarna för Azure Relay.

## <a name="hybrid-connections"></a>Hybridanslutningar

Hybridanslutningar använder WebSockets på port 443 med SSL som den underliggande transportmekanismen som använder **HTTPS** endast. 

## <a name="wcf-relays"></a>WCF-reläer
  
|Bindning|Transportsäkerhet|Port|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding klassen](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (klient)|Ja|HTTPS| 
|" |Nej|HTTP|  
|[BasicHttpRelayBinding klassen](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (tjänst)|Antingen|9351/HTTP|  
|[NetEventRelayBinding klassen](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (klient)|Ja|9351/HTTPS|  
|" |Nej|9350/HTTP|  
|[NetEventRelayBinding klassen](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (tjänst)|Antingen|9351/HTTP|  
|[NetTcpRelayBinding klassen](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (klienttjänsten)|Antingen|5671/9352/HTTP (9352/9353, om du använder hybrid)|  
|[NetOnewayRelayBinding klassen](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (klient)|Ja|9351/HTTPS|  
|" |Nej|9350/HTTP|  
|[NetOnewayRelayBinding klassen](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (tjänst)|Antingen|9351/HTTP|  
|[WebHttpRelayBinding klassen](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (klient)|Ja|HTTPS|  
|" |Nej|HTTP|  
|[WebHttpRelayBinding klassen](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (tjänst)|Antingen|9351/HTTP|  
|[WS2007HttpRelayBinding klassen](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (klient)|Ja|HTTPS|  
|" |Nej|HTTP|  
|[WS2007HttpRelayBinding klassen](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (tjänst)|Antingen|9351/HTTP|

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Relay finns i följande länkar:
* [Vad är Azure Relay?](relay-what-is-it.md)
* [Vanliga frågor och svar om Relay](relay-faq.md)