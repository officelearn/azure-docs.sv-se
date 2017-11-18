---
title: "Förstå Azure IoT Hub-meddelanden | Microsoft Docs"
description: "Utvecklarhandbok - enhet till moln och moln till enhet meddelanden med IoT-hubben. Innehåller information om meddelandeformat och stöds kommunikationsprotokoll."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: dobett
ms.openlocfilehash: 4a7f545f1af2904f47808fe8b1d7d3aab3afff84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="device-to-cloud-and-cloud-to-device-messaging-with-iot-hub"></a>Enhet till moln och moln till enhet meddelanden med IoT-hubb

Använd IoT-hubb-meddelanden för att kommunicera med dina enheter genom att:

* Skicka [enhet till moln] [ lnk-d2c] meddelanden från dina enheter till din lösning serverdel.
* Skicka [moln till enhet] [ lnk-c2d] meddelanden från lösningen serverdel till dina enheter.

Grundläggande egenskaper för IoT-hubb meddelandefunktioner är tillförlitlighet och hållbarhet av meddelanden. De här egenskaperna aktiverar återhämtning för återkommande anslutning på enhetens sida och att läsa in toppar i händelsebearbetning på sidan moln. IoT-hubb implementerar *minst en gång* leverans garanterar för både enhet till moln och moln till enhet meddelanden.

En introduktion till funktionerna för IoT-hubb finns i artiklar [Azure och Sakernas Internet] [ lnk-azure-iot] och [översikt över tjänsten Azure IoT Hub] [ lnk-iot-hub-overview].

## <a name="when-to-use-iot-hub-messaging"></a>När du ska använda IoT-hubb-meddelanden

Använda meddelanden från enhet till moln för att skicka tid serien telemetri och aviseringar från din enhet och moln till enhet meddelanden för envägs-meddelanden till din enhetsapp.

* Referera till [enhet till moln kommunikation vägledning] [ lnk-d2c-guidance] om osäkra mellan med hjälp av meddelanden från enhet till moln, rapporterade egenskaper eller ladda upp filen.
* Referera till [moln till enhet kommunikation vägledning] [ lnk-c2d-guidance] om osäkra mellan att använda moln till enhet meddelanden, önskade egenskaper eller direkt metoder.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om IoT-hubb [enhet till moln messaging][lnk-d2c].
* Lär dig mer om IoT-hubb [moln till enhet messaging][lnk-c2d].

[lnk-azure-iot]: iot-hub-what-is-azure-iot.md
[lnk-iot-hub-overview]: iot-hub-what-is-iot-hub.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md