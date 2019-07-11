---
title: Skalbarhet och prestanda – Personalizer
titleSuffix: Azure Cognitive Services
description: 'Höga prestanda och hög trafik webbplatser och program har två huvudsakliga faktorer att tänka på med Personalizer för skalbarhet och prestanda: svarstider och genomströmning för utbildning.'
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: edjez
ms.openlocfilehash: 06c2e65c723e18acc515dd7effc61aae0564f411
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722416"
---
# <a name="scalability-and-performance"></a>Skalbarhet och prestanda

Höga prestanda och hög trafik webbplatser och program har två huvudsakliga faktorer att tänka på med Personalizer för skalbarhet och prestanda:

* Att hålla låg svarstid när du gör rangordning API-anrop
* Se till utbildning Dataflöde håller takten med händelsen indata

Anpassning kan returnera en rangordning mycket snabbt med de flesta av de varaktighet för kommunikation via REST API. Azure kommer automatisk skalning kan svara på begäranden snabbt.

##  <a name="low-latency-scenarios"></a>Scenarier med låg latens

Vissa program kräver låg fördröjning när returnerar en rangordning. Detta är nödvändigt:

* Rangordnas innehåll för att förhindra användaren från väntar på en märkbar tidsperiod innan den visas.
* För att en server som har drabbats av extrem trafik undvika binda upp begränsade beräkning tid och nätverksanslutningar.

<!--

If your web site is scaled on your infrastructure, you can avoid making HTTP calls by hosting the Personalizer API in your own servers running a Docker container.

This change would be transparent to your application, other than using an endpoint URL referring to the running docker instances as opposed to an online service in the cloud.



### Extreme Low Latency Scenarios

If you require latencies under a millisecond, and have already tested using Personalizer via containers, please contact our support team so we can assess your scenario and provide guidance suited to your needs.

-->

## <a name="scalability-and-training-throughput"></a>Skalbarhet och utbildning Dataflöde

Personalizer fungerar genom att uppdatera en modell som är modellkomponenten baserat på meddelanden som skickats asynkront av Personalizer efter rangordning och API: er för trafik. Dessa meddelanden som skickas med en Azure-EventHub för programmet.

 Det är osannolikt de flesta program når maximal koppla och utbildning genomströmning på Personalizer. När du når den här högsta värdet inte tar lång tid att programmet, skulle det Event Hub köer komma fylls internt snabbare än vad de kan rensas.

## <a name="how-to-estimate-your-throughput-requirements"></a>Så här att uppskatta dina behov för dataflöde

* Beräkna det genomsnittliga antalet byte per rangordning händelse att lägga till längder av JSON-dokument kontext och åtgärd.
* Dela upp 20MB per sekund av den här uppskattade Genomsnittligt antal byte.

Exempel: om din genomsnittliga nyttolast har 500 funktioner och var och en är en beräknad 20 tecken, sedan varje händelse är ungefär 10kb. Med dessa beräkningar, 20 000 000 / 10 000 = 2 000 händelser/sekund, vilket är ungefär 173 miljoner händelser per dag. 

Om du ansluter till dessa gränser, kontakta Vårt supportteam för arkitekturen råd.

## <a name="next-steps"></a>Nästa steg

[Skapa och konfigurera Personalizer](how-to-settings.md).