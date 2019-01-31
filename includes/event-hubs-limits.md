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
ms.openlocfilehash: 84a51f65ef46c390f84308ab17cf83859a16c3b8
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55428514"
---
I följande tabell visas kvoter och gränser specifika för [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Information om priser för Event Hubs finns i [priser för Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

| Gräns | Scope | Anteckningar | Värde |
| --- | --- | --- | --- | --- |
| Antal Event Hubs-namnområden per prenumeration |Prenumeration |- |1000 |
| Antal event-hubbar per namnområde |Namnområde |Efterföljande förfrågningar för att skapa en ny händelsehubb avvisas. |10 |
| Antalet partitioner per händelsehubb |Entitet |- |32 |
| Antal konsumentgrupper per händelsehubb |Entitet |- |20 |
| Antal AMQP-anslutningar per namnområde |Namnområde |Efterföljande begäranden om ytterligare anslutningar kommer att avvisas och ett undantag tas emot av den anropande koden. |5 000 |
| Maximal storlek för Event Hubs-händelse|Entitet |- |1 MB |
| Maximal storlek på en event hub-namn |Entitet |- |50 tecken |
| Antal icke-epoch mottagare per konsumentgrupp |Entitet |- |5 |
| Högsta bevarandeperioden av händelsedata |Entitet |- |1-7 dagar |
| Högsta antal Throughput Units |Namnområde |Throughput unit överskrids gör att dina data till att begränsas och genererar en  **[ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)**. Du kan begära ett större antal throughput units för en Standard nivå genom att skicka in en [supportförfrågan](/azure/azure-supportability/how-to-create-azure-support-request). [Ytterligare throughput units](../articles/event-hubs/event-hubs-auto-inflate.md) är tillgängliga i block om 20 på åtagandeköpsbasis. |20 |
| Antalet regler per namnområde |Namnområde|Efterföljande begäranden om skapande av auktorisering avvisas.|12 |
