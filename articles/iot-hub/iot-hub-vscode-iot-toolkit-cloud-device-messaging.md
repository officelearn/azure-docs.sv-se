---
title: Använda Azure IoT Tools för VSCode för att belägga IT Hub-meddelanden
description: Lär dig hur du använder Azure IoT Tools for Visual Studio Code för att övervaka enhet till molnmeddelanden och skicka moln till enhetsmeddelanden i Azure IoT Hub.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.openlocfilehash: 31a5d55d1067b9dd946c1667118d0bde5ee3d59e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682501"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Använda Azure IoT Tools for Visual Studio Code för att skicka och ta emot meddelanden mellan enheten och IoT Hub

![Diagram från på sluten tid](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) är ett användbart Visual Studio-kodtillägg som gör IoT Hub-hantering och IoT-programutveckling enklare. Den här artikeln fokuserar på hur du använder Azure IoT Tools for Visual Studio Code för att skicka och ta emot meddelanden mellan enheten och din IoT-hubb.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Vad du kommer att lära dig

Du lär dig hur du använder Azure IoT Tools för Visual Studio-kod för att övervaka meddelanden från enhet till moln och skicka meddelanden från molnet till enheten. Meddelanden från enhet till moln kan vara sensordata som enheten samlar in och sedan skickar till din IoT-hubb. Meddelanden från molnet till enheten kan vara kommandon som din IoT-hubb skickar till enheten för att blinka en lysdiod som är ansluten till enheten.

## <a name="what-you-will-do"></a>Vad du ska göra

* Använd Azure IoT Tools for Visual Studio Code för att övervaka meddelanden från enhet till moln.

* Använd Azure IoT Tools for Visual Studio Code för att skicka meddelanden från molnet till enheten.

## <a name="what-you-need"></a>Vad du behöver

* En aktiv Azure-prenumeration.

* En Azure IoT-hubb under din prenumeration.

* [Visual Studio-koden](https://code.visualstudio.com/)

* [Azure IoT Tools for VS-kod](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) eller kopiera och klistra in den här URL:en i ett webbläsarfönster:`vscode:extension/vsciot-vscode.azure-iot-tools`

## <a name="sign-in-to-access-your-iot-hub"></a>Logga in för att komma åt din IoT-hubb

1. Expandera avsnittet **Azure IoT Hub Devices** i det nedre vänstra hörnet i **Utforskarvyn** för VS-kod.

2. Klicka på **Välj IoT Hub** på snabbmenyn.

3. Ett popup-fönster visas i det nedre högra hörnet så att du kan logga in på Azure för första gången.

4. När du har loggat in visas din Azure-prenumerationslista och väljer sedan Azure-prenumeration och IoT Hub.

5. Enhetslistan visas på fliken **Azure IoT Hub Devices** inom några sekunder.

   > [!Note]
   > Du kan också slutföra konfigurationen genom att välja **Set IoT Hub Connection String** (Ange IoT Hub-anslutningssträng). Ange **iothubowner-principanslutningssträngen** för IoT-hubben som IoT-enheten ansluter till i popup-fönstret.

## <a name="monitor-device-to-cloud-messages"></a>Övervaka meddelanden från enhet till moln

Så här övervakar du meddelanden som skickas från enheten till IoT-hubben:

1. Högerklicka på enheten och välj **Starta övervakning inbyggd händelseslutpunkt**.

2. De övervakade meddelandena visas i **UTDATA** > **Azure IoT** Hub-vyn.

3. Om du vill sluta övervaka högerklickar du på **utdatavyn** och väljer **Sluta övervaka inbyggt händelseslutpunkt**.

## <a name="send-cloud-to-device-messages"></a>Skicka meddelanden från moln till enhet

Så här skickar du ett meddelande från IoT-hubben till enheten:

1. Högerklicka på enheten och välj **Skicka C2D-meddelande till enhet**.

2. Ange meddelandet i inmatningsrutan.

3. Resultaten visas i **OUTPUT** > **Azure IoT** Hub-vyn.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du övervakar meddelanden från enhet till moln och skickar meddelanden från molnet till enheten mellan din IoT-enhet och Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]