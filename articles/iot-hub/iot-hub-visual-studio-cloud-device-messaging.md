---
title: Använda VS Cloud Explorer för att hantera Azure IoT Hub-enhetsmeddelanden
description: Lär dig hur du använder Cloud Explorer för Visual Studio för att övervaka enhet till molnmeddelanden och skicka moln till enhetsmeddelanden i Azure IoT Hub.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: c56bb7030b2ebc12e3afc24e2d8cb29ce2dda0bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74079482"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Använda Cloud Explorer för Visual Studio för att skicka och ta emot meddelanden mellan enheten och IoT Hub

![Diagram från på sluten tid](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) är ett användbart Visual Studio-tillägg som gör att du kan visa dina Azure-resurser, inspektera deras egenskaper och utföra viktiga utvecklaråtgärder inifrån Visual Studio. Den här artikeln fokuserar på hur du använder Cloud Explorer för att skicka och ta emot meddelanden mellan enheten och navet.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-learn"></a>Detta får du får lära dig

I den här artikeln får du lära dig hur du använder Cloud Explorer för Visual Studio för att övervaka meddelanden från enhet till moln och skicka meddelanden från molnet till enheten. Meddelanden från enhet till moln kan vara sensordata som enheten samlar in och sedan skickar till din IoT Hub. Meddelanden från molnet till enheten kan vara kommandon som din IoT-hubb skickar till din enhet. Blinka till exempel en lysdiod som är ansluten till enheten.

## <a name="what-you-do"></a>Vad du gör

I den här artikeln gör du följande:

- Använd Cloud Explorer för Visual Studio för att övervaka meddelanden från enhet till moln.

- Använd Cloud Explorer för Visual Studio för att skicka meddelanden från molnet till enheten.

## <a name="what-you-need"></a>Vad du behöver

Du behöver följande förutsättningar:

- En aktiv Azure-prenumeration.

- En Azure IoT-hubb under din prenumeration.

- Microsoft Visual Studio 2017 Uppdatering 9 eller senare. I den här artikeln används [Visual Studio 2019](https://www.visualstudio.com/vs/).

- Cloud Explorer-komponenten från Visual Studio Installer, som väljs som standard med Azure-arbetsbelastning.

## <a name="update-cloud-explorer-to-latest-version"></a>Uppdatera Cloud Explorer till den senaste versionen

Cloud Explorer-komponenten från Visual Studio Installer för Visual Studio 2017 stöder endast övervakning av meddelanden från enhet till moln och från molnet till enheten. Om du vill använda Visual Studio 2017 laddar du ned och installerar den senaste [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-hub"></a>Logga in för att komma åt ditt nav

Så här öppnar du hubben:

1. Öppna Cloud Explorer **i** > **Cloud Explorer** Visual Studio.

1. Välj ikonen Kontohantering för att visa dina prenumerationer.

    ![Ikon för kontohantering](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Om du är inloggad på Azure visas dina konton. Om du vill logga in på Azure för första gången väljer du **Lägg till ett konto**.

1. Välj de Azure-prenumerationer som du vill använda och välj **Använd**.

1. Expandera prenumerationen och expandera sedan **IoT Hubs**.  Under varje hubb kan du se dina enheter för det navet.

    ![Enhetslista](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Övervaka meddelanden från enhet till moln

Så här övervakar du meddelanden som skickas från enheten till IoT Hub:

1. Högerklicka på IoT-hubben eller enheten och välj **Starta övervakning D2C-meddelande**.

    ![Starta övervakning d2c-meddelande](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. De övervakade meddelandena visas under **Utdata**.

    ![Övervakning av D2C-meddelanderesultat](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. Om du vill sluta övervaka högerklickar du på en IoT-hubb eller enhet och väljer **Sluta övervaka D2C Message**.

## <a name="send-cloud-to-device-messages"></a>Skicka meddelanden från moln till enhet

Så här skickar du ett meddelande från IoT Hub till enheten:

1. Högerklicka på enheten och välj **Skicka C2D-meddelande**.

1. Ange meddelandet i inmatningsrutan.

    ![Skicka C2D-meddelande](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    Resultaten visas under **Utdata**.

    ![Skicka C2D-meddelanderesultat](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du övervakar meddelanden från enhet till moln och skickar meddelanden från molnet till enheten mellan din IoT-enhet och Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]