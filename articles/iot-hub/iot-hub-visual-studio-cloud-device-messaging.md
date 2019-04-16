---
title: Hantera Azure IoT Hub cloud enhet-meddelanden med Cloud Explorer för Visual Studio | Microsoft Docs
description: Lär dig hur du använder Cloud Explorer för Visual Studio för att övervaka enhet till moln-meddelanden och skicka cloud enhet-meddelanden i Azure IoT Hub.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2018
ms.author: xshi
ms.openlocfilehash: ab3c02d7207bca70a90df8aa08c73c1484cd635d
ms.sourcegitcommit: e89b9a75e3710559a9d2c705801c306c4e3de16c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59571319"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Använda Cloud Explorer för Visual Studio för att skicka och ta emot meddelanden mellan enheten och IoT Hub

![Slutpunkt till slutpunkt-diagram](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) är ett användbart Visual Studio-tillägg som gör att du kan visa dina Azure-resurser, granska deras egenskaper och utföra viktiga developer åtgärder från Visual Studio. Den här artikeln handlar om hur du använder Cloud Explorer för att skicka och ta emot meddelanden mellan enheten och IoT-hubben.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Vad du får lära dig

Du kommer lära dig hur du använder Cloud Explorer för Visual Studio att övervaka meddelanden från enheten till molnet och skicka meddelanden från molnet till enheten. Meddelanden från enheten till molnet kan vara sensordata som din enhet samlar in och skickar sedan till din IoT-hubb. Meddelanden från moln till enhet kan vara kommandon som din IoT-hubb som skickas till din enhet. Till exempel Blinka en Indikator som är ansluten till din enhet.

## <a name="what-you-will-do"></a>Vad du ska göra

- Använda Cloud Explorer för Visual Studio för att övervaka meddelanden från enheten till molnet.
- Använda Cloud Explorer för Visual Studio för att skicka meddelanden från molnet till enheten.

## <a name="what-you-need"></a>Vad du behöver

- En aktiv Azure-prenumeration.
- En Azure IoT-hubb i din prenumeration.
- Microsoft Visual Studio 2017 Update 8 eller senare
- Cloud Explorer-komponenten från installationsprogrammet för Visual Studio (valt som standard med Azure-arbetsbelastning)

## <a name="update-cloud-explorer-to-latest-version"></a>Uppdatera Cloud Explorer till senaste versionen

Cloud Explorer-komponenten från installationsprogrammet för Visual Studio stöder endast övervakning enhet till moln och från moln till enhet-meddelanden. För att kunna skicka meddelanden till enheten eller i molnet, ladda ned och installera senast [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-iot-hub"></a>Logga in att få åtkomst till din IoT-hubb

1. I Visual Studio **Cloud Explorer** fönstret klickar du på ikonen kontohantering. Du kan öppna fönstret Cloud Explorer från **visa** > **Cloud Explorer** menyn.

    ![Klicka på hantering](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)


2. Klicka på **hantera konton** i Cloud Explorer.

3. Klicka på **Lägg till ett konto...**  i nytt fönster för att logga in på Azure för första gången.

4. När du har loggat in visas listan Azure-prenumeration. Välj de Azure-prenumerationer du vill visa och klicka på **tillämpa**.

5. Expandera **prenumerationen** > **IoT-hubbar** > **din IoT Hub**, enhetslistan visas under din IoT Hub-nod.

    ![Enhetslista](media/iot-hub-visual-studio-cloud-device-messaging/device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Övervaka meddelanden från enheten till molnet

Följ dessa steg för att övervaka meddelanden som skickas från enheten till IoT Hub:

1. Högerklicka på din IoT-hubb eller en enhet och välj **börja övervaka D2C-meddelande**.

    ![Börja övervaka D2C-meddelande](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message.png)

2. Övervakade meddelandena visas i den **IoT Hub** utdatarutan.

    ![Övervakning av D2C Meddelanderesultatet](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result.png)

3. Att stoppa övervakning, högerklicka på valfri IoT-hubb eller en enhet och välj **stoppa övervakning av D2C-meddelande**.

## <a name="send-cloud-to-device-messages"></a>Skicka meddelanden från moln till enhet

Om du vill skicka ett meddelande från IoT Hub till enheten, Följ dessa steg:

1. Högerklicka på enheten och välj **skicka C2D meddelande**.

    ![Skicka C2D-meddelande](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message.png)

2. Skriv meddelandet i textrutan.

3. Resultaten visas i den **IoT Hub** utdatarutan.

    ![Skicka C2D Meddelanderesultatet](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result.png)

## <a name="next-steps"></a>Nästa steg

Du har lärt dig att övervaka meddelanden från enheten till molnet och skicka meddelanden från molnet till enheten mellan dina IoT-enhet och Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]