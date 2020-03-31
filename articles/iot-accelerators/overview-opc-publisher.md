---
title: Vad är OPC Publisher - Azure | Microsoft-dokument
description: Den här artikeln innehåller en översikt över funktionerna i OPC Publisher. Det låter dig publicera kodade JSON telemetridata med hjälp av en JSON-nyttolast, till Azure IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 2310a81d3cfaeff203134af9968bc2d5caea3e9c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "73824784"
---
# <a name="what-is-opc-publisher"></a>Vad är OPC Publisher?

OPC Publisher är en referensimplementering som visar hur du:

- Anslut till befintliga OPC UA-servrar.
- Publicera JSON-kodade telemetridata från OPC UA-servrar i OPC UA Pub/Sub-format, med hjälp av en JSON-nyttolast, till Azure IoT Hub.

Du kan använda något av transportprotokollen som Azure IoT Hub-klienten SDK stöder: HTTPS, AMQP och MQTT.

Referensgenomförandet omfattar följande:

- En OPC *UA-klient* för anslutning till befintliga OPC UA-servrar som du har i nätverket.
- En OPC *UA-server* på port 62222 som du kan använda för att hantera vad som publiceras och erbjuder IoT Hub direkta metoder för att göra detsamma.

Du kan hämta [OPC Publisher-referensimplementeringen](https://github.com/Azure/iot-edge-opc-publisher) från GitHub.

Programmet implementeras med .NET Core-teknik och kan köras på alla plattformar som stöds av .NET Core.

OPC Publisher implementerar återförsökslogik för att upprätta anslutningar till slutpunkter som inte svarar på ett visst antal begäranden vid liv. Om till exempel en UA-server för OPC slutar svara på grund av ett strömavbrott.

För varje distinkt publiceringsintervall till en OPC UA-server skapar programmet en separat prenumeration över vilken alla noder med det här publiceringsintervallet uppdateras.

OPC Publisher stöder batchbearbetning av data som skickas till IoT Hub för att minska nätverksbelastningen. Den här batchningen skickar ett paket till IoT Hub endast om den konfigurerade paketstorleken har uppnåtts.

Detta program använder OPC Foundation OPC UA referens stack som NuGet paket. Se [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) för licensvillkoren.

### <a name="next-steps"></a>Nästa steg

Nu har du lärt dig vad OPC Publisher är, det föreslagna nästa steget är att lära sig hur [du konfigurerar OPC Publisher](howto-opc-publisher-configure.md).
