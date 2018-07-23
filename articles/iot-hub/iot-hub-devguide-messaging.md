---
title: Förstå Azure IoT Hub-meddelanden | Microsoft Docs
description: Utvecklarguide – enhet till molnet och molnet till enheten med IoT Hub-meddelanden. Innehåller information om meddelandeformat och stöds kommunikationsprotokoll.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: b0667f820145f16c75a07ebe1849e20d2de36cc7
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185517"
---
# <a name="device-to-cloud-and-cloud-to-device-messaging-with-iot-hub"></a>Enhet-till-moln och meddelanden med IoT Hub moln-till-enhet

Använd IoT Hub-meddelanden för att kommunicera med dina enheter genom att:

* Skicka [enhet till moln] [ lnk-d2c] serverdel för meddelanden från dina enheter till din lösning.
* Skicka [moln till enhet] [ lnk-c2d] meddelanden från lösningen serverdel till dina enheter.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Core-egenskaperna för IoT Hub meddelandefunktioner är tillförlitlighet och hållbarhet av meddelanden. De här egenskaperna aktivera återhämtning till tillfällig anslutning på enheten och att läsa in toppar i händelsebearbetning på molnet sida. IoT Hub implementerar *minst en gång* leverans garanterar för både enheten till molnet och moln-till-enhet-meddelanden.

En introduktion till funktionerna för IoT Hub finns i den [översikt över tjänsten Azure IoT Hub][lnk-iot-hub-overview].

## <a name="when-to-use-iot-hub-messaging"></a>När du ska använda IoT Hub-meddelanden

Använda meddelanden från enheten till molnet för att skicka time series telemetri och aviseringar från appen för enheter och meddelanden från moln till enhet för enkelriktade meddelanden till din enhetsapp.

* Referera till [enhet till molnet kommunikation vägledning] [ lnk-d2c-guidance] om tveksam mellan att använda meddelanden från enheten till molnet, rapporterade egenskaper eller ladda upp filen.
* Referera till [moln till enhet kommunikation vägledning] [ lnk-c2d-guidance] om du tvekar mellan att använda meddelanden från moln till enhet, önskade egenskaper eller direkta metoder.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om IoT Hub [enhet-till-moln-meddelanden][lnk-d2c].
* Lär dig mer om IoT Hub [moln till enhet messaging][lnk-c2d].

[lnk-azure-iot]: ../iot-fundamentals/index.yml
[lnk-iot-hub-overview]: about-iot-hub.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md