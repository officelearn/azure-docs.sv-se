---
title: Hantera Azure IoT Hub moln enhet meddelanden med iothub explorer | Microsoft Docs
description: Lär dig hur du använder verktyget iothub explorer CLI för att övervaka enheten till molnet (D2C) meddelanden och skicka moln till enhet (C2D) meddelanden i Azure IoT Hub.
services: iot-hub
documentationcenter: ''
author: shizn
manager: timlt
tags: ''
keywords: iothub explorer moln enhet messaging iot hub moln till enhet, moln till enhet messaging
ms.assetid: 04521658-35d3-4503-ae48-51d6ad3c62cc
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: xshi
ms.openlocfilehash: 7e74c08849ae2f98d3189c0cd3d340874ea085ff
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="use-iothub-explorer-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Använda iothub explorer för att skicka och ta emot meddelanden mellan enheten och IoT-hubb

![Diagram för slutpunkt till slutpunkt](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[iothub explorer](https://github.com/azure/iothub-explorer) har en handfull kommandon som gör det enklare hantering av IoT-hubb. Den här självstudiekursen fokuserar på hur du använder iothub explorer för att skicka och ta emot meddelanden mellan enheten och din IoT-hubb.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Vad får du lära dig

Du lär dig hur du använder iothub explorer att övervaka meddelanden från enhet till moln och skicka meddelanden moln till enhet. Meddelanden från enhet till moln kan vara sensordata som enheten samlar in och skickar sedan till din IoT-hubb. Moln till enhet meddelanden kan vara kommandon som din IoT-hubb som skickas till din enhet blinkar en Indikator som är ansluten till din enhet.

## <a name="what-you-will-do"></a>Vad du ska göra

- Använd iothub explorer för att övervaka meddelanden från enhet till moln.
- Använda iothub explorer för att skicka meddelanden moln till enhet.

## <a name="what-you-need"></a>Vad du behöver

- Kursen [konfigurera enheten](iot-hub-raspberry-pi-kit-node-get-started.md) slutförts som omfattar följande krav:
  - En aktiv Azure-prenumeration.
  - En Azure IoT-hubb i din prenumeration.
  - Ett klientprogram som skickar meddelanden till din Azure IoT-hubb.
- iothub-explorer. ([Installera iothub explorer](https://github.com/azure/iothub-explorer))

## <a name="monitor-device-to-cloud-messages"></a>Övervaka meddelanden från enhet till moln

Följ dessa steg om du vill övervaka meddelanden som skickas från enheten till din IoT-hubb:

1. Öppna ett konsolfönster.
1. Kör följande kommando:

   ```bash
   iothub-explorer monitor-events <device-id> --login "<IoTHubConnectionString>"
   ```

   > [!Note]
   > Hämta `<device-id>` och `<IoTHubConnectionString>` från IoT-hubb. Kontrollera att du är klar med föregående kursen. Eller försök att använda `iothub-explorer monitor-events <device-id> --login "HostName=<my-hub>.azure-devices.net;SharedAccessKeyName=<my-policy>;SharedAccessKey=<my-policy-key>"` om du har `HostName`, `SharedAccessKeyName` och `SharedAccessKey`.

## <a name="send-cloud-to-device-messages"></a>Skicka meddelanden från moln till enhet

Om du vill skicka ett meddelande från din IoT-hubb till enheten, så här:

1. Öppna ett konsolfönster.
1. Starta en session på din IoT-hubb genom att köra följande kommando:

   ```bash
   iothub-explorer login `<IoTHubConnectionString>`
   ```

1. Skicka ett meddelande till din enhet genom att köra följande kommando:

   ```bash
   iothub-explorer send <device-id> <message>
   ```

Kommandot blinkar Indikator som är ansluten till din enhet och skickar meddelandet till enheten.

> [!Note]
> Det finns inget behov av enheten för att skicka ett separat ack-kommando tillbaka till din IoT-hubb vid mottagning av meddelandet.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du övervakar meddelanden från enhet till moln och skicka meddelanden mellan din IoT-enhet och Azure IoT-hubb för moln till enhet.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
