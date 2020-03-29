---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b08516b35a864eae6d15c4c5c928f0550c64c239
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67712451"
---
Fluentd är en datainsamlare med öppen källkod för enhetlig loggning. Inställningarna `Fluentd` hanterar behållarens anslutning till en [Fluentd-server.](https://www.fluentd.org) Behållaren innehåller en Fluentd loggningsleverantör, som gör att din behållare att skriva loggar och, eventuellt, metriska data till en Fluentd-server.

I följande tabell beskrivs de `Fluentd` konfigurationsinställningar som stöds under avsnittet.

| Namn | Datatyp | Beskrivning |
|------|-----------|-------------|
| `Host` | String | IP-adressen eller DNS-värdnamnet för Fluentd-servern. |
| `Port` | Integer | Porten på Fluentd-servern.<br/> Standardvärdet är 24224. |
| `HeartbeatMs` | Integer | Hjärtslagsintervallet, i millisekunder. Om ingen händelsetrafik har skickats innan det här intervallet går ut skickas ett pulsslag till Flämtningsservern. Standardvärdet är 60000 millisekunder (1 minut). |
| `SendBufferSize` | Integer | Nätverksbuffertutrymmet, i byte, allokerat för sändningsåtgärder. Standardvärdet är 32768 byte (32 kilobyte). |
| `TlsConnectionEstablishmentTimeoutMs` | Integer | Tidsgränsen i millisekunder för att upprätta en SSL/TLS-anslutning med Fluentd-servern. Standardvärdet är 10000 millisekunder (10 sekunder).<br/> Om `UseTLS` är inställt på false ignoreras det här värdet. |
| `UseTLS` | Boolean | Anger om behållaren ska använda SSL/TLS för att kommunicera med Flentd-servern. Standardvärdet är false. |