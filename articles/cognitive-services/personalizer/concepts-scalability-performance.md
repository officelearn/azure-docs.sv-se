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
ms.date: 06/07/2019
ms.author: diberry
ms.openlocfilehash: d116f6bd389b1404ea723c965111cd05880e6c30
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662820"
---
# <a name="scalability-and-performance"></a>Skalbarhet och prestanda

Webbplatser och program med höga prestanda och hög trafik har två huvudsakliga faktorer att tänka på med en Personanpassare för skalbarhet och prestanda:

* Hålla låg latens vid rang-API-anrop
* Se till att inlärnings data flödet hålls igång med händelse indata

Anpassningen kan snabbt returnera en rankning, med de flesta samtals varaktigheter som är avsedda för kommunikation via REST API. Azure skalar automatiskt möjligheten att svara på begär Anden snabbt.

##  <a name="low-latency-scenarios"></a>Scenarier med låg latens

Vissa program kräver låg fördröjning när en rang returneras. Detta är nödvändigt:

* Att hindra användaren från att vänta en märkbart lång tid innan det rankade innehållet visas.
* För att hjälpa en server som har extrem trafik undviker du att binda upp begränsade beräknings tid och nätverks anslutningar.

<!--

If your web site is scaled on your infrastructure, you can avoid making HTTP calls by hosting the Personalizer API in your own servers running a Docker container.

This change would be transparent to your application, other than using an endpoint URL referring to the running docker instances as opposed to an online service in the cloud.



### Extreme Low Latency Scenarios

If you require latencies under a millisecond, and have already tested using Personalizer via containers, please contact our support team so we can assess your scenario and provide guidance suited to your needs.

-->

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