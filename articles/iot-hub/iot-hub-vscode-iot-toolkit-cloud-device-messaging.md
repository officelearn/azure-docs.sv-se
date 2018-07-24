---
title: Hantera Azure IoT Hub cloud enhet-meddelanden med Azure IoT Toolkit-tillägget för Visual Studio Code | Microsoft Docs
description: Lär dig hur du använder Azure IoT Toolkit-tillägget för Visual Studio Code för att övervaka enhet till moln-meddelanden och skicka cloud enhet-meddelanden i Azure IoT Hub.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 7/20/2018
ms.author: junhan
ms.openlocfilehash: 7bcb6eebdb6ceba6b07aeb19c1a74309fd4227d0
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39206128"
---
# <a name="use-azure-iot-toolkit-extension-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Använd Azure IoT Toolkit-tillägget för Visual Studio Code för att skicka och ta emot meddelanden mellan enheten och IoT Hub

![Slutpunkt till slutpunkt-diagram](media/iot-hub-get-started-e2e-diagram/2.png)

[Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) är ett användbart Visual Studio Code-tillägg som underlättar IoT Hub-hantering. Den här artikeln handlar om hur du använder Azure IoT Toolkit-tillägget för Visual Studio Code för att skicka och ta emot meddelanden mellan enheten och IoT-hubben.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Vad du får lära dig

Du lär dig hur du använder Azure IoT Toolkit-tillägget för Visual Studio Code för att övervaka meddelanden från enheten till molnet och skicka meddelanden från molnet till enheten. Meddelanden från enheten till molnet kan vara sensordata som din enhet samlar in och skickar sedan till din IoT-hubb. Meddelanden från moln till enhet kan vara kommandon som din IoT-hubb som skickas till din enhet blinkar en Indikator som är ansluten till din enhet.

## <a name="what-you-will-do"></a>Vad du ska göra

- Använd Azure IoT Toolkit-tillägget för Visual Studio Code för att övervaka meddelanden från enheten till molnet.
- Använda Azure IoT Toolkit-tillägget för Visual Studio Code för att skicka meddelanden från molnet till enheten.

## <a name="what-you-need"></a>Vad du behöver

- En aktiv Azure-prenumeration.
- Azure IoT hub i din prenumeration.
- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)

## <a name="sign-in-to-access-your-iot-hub"></a>Logga in att få åtkomst till din IoT-hubb

1. I **Explorer** av VS Code, expanderar **Azure IoT Hub-enheter** avsnitt i det nedre vänstra hörnet.
1. Klicka på **Välj IoT Hub** i snabbmenyn.
1. Ett popup-fönster visas i det nedre högra hörnet så att du kan logga in på Azure för första gången.
1. När du har loggat in din Azure-prenumeration lista visas och välj sedan Azure-prenumeration och IoT Hub.
1. Listan över enheter visas i **Azure IoT Hub-enheter** fliken på några sekunder.

   > [!Note]
   > Du kan också komplettera upp genom att välja **ange anslutningssträngen för IoT Hub**. Ange anslutningssträngen för IoT-hubb som din IoT-enhet som ansluter till i popup-fönstret.
   
## <a name="monitor-device-to-cloud-messages"></a>Övervaka meddelanden från enheten till molnet

Följ dessa steg för att övervaka meddelanden som skickas från enheten till IoT hub:

1. Högerklicka på enheten och välj **börja övervaka D2C-meddelande**.
1. Övervakade meddelandena visas i **utdata** > **Azure IoT Toolkit** vy.
1. Stoppa övervakning genom att högerklicka på den **utdata** visa och välja **stoppa övervakning av D2C-meddelande**.

## <a name="send-cloud-to-device-messages"></a>Skicka meddelanden från moln till enhet

Om du vill skicka ett meddelande från IoT hub till enheten, Följ dessa steg:

1. Högerklicka på enheten och välj **C2D skicka meddelande till enhet**. 
1. Skriv meddelandet i textrutan.
1. Resultaten visas i **utdata** > **Azure IoT Toolkit** vy.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig att övervaka meddelanden från enheten till molnet och skicka meddelanden från molnet till enheten mellan dina IoT-enhet och Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
