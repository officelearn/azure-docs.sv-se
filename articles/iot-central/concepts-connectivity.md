---
title: Enhetsanslutning i Azure IoT Central | Microsoft Docs
description: Den här artikeln beskriver viktiga begrepp som rör enhetsanslutning i Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: dc9fe144c2258f33ce59c61ce63c15835cc3fa53
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628342"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Enhetsanslutning i Azure IoT Central

Den här artikeln beskriver viktiga begrepp som rör enhetsanslutning i Microsoft Azure IoT Central.

Varje enhet som ansluter till dina Azure IoT centralt program ansluter till den [slutpunkter](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints) som exponeras av IoT-hubben Azure IoT centrala använder. IoT-hubb kan skalbar, säker och tillförlitliga anslutningar från dina anslutna enheter.

## <a name="sdk-support"></a>Stöd för SDK

Azure SDK för enheten erbjudandet det enklaste sättet du implementera koden på dina enheter som ansluter till dina Azure IoT centralt program. Följande enhet SDK: er är tillgängliga:

- [Azure IoT-SDK för C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT-SDK för Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT-SDK för Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT-SDK för Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT-SDK för .NET](https://github.com/azure/azure-iot-sdk-csharp)

Varje enhet ansluter med en unik anslutningssträng som identifierar enheten. En enhet kan bara ansluta till IoT-hubben där den är registrerad. När du skapar en verklig enhet i Azure IoT centrala programmet skapar en anslutningssträng som du kan använda programmet.

## <a name="sdk-features-and-iot-hub-connectivity"></a>SDK-funktioner och IoT-hubb-anslutningar

Alla enhetskommunikation med IoT-hubb använder följande anslutningsalternativ för IoT-hubb:

- [Meddelanden enhet till moln](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [Enheten twins](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

I följande tabell visas hur Azure IoT centrala enhetsfunktioner mappas till IoT-hubb funktioner:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Mått: telemetri | Meddelanden enhet till moln |
| Enhetsegenskaper | Enheten dubbla rapporterade egenskaper |
| Inställningar | Enheten dubbla önskad och rapporterade egenskaper |

Mer information om hur du använder SDK: er för enheten finns i följande artiklar exempelkod:

- [Ansluta en generisk Node.js-klient till Azure IoT Central-programmet](howto-connect-nodejs.md)
- [Anslut en hallon Pi-enhet till din Azure IoT centralt program](howto-connect-raspberry-pi-python.md)
- [Ansluter en DevDiv kit enhet till din Azure IoT centralt program](howto-connect-devkit.md).

Följande videoklipp visar en översikt över hur du ansluter en verklig enhet till Azure IoT centrala:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Connect-real-devices-to-Microsoft-IoT-Central/Player]

## <a name="protocols"></a>Protokoll

Enheten SDK stöder följande nätverksprotokoll för att ansluta till en IoT-hubb:

- MQTT
- AMQP
- HTTPS

Information om dessa skillnaden protokoll och riktlinjer för att välja ett finns [väljer ett kommunikationsprotokoll](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols).

Om enheten inte kan använda någon av protokoll som stöds, kan du använda Azure IoT kant till protokoll konvertering. IoT-Edge stöder andra intelligence-på-the-kant-scenarier för att avlasta bearbetningen till kanten från programmet Azure IoT Central.

## <a name="security"></a>Säkerhet

Alla data som utbyts mellan enheter och dina Azure IoT centrala är krypterad. IoT-hubb autentiserar varje begäran från en enhet som ansluter till någon enhet riktade IoT-hubb slutpunkter. För att undvika utbyte av autentiseringsuppgifter via kabel kan använder en enhet signerade token för autentisering. Mer information finns i, [styra åtkomsten till IoT-hubb](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security).

> [!NOTE]
> Enheter som ansluter till Azure IoT centrala måste för närvarande använder SAS-token. X.509-certifikat stöds inte för enheter som ansluter till Azure IoT Central.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt enhetsanslutning i Azure IoT Central är här de föreslagna nästa steg:

- [Förbereda och ansluter en DevKit-enhet](howto-connect-devkit.md)
- [Förbereda och ansluta en Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Ansluta en generisk Node.js-klient till Azure IoT Central-programmet](howto-connect-nodejs.md)
