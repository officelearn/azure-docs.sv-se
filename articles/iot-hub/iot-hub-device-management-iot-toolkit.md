---
title: Hantering av Azure IoT-enheter med Azure IoT Tools för VSCode
description: Använd Azure IoT Tools for Visual Studio Code for Azure IoT Hub-enhetshantering, med direct-metoderna och Twins önskade egenskaperhanteringsalternativ.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 9d4d82472664900c96b77b31740573d0463465b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75911918"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Använda Azure IoT Tools för Visual Studio-kod för Azure IoT Hub-enhetshantering

![Diagram från på sluten tid](media/iot-hub-get-started-e2e-diagram/2.png)

[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) är ett användbart Visual Studio-kodtillägg som gör IoT Hub-hantering och IoT-programutveckling enklare. Den levereras med hanteringsalternativ som du kan använda för att utföra olika uppgifter.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Förvaltningsalternativ          | Aktivitet                    |
|----------------------------|--------------------------------|
| Direkta metoder             | Få en enhet att fungera som att starta eller sluta skicka meddelanden eller starta om enheten.                                        |
| Läs enhetstvilling           | Hämta det rapporterade tillståndet för en enhet. Enheten rapporterar till exempel att lysdioden blinkar nu.                                    |
| Uppdatera enhetstvilling         | Sätt en enhet i vissa lägen, till exempel ställa in en lysdiod till grönt eller ställa in telemetrissändintervallet till 30 minuter.         |
| Meddelanden från molnet till enheten   | Skicka meddelanden till en enhet. Till exempel, "Det är mycket troligt att regna idag. Glöm inte att ta med ett paraply."              |

Mer detaljerad förklaring om skillnaderna och vägledningen om hur du använder dessa alternativ finns i [kommunikationsvägledning för enhet till moln](iot-hub-devguide-d2c-guidance.md) och [kommunikationvägledning från molnet till enhet](iot-hub-devguide-c2d-guidance.md).

Enhetstvillingar är JSON-dokument som lagrar information om enhetstillstånd (metadata, konfigurationer och villkor). IoT Hub beständiga en enhetstvilling för varje enhet som ansluter till den. Mer information om enhetstvillingar finns i [Komma igång med enhetstvillingar](iot-hub-node-node-twin-getstarted.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-learn"></a>Detta får du får lära dig

Du lär dig att använda Azure IoT Tools för Visual Studio-kod med olika hanteringsalternativ på din utvecklingsdator.

## <a name="what-you-do"></a>Vad du gör

Kör Azure IoT Tools för Visual Studio-kod med olika hanteringsalternativ.

## <a name="what-you-need"></a>Vad du behöver

* En aktiv Azure-prenumeration.
* En Azure IoT-hubb under din prenumeration.
* [Visual Studio-kod](https://code.visualstudio.com/)
* [Azure IoT Tools for VS-kod](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) eller [öppna den här länken i Visual Studio Code](vscode:extension/vsciot-vscode.azure-iot-tools).

## <a name="sign-in-to-access-your-iot-hub"></a>Logga in för att komma åt din IoT-hubb

1. Expandera avsnittet **Azure IoT Hub Devices** i det nedre vänstra hörnet i **Utforskarvyn** för VS-kod.

2. Klicka på **Välj IoT Hub** på snabbmenyn.

3. Ett popup-fönster visas i det nedre högra hörnet så att du kan logga in på Azure för första gången.

4. När du har loggat in visas din Azure-prenumerationslista och väljer sedan Azure-prenumeration och IoT Hub.

5. Enhetslistan visas på fliken **Azure IoT Hub Devices** inom några sekunder.

   > [!Note]
   > Du kan också slutföra konfigurationen genom att välja **Set IoT Hub Connection String** (Ange IoT Hub-anslutningssträng). Ange **iothubowner-principanslutningssträngen** för IoT-hubben som IoT-enheten ansluter till i popup-fönstret.

## <a name="direct-methods"></a>Direkta metoder

1. Högerklicka på enheten och välj **Anropa direktmetod**. 

2. Ange metodnamn och nyttolast i indatarutan.

3. Resultaten visas i **OUTPUT** > **Azure IoT** Hub-vyn.

## <a name="read-device-twin"></a>Läs enhetstvilling

1. Högerklicka på enheten och välj **Redigera enhetstvilling**. 

2. En **azure-iot-device-twin.json-fil** öppnas med innehållet i enhetstvillingen.

## <a name="update-device-twin"></a>Uppdatera enhetstvilling

1. Gör några ändringar av **taggar** eller **egenskaper.önskat** fält.

2. Högerklicka på **filen Azure-iot-device-twin.json.**

3. Välj **Uppdatera Enhetstvilling** om du vill uppdatera enhetstvillingen.

## <a name="send-cloud-to-device-messages"></a>Skicka meddelanden från moln till enhet

Så här skickar du ett meddelande från IoT-hubben till enheten:
 
1. Högerklicka på enheten och välj **Skicka C2D-meddelande till enhet**. 

2. Ange meddelandet i inmatningsrutan.

3. Resultaten visas i **OUTPUT** > **Azure IoT** Hub-vyn.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du använder Azure IoT Tools-tillägget för Visual Studio-kod med olika hanteringsalternativ.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
