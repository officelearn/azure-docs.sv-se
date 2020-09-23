---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b08516b35a864eae6d15c4c5c928f0550c64c239
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "67712451"
---
Fluent är en data insamlare med öppen källkod för enhetlig loggning. `Fluentd`Inställningarna hanterar behållarens anslutning till en [Fluent](https://www.fluentd.org) -Server. Behållaren innehåller en upplösnings leverantör som gör att din behållare kan skriva loggar och, om du vill, Metric-data till en ansvars bara server.

I följande tabell beskrivs de konfigurations inställningar som stöds i `Fluentd` avsnittet.

| Name | Datatyp | Beskrivning |
|------|-----------|-------------|
| `Host` | Sträng | IP-adressen eller DNS-värdnamnet för den sammanslagna servern. |
| `Port` | Heltal | Porten för den Fluent-servern.<br/> Standardvärdet är 24224. |
| `HeartbeatMs` | Heltal | Pulsslags intervallet, i millisekunder. Om ingen händelse trafik har skickats innan det här intervallet går ut skickas ett pulsslag till den distribuerade servern. Standardvärdet är 60000 millisekunder (1 minut). |
| `SendBufferSize` | Heltal | Utrymmet för nätverket i byte allokerat för sändnings åtgärder. Standardvärdet är 32768 byte (32 kilobyte). |
| `TlsConnectionEstablishmentTimeoutMs` | Heltal | Timeout, i millisekunder, för att upprätta en SSL/TLS-anslutning med den upprättade servern. Standardvärdet är 10000 millisekunder (10 sekunder).<br/> Om `UseTLS` är inställt på falskt ignoreras värdet. |
| `UseTLS` | Boolesk | Anger om behållaren ska använda SSL/TLS för att kommunicera med den upprättade servern. Standardvärdet är false. |