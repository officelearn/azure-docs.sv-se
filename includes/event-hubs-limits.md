---
title: ta med fil
description: ta med fil
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 02/26/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: ab4c5b98ed9f6fcc8c271797db2d81dcc7ec4449
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
ms.locfileid: "29717038"
---
I följande tabell visas kvoter och begränsar specifika för [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Information om priser för Händelsehubbar finns [priser för Händelsehubbar](https://azure.microsoft.com/pricing/details/event-hubs/).

| Gräns | Omfång | Anteckningar | Värde |
| --- | --- | --- | --- | --- |
| Antal händelsehubbar per namnområde |Namnrymd |Efterföljande förfrågningar för att skapa en ny händelsehubb avvisas. |10 |
| Antalet partitioner per händelsehubb |Entitet |- |32 |
| Antal konsumentgrupper per händelsehubb |Entitet |- |20 |
| Antalet AMQP anslutningar per namnområde |Namnrymd |Efterföljande begäranden om ytterligare anslutningar avvisas och ett undantag tas emot av den anropande koden. |5 000 |
| Maximal storlek för Händelsehubbar händelse|Entitet |- |256 kB |
| Maximal storlek för en händelsehubbens namn |Entitet |- |50 tecken |
| Antal icke-epok mottagare per konsumentgrupp |Entitet |- |5 |
| Högsta bevarandeperioden av händelsedata |Entitet |- |1-7 dagar |
| Högsta antal Throughput Units |Namnrymd |Genomströmning enhet överskrids gör att dina data så att begränsas och genererar en  **[ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)**. Du kan begära ett stort antal enheter för en Standard-lagret med ansökan en [supportbegäran](/azure/azure-supportability/how-to-create-azure-support-request). [Ytterligare genomflödesenheter](../articles/event-hubs/event-hubs-auto-inflate.md) är tillgängliga i block på 20 på bas allokerade inköp. |20 |
| Antal regler för auktorisering per namnområde |Namnrymd|Efterföljande förfrågningar för att skapa en regel för auktorisering avvisas.|12 |
