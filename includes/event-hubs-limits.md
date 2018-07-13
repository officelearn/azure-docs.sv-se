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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38756119"
---
I följande tabell visas kvoter och gränser specifika för [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Information om priser för Event Hubs finns i [priser för Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

| Gräns | Omfång | Anteckningar | Värde |
| --- | --- | --- | --- | --- |
| Antal event-hubbar per namnområde |Namnrymd |Efterföljande förfrågningar för att skapa en ny händelsehubb avvisas. |10 |
| Antalet partitioner per händelsehubb |Entitet |- |32 |
| Antal konsumentgrupper per händelsehubb |Entitet |- |20 |
| Antal AMQP-anslutningar per namnområde |Namnrymd |Efterföljande begäranden om ytterligare anslutningar kommer att avvisas och ett undantag tas emot av den anropande koden. |5 000 |
| Maximal storlek för Event Hubs-händelse|Entitet |- |256 kB |
| Maximal storlek på en event hub-namn |Entitet |- |50 tecken |
| Antal icke-epoch mottagare per konsumentgrupp |Entitet |- |5 |
| Högsta bevarandeperioden av händelsedata |Entitet |- |1-7 dagar |
| Högsta antal Throughput Units |Namnrymd |Throughput unit överskrids gör att dina data till att begränsas och genererar en  **[ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)**. Du kan begära ett större antal throughput units för en Standard nivå genom att skicka in en [supportförfrågan](/azure/azure-supportability/how-to-create-azure-support-request). [Ytterligare throughput units](../articles/event-hubs/event-hubs-auto-inflate.md) är tillgängliga i block om 20 på åtagandeköpsbasis. |20 |
| Antalet regler per namnområde |Namnrymd|Efterföljande begäranden om skapande av auktorisering avvisas.|12 |
