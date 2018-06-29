---
title: Azure Relay portinställningar | Microsoft Docs
description: Information om Azure Relay-portarna.
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2018
ms.author: sethm
ms.openlocfilehash: 56688b6941d58c0ecc8d0ff4ba3c8a8392e71496
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029381"
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
|[BasicHttpRelayBinding klassen](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (service)|Antingen|9351/HTTP|  
|[NetEventRelayBinding klassen](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (klient)|Ja|9351/HTTPS|  
|" |Nej|9350/HTTP|  
|[NetEventRelayBinding klassen](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (service)|Antingen|9351/HTTP|  
|[NetTcpRelayBinding klassen](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (klienttjänsten)|Antingen|HTTP-5671/9352 (9352/9353, om du använder hybrid)|  
|[NetOnewayRelayBinding klassen](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (klient)|Ja|9351/HTTPS|  
|" |Nej|9350/HTTP|  
|[NetOnewayRelayBinding klassen](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (service)|Antingen|9351/HTTP|  
|[WebHttpRelayBinding klassen](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (klient)|Ja|HTTPS|  
|" |Nej|HTTP|  
|[WebHttpRelayBinding klassen](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (service)|Antingen|9351/HTTP|  
|[WS2007HttpRelayBinding klassen](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (klient)|Ja|HTTPS|  
|" |Nej|HTTP|  
|[WS2007HttpRelayBinding klassen](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (service)|Antingen|9351/HTTP|

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Relay finns i följande länkar:
* [Vad är Azure Relay?](relay-what-is-it.md)
* [Vanliga frågor och svar om Relay](relay-faq.md)