---
title: Bandbredds rekommendationer för fjärrsessioner – Azure
description: Tillgängliga bandbredds rekommendationer för fjärrsessioner.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: helohr
ms.openlocfilehash: 1a88cfb2fcd5f0b607e7a2b43ae833789a584e34
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695307"
---
# <a name="bandwidth-recommendations-for-remote-sessions"></a>Bandbredds rekommendationer för fjärrsessioner

När du använder en fjärran sluten Windows-session påverkar nätverkets tillgängliga bandbredd avsevärt kvaliteten på din upplevelse. Olika program och visnings lösningar kräver olika nätverkskonfigurationer, så det är viktigt att se till att nätverket är konfigurerat för att uppfylla dina behov. I den här artikeln beskrivs rekommenderade bandbredder för varje arbets belastning.

>[!NOTE]
>Följande rekommendationer gäller nätverk med mindre än 0,1% förlust.

## <a name="applications"></a>Program

I följande tabell visas minimi kraven för en smidig användar upplevelse. 

|Arbetsbelastning        |Exempelprogram                                                                                           |Rekommenderad bandbredd|
|----------------|--------------------------------------------------------------------------------------------------------------|---------------------|
|Uppgiftsarbetare     |Microsoft Word, Outlook, Excel, Adobe Reader                                                                  |1,5 @ no__t – 0Mbps             |
|Kontors arbetare   |Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, Photo Viewer                                        |3 @ no__t-0Mbps               |
|Kunskaps arbetare|Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, Foto visare, Java                                  |5 @ no__t-0Mbps               |
|Energi arbetare    |Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, Foto visare, Java, CAD/CAM, illustration/publicering|15 @ no__t – 0Mbps              |

Tänk på att belastningen på ditt nätverk är beroende av både bild frekvensen för din app-arbetsbelastning och din skärmupplösning. Om bild frekvensen eller skärmupplösningen ökar, kommer bandbredds kravet också att öka. En ljus arbets belastning med hög upplöst visning kräver till exempel mer tillgänglig bandbredd än en lätt arbets belastning med regelbunden eller låg upplösning.

I andra scenarier kan bandbredds kraven ändras beroende på hur du använder dem, till exempel:

- Röst-eller video konferens
- Kommunikation i real tid
- Strömmande 4K-video

Se till att läsa in test scenarier i distributionen med hjälp av simulerings verktyg som inloggning VSI. Variera inläsnings storlek, kör stress test och testa vanliga användar scenarier i fjärrsessioner för att bättre förstå ditt nätverks krav. 

## <a name="display-resolutions"></a>Skärmupplösningar

Olika visnings lösningar kräver olika tillgängliga bandbredder. I följande tabell visas de bandbredder vi rekommenderar för en smidig användar upplevelse vid vanliga skärmupplösningar med en bild Rute hastighet på 30 bild rutor per sekund (FPS). Tänk på att scenarier som involverar en bild Rute frekvens under 30 fps, till exempel att läsa statisk text, kräver mindre tillgänglig bandbredd. 

|Vanliga skärmupplösningar vid 30 fps    |Rekommenderad bandbredd|
|-----------------------------------------|---------------------|
|Cirka 1024 × 768 BPT                      |1,5 Mbit/s             |
|Cirka 1280 × 720 BPT                      |3 Mbit/s               |
|Cirka 1920 × 1080 BPT                     |5 Mbit/s               |
|Cirka 3840 × 2160 BPT (4K)                |15 Mbit/s              |
