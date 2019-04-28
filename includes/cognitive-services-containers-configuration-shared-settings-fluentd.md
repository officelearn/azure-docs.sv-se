---
ms.openlocfilehash: ffd17f7a641e1481aa4c88f8b2eb12ec11fa7d8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60599416"
---
Fluentd är en öppen källkod för datainsamling för enhetlig loggning. Den `Fluentd` inställningar hantera behållarens anslutning till en [Fluentd](https://www.fluentd.org) server. Behållaren innehåller en Fluentd loggningsprovider, vilket gör att din behållare att skriva loggar och eventuellt måttdata till en Fluentd-server.

I följande tabell beskrivs de konfigurationsinställningar som stöds den `Fluentd` avsnittet.

| Namn | Datatyp | Beskrivning |
|------|-----------|-------------|
| `Host` | Sträng | IP-adressen eller DNS-värdnamn för Fluentd-servern. |
| `Port` | Integer | Porten som används av Fluentd-server.<br/> Standardvärdet är 24224. |
| `HeartbeatMs` | Integer | Pulsslag intervall i millisekunder. Om ingen händelse trafik har skickats innan det här intervallet upphör att gälla, skickas ett pulsslag till Fluentd-servern. Standardvärdet är 60000 millisekunder (1 minut). |
| `SendBufferSize` | Integer | Buffertutrymme för nätverk i byte som allokerats för skickar åtgärder. Standardvärdet är 32768 byte (32 kB). |
| `TlsConnectionEstablishmentTimeoutMs` | Integer | Tidsgräns i millisekunder för att upprätta en SSL/TLS-anslutning med Fluentd-servern. Standardvärdet är 10 000 millisekunder (10 sekunder).<br/> Om `UseTLS` har angetts till false, ignoreras det här värdet. |
| `UseTLS` | Boolesk | Anger om behållaren ska använda SSL/TLS för att kommunicera med Fluentd-servern. Standardvärdet är FALSKT. |