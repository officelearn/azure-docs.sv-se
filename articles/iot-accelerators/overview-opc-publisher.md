---
title: Vad är OPC Publisher - Azure | Microsoft Docs
description: Översikt över OPC Publisher
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: c738e927a352373d7f5a4aeb5697e07134a98cba
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603669"
---
# <a name="what-is-opc-publisher"></a>Vad är OPC Publisher?

OPC Publisher är en referensimplementering som visar hur du:

- Anslut till befintliga OPC UA-servrar.
- Publicera JSON-kodad telemetridata från OPC UA-servrar i OPC UA Pub/Sub-format, med hjälp av en JSON-nyttolast till Azure IoT Hub.

Du kan använda någon av de transportprotokoll som stöds av Azure IoT Hub-klient-SDK: HTTPS, AMQP och MQTT.

Referensimplementeringen innehåller:

- En OPC UA *klienten* för att ansluta till befintliga OPC UA-servrar som du har i nätverket.
- En OPC UA *server* på port 62222 som du kan använda för att hantera vad publiceras och erbjuder IoT Hub direkta metoder för att göra detsamma.

Du kan ladda ned den [referensimplementering för OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) från GitHub.

Programmet har implementerats med hjälp av .NET Core-teknik och kan köras på alla plattformar som stöds av .NET Core.

OPC Publisher implementerar logik för omprövning och kan upprätta anslutningar till slutpunkter som inte svarar på ett visst antal keep alive-begäranden. Om exempelvis en OPC UA-servern inte svarar på grund av ett strömavbrott.

Programmet skapar en separat prenumeration som alla noder med den här Publiceringsintervall uppdateras för varje distinkt Publiceringsintervall till en OPC UA-server.

OPC Publisher har stöd för batchbearbetning av data som skickas till IoT Hub till att minska nätverksbelastningen. Den här batchbearbetning skickar ett paket till IoT Hub endast om den konfigurera paketstorleken har nåtts.

Det här programmet använder referens OPC Foundation OPC UA-stacken som NuGet-paket. Se [ https://opcfoundation.org/license/redistributables/1.3/ ](https://opcfoundation.org/license/redistributables/1.3/) för licensvillkoren.

### <a name="next-steps"></a>Nästa steg

Nu har du lärt dig OPC Publisher nyheter, föreslagna nästa steg är att lära dig hur du [konfigurera OPC Publisher](howto-opc-publisher-configure.md).
