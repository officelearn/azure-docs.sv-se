---
title: Azure IoT-hubb moln till enhet alternativ | Microsoft Docs
description: "Utvecklare guide - hjälp när du ska använda direkt metoder, enheten dubbla önskade egenskaper eller moln till enhet meddelanden för moln till enhet kommunikation."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: 1ac90923-1edf-4134-bbd4-77fee9b68d24
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: elioda
ms.openlocfilehash: c0f9d0e13cb159188bdaf2b915c1bf6de73be855
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="cloud-to-device-communications-guidance"></a>Vägledning för kommunikation moln till enhet
IoT-hubb innehåller tre alternativ för enhetsappar exponera funktionen till en backend-app:

* [Dirigera metoder] [ lnk-methods] för kommunikation som kräver omedelbar bekräftelse av resultatet. Direkt-metoder används ofta för interaktiv kontroll av enheter, till exempel aktivera fläktar.
* [Dubbla har önskade egenskaper] [ lnk-twins] för tidskrävande kommandon som är avsedda att placera enheten i en viss önskad tillstånd. Till exempel telemetri skicka intervallet till 30 minuter.
* [Moln till enhet meddelanden] [ lnk-c2d] för envägs-meddelanden till appen med enheten.

Här följer en detaljerad jämförelse av de olika alternativen för kommunikation molnet till enheten.

|  | Direkta metoder | Dubblas egenskaper | Meddelanden moln till enhet |
| ---- | ------- | ---------- | ---- |
| Scenario | Kommandon som kräver omedelbar bekräftelse, till exempel aktivera fläktar. | Långvariga kommandon som är avsedda att placera enheten i ett visst tillstånd. Till exempel telemetri skicka intervallet till 30 minuter. | Enkelriktade meddelanden till appen med enheten. |
| Dataflöde | Dubbelriktad. Enhetsapp svara direkt till metoden. Lösningens serverdel får resultatet sammanhang att begäran. | Enkelriktad. Enheten appen tar emot ett meddelande med egenskapsändringen. | Enkelriktad. Enheten appen tar emot meddelandet
| Hållbarhet | Frånkopplade enheter kontaktas inte. Lösningens serverdel meddelas att enheten inte är ansluten. | Egenskapsvärden bevaras i dubbla för enheten. Enheten läses den vid nästa återanslutning. Egenskapsvärden är strängfält med den [IoT-hubb frågespråket][lnk-query]. | Meddelanden kan behållas av IoT-hubb för upp till 48 timmar. |
| Mål | Enhet med hjälp av **deviceId**, eller flera enheter med hjälp av [jobb][lnk-jobs]. | Enhet med hjälp av **deviceId**, eller flera enheter med hjälp av [jobb][lnk-jobs]. | Enskild enhet av **deviceId**. |
| Storlek | Upp till 8 KB-begäran och svar på 8 KB. | Maximalt önskade egenskaper storleken är 8 KB. | Upp till 64 KB meddelanden. |
| frekvens | Hög. Mer information finns i [IoT-hubb begränsar][lnk-quotas]. | Medel. Mer information finns i [IoT-hubb begränsar][lnk-quotas]. | Låg. Mer information finns i [IoT-hubb begränsar][lnk-quotas]. |
| Protokoll | Tillgängligt med MQTT eller AMQP. | Tillgängligt med MQTT eller AMQP. | Tillgängligt på alla protokoll. Enheten måste avsöka när du använder HTTPS. |

Lär dig hur du använder direkta metoder, egenskaper och moln till enhet meddelanden i följande kurser:

* [Använda direkt metoder][lnk-methods-tutorial], för direkta metoder.
* [Använd önskade egenskaper för att konfigurera enheter][lnk-twin-properties]för enheten dubbla har önskade egenskaper. 
* [Skicka meddelanden moln till enhet][lnk-c2d-tutorial], för moln till enhet meddelanden.

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-tutorial]: iot-hub-node-node-c2d.md
