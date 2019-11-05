---
title: Skalbarhet och prestanda – Personanpassare
titleSuffix: Azure Cognitive Services
description: 'Webbplatser och program med höga prestanda och hög trafik har två huvudsakliga faktorer att tänka på när det gäller skalbarhet och prestanda: svars tid och inlärnings data flöde.'
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: 5ac9a870cb05328f040febd0f8161a97f0982e09
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490786"
---
# <a name="scalability-and-performance"></a>Skalbarhet och prestanda

Webbplatser och program med höga prestanda och hög trafik har två huvudsakliga faktorer att tänka på med en Personanpassare för skalbarhet och prestanda:

* Hålla låg latens vid rang-API-anrop
* Se till att inlärnings data flödet hålls igång med händelse indata

Anpassning kan returnera en rangordning snabbt, med de flesta samtals varaktigheter som är avsedda för kommunikation via REST API. Azure skalar automatiskt möjligheten att svara på begär Anden snabbt.

##  <a name="low-latency-scenarios"></a>Scenarier med låg latens

Vissa program kräver låg fördröjning när en rang returneras. Låg fördröjning krävs:

* Att hindra användaren från att vänta en märkbart lång tid innan det rankade innehållet visas.
* För att hjälpa en server som har extrem trafik undviker du att binda upp begränsade beräknings tid och nätverks anslutningar.


## <a name="scalability-and-training-throughput"></a>Data flöde för skalbarhet och utbildning

Personanpassaren fungerar genom att uppdatera en modell som omtränas baserat på meddelanden som skickas asynkront av personanpassa efter rang-och belönings-API: er. Dessa meddelanden skickas med en Azure-EventHub för programmet.

 Det är osannolikt att de flesta program kommer att uppnå det högsta antalet anslutningar och sitt inlärnings flöde hos Personanpassaren. Om det här max värdet når det här maximala värdet går det inte att komma åt Event Hub-köer som är ifyllda internt snabbare än de kan rensas.

## <a name="how-to-estimate-your-throughput-requirements"></a>Beräkna dina data flödes krav

* Beräkna det genomsnittliga antalet byte per ranknings händelse som lägger till längderna för kontext-och åtgärds-JSON-dokument.
* Dividera 20 MB/SEK med detta beräknade genomsnittliga byte.

Om din genomsnittliga nytto Last till exempel har 500 funktioner och var och en är en uppskattad 20 tecken, är varje händelse cirka 10 kB. Med dessa uppskattningar, 20 000 000/10 000 = 2 000 händelser/SEK, som är cirka 173 000 000 händelser/dag. 

Om du når dessa gränser kan du kontakta vårt support team för att få arkitektur råd.

## <a name="next-steps"></a>Nästa steg

[Skapa och konfigurera personanpassare](how-to-settings.md).