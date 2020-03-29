---
title: Skalbarhet och prestanda - Personalizer
titleSuffix: Azure Cognitive Services
description: 'Högpresterande och högtrafikerade webbplatser och program har två huvudfaktorer att tänka på med Personalizer för skalbarhet och prestanda: svarstid och utbildningsgenomströmning.'
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: 5ac9a870cb05328f040febd0f8161a97f0982e09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73490786"
---
# <a name="scalability-and-performance"></a>Skalbarhet och prestanda

Högpresterande och högtrafikerade webbplatser och program har två huvudfaktorer att tänka på med Personalizer för skalbarhet och prestanda:

* Hålla låg latens när du ringer Rank API-anrop
* Se till att utbildningsdataflödet håller jämnare med händelseinmatningen

Anpassning kan returnera en rang snabbt, med de flesta av samtalstiden tillägnad kommunikation via REST API. Azure kommer automatiskt att skala möjligheten att svara på begäranden snabbt.

##  <a name="low-latency-scenarios"></a>Scenarier med låg latens

Vissa program kräver låga fördröjningar när du returnerar en rang. Låga svarstider är nödvändiga:

* För att hindra användaren från att vänta en märkbar tid innan den visar rangordnat innehåll.
* För att hjälpa en server som upplever extrem trafik undvika att binda upp knappa beräkningstid och nätverksanslutningar.


## <a name="scalability-and-training-throughput"></a>Skalbarhet och utbildningsgenomströmning

Personalizer fungerar genom att uppdatera en modell som tränas om baserat på meddelanden som skickas asynkront av Personalizer efter Rank och Reward API:er. Dessa meddelanden skickas med hjälp av en Azure EventHub för programmet.

 Det är osannolikt att de flesta program kommer att nå maximal sammanfogning och utbildning genomströmning av Personalizer. Även om du når det här maxvärdet kommer inte att sakta ner programmet, skulle det innebära att Event Hub-köerna fylls internt snabbare än de kan rensas upp.

## <a name="how-to-estimate-your-throughput-requirements"></a>Så här beräknar du dina dataflödeskrav

* Uppskatta det genomsnittliga antalet byte per rangordningshändelse som lägger till längden på kontext- och åtgärds-JSON-dokumenten.
* Dividera 20 MB/sek med denna uppskattade genomsnittliga byte.

Om din genomsnittliga nyttolast till exempel har 500 funktioner och var och en är uppskattningsvis 20 tecken, är varje händelse cirka 10kb. Med dessa uppskattningar, 20.000.000 / 10.000 = 2.000 händelser / sek, vilket är cirka 173 miljoner händelser / dag. 

Om du når dessa gränser, vänligen kontakta vårt supportteam för arkitekturråd.

## <a name="next-steps"></a>Nästa steg

[Skapa och konfigurera Personalizer](how-to-settings.md).