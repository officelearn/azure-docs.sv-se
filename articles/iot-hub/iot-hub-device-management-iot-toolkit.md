---
title: Azure IoT-enhetshantering med Azure IoT-verktyg för Visual Studio Code | Microsoft Docs
description: Använd Azure IoT Tools för Visual Studio Code för Azure IoT Hub-enhetshantering med direkta metoder och hanteringsalternativ för den läsningen önskade egenskaper.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 03df2ceb2df4d857e48f1790703a1d87647e43d0
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445267"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Använd Azure IoT-verktyg för Visual Studio Code för Azure IoT Hub-enhetshantering

![Slutpunkt till slutpunkt-diagram](media/iot-hub-get-started-e2e-diagram/2.png)

[Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) är ett användbart Visual Studio Code-tillägg som gör IoT Hub hantering och utveckling av IoT enklare. Den innehåller alternativ som du kan använda för att utföra olika uppgifter.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Management-alternativ          | Aktivitet                    |
|----------------------------|--------------------------------|
| Direkta metoder             | Se en enhet som fungerar, till exempel starta eller stoppa skicka meddelanden eller att enheten startas om.                                        |
| Läs enhetstvilling           | Hämta det rapporterade tillståndet för en enhet. Enheten rapporterar till exempel LAMPAN blinkar nu.                                    |
| Uppdatera enhetstvillingen         | Placera en enhet i vissa lägen, till exempel ställa in en LED grönt eller 30 minuter att ställa in skicka telemetriintervall.         |
| Meddelanden från moln till enhet   | Skicka meddelanden till en enhet. Exempel: ”det är mycket troligt att regn idag. Glöm inte att ta med en samlingsnamnet ”.              |

Mer detaljerad förklaring om skillnaderna och hjälp med att använda de här alternativen finns i [enhet till molnet kommunikation vägledning](iot-hub-devguide-d2c-guidance.md) och [moln till enhet kommunikation vägledning](iot-hub-devguide-c2d-guidance.md).

Enhetstvillingar är JSON-dokument som lagrar information om enhetstillstånd (metadata, konfigurationer och villkor). IoT Hub lagrar en enhetstvilling för varje enhet som ansluter till den. Läs mer om enhetstvillingar [Kom igång med enhetstvillingar](iot-hub-node-node-twin-getstarted.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-learn"></a>Detta får du får lära dig

Du lär dig använda Azure IoT-verktyg för Visual Studio Code med olika hanteringsalternativ på utvecklingsdatorn.

## <a name="what-you-do"></a>Vad du gör

Kör Azure IoT-verktyg för Visual Studio Code med olika alternativ.

## <a name="what-you-need"></a>Vad du behöver

* En aktiv Azure-prenumeration.
* Azure IoT hub i din prenumeration.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT-verktyg för VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) eller [öppna den här länken i Visual Studio Code](vscode:extension/vsciot-vscode.azure-iot-tools).

## <a name="sign-in-to-access-your-iot-hub"></a>Logga in att få åtkomst till din IoT-hubb

1. I **Explorer** av VS Code, expanderar **Azure IoT Hub-enheter** avsnitt i det nedre vänstra hörnet.

2. Klicka på **Välj IoT Hub** i snabbmenyn.

3. Ett popup-fönster visas i det nedre högra hörnet så att du kan logga in på Azure för första gången.

4. När du har loggat in din Azure-prenumeration lista visas och välj sedan Azure-prenumeration och IoT Hub.

5. Listan över enheter visas i **Azure IoT Hub-enheter** fliken på några sekunder.

   > [!Note]
   > Du kan också slutföra konfigurationen genom att välja **Set IoT Hub Connection String** (Ange IoT Hub-anslutningssträng). Ange anslutningssträngen för IoT-hubb som din IoT-enhet som ansluter till i popup-fönstret.

## <a name="direct-methods"></a>Direkta metoder

1. Högerklicka på enheten och välj **anropa direkt metod**. 

2. Ange metodnamnet och nyttolast i textrutan.

3. Resultaten visas i **utdata** > **Azure IoT Hub Toolkit** vy.

## <a name="read-device-twin"></a>Läs enhetstvilling

1. Högerklicka på enheten och välj **redigera Enhetstvilling**. 

2. En **azure-iot-device-twin.json** filen öppnas med innehållet i enhetstvillingen.

## <a name="update-device-twin"></a>Uppdatera enhetstvillingen

1. Göra vissa ändringar av **taggar** eller **properties.desired** fält.

2. Högerklicka på den **azure-iot-device-twin.json** fil.

3. Välj **uppdatera Enhetstvillingen** att uppdatera enhetstvillingen.

## <a name="send-cloud-to-device-messages"></a>Skicka meddelanden från moln till enhet

Om du vill skicka ett meddelande från IoT hub till enheten, Följ dessa steg:
 
1. Högerklicka på enheten och välj **C2D skicka meddelande till enhet**. 

2. Skriv meddelandet i textrutan.

3. Resultaten visas i **utdata** > **Azure IoT Hub Toolkit** vy.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du använder tillägget Azure IoT Tools för Visual Studio Code med olika alternativ.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
