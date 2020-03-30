---
title: Azure IoT-enhetshantering w/ Visual Studio Cloud Explorer
description: Använd Cloud Explorer för Visual Studio för Azure IoT Hub-enhetshantering, med direktmetoderna och Twins önskade egenskaperhanteringsalternativ.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 6fe5a45dda6632c56b3c6714827950e25e7d26af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953182"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Använda Cloud Explorer för Visual Studio för Azure IoT Hub-enhetshantering

![Diagram från på sluten tid](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) är ett användbart Visual Studio-tillägg som gör att du kan visa dina Azure-resurser, inspektera deras egenskaper och utföra viktiga utvecklaråtgärder inifrån Visual Studio. Den levereras med hanteringsalternativ som du kan använda för att utföra olika uppgifter.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Förvaltningsalternativ          | Aktivitet                    |
|----------------------------|--------------------------------|
| Direkta metoder             | Få en enhet att fungera som att starta eller sluta skicka meddelanden eller starta om enheten.                                        |
| Läs enhetstvilling           | Hämta det rapporterade tillståndet för en enhet. Enheten rapporterar till exempel att lysdioden blinkar nu.                                    |
| Uppdatera enhetstvilling         | Sätt en enhet i vissa lägen, till exempel ställa in en lysdiod till grönt eller ställa in telemetrissändintervallet till 30 minuter.         |
| Meddelanden från molnet till enheten   | Skicka meddelanden till en enhet. Till exempel, "Det är mycket troligt att regna idag. Glöm inte att ta med ett paraply."              |

Mer detaljerad förklaring om skillnaderna och vägledningen om hur du använder dessa alternativ finns i [kommunikationsvägledning för enhet till moln](iot-hub-devguide-d2c-guidance.md) och [kommunikationvägledning från molnet till enhet](iot-hub-devguide-c2d-guidance.md).

Enhetstvillingar är JSON-dokument som lagrar information om enhetstillstånd, som metadata, konfigurationer och villkor. IoT Hub beständiga en enhetstvilling för varje enhet som ansluter till den. Mer information om enhetstvillingar finns i [Komma igång med enhetstvillingar](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Detta får du får lära dig

I den här artikeln får du lära dig hur du använder Cloud Explorer för Visual Studio med olika hanteringsalternativ på utvecklingsdatorn.

## <a name="what-you-do"></a>Vad du gör

I den här artikeln kör du Cloud Explorer för Visual Studio med olika hanteringsalternativ.

## <a name="what-you-need"></a>Vad du behöver

Du behöver följande förutsättningar:

- En aktiv Azure-prenumeration.

- En Azure IoT-hubb under din prenumeration.

- Microsoft Visual Studio 2017 Uppdatering 9 eller senare. I den här artikeln används [Visual Studio 2017 eller Visual Studio 2019](https://www.visualstudio.com/vs/).

- Cloud Explorer-komponent från Visual Studio Installer, som valts som standard med Azure-arbetsbelastning.

## <a name="update-cloud-explorer-to-latest-version"></a>Uppdatera Cloud Explorer till den senaste versionen

Cloud Explorer-komponenten från Visual Studio Installer för Visual Studio 2017 stöder endast övervakning av meddelanden från enhet till moln och från molnet till enheten. Om du vill använda Visual Studio 2017 laddar du ned och installerar den senaste [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-hub"></a>Logga in för att komma åt ditt nav

1. Öppna Cloud Explorer **i** > **Cloud Explorer** Visual Studio.

1. Välj ikonen Kontohantering för att visa dina prenumerationer.

    ![Ikon för kontohantering](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Om du är inloggad på Azure visas dina konton. Om du vill logga in på Azure för första gången väljer du **Lägg till ett konto**.

1. Välj de Azure-prenumerationer som du vill använda och välj **Använd**.

1. Expandera prenumerationen och expandera sedan **IoT Hubs**.  Under varje hubb kan du se dina enheter för det navet. Högerklicka på en enhet för att komma åt hanteringsalternativen.

    ![Hanteringsalternativ](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>Direkta metoder

Så här använder du direktmetoder:

1. Högerklicka på enheten och välj **Anropa Enhet direktmetod**.

1. Ange metodnamn och nyttolast i **Anropa direktmetod**och välj sedan **OK**.

    Resultaten visas i **Utdata**.

## <a name="update-device-twin"></a>Uppdatera enhetstvilling

Så här redigerar du en enhetstvilling:

1. Högerklicka på enheten och välj **Redigera enhetstvilling**.

   En **azure-iot-device-twin.json-fil** öppnas med innehållet i enhetstvillingen.

1. Gör några ändringar av **taggar** eller **properties.desired-fält** till filen **azure-iot-device-twin.json.**

1. Tryck på **Ctrl+S** för att uppdatera enhetstvillingen.

   Resultaten visas i **Utdata**.

## <a name="send-cloud-to-device-messages"></a>Skicka meddelanden från moln till enhet

Så här skickar du ett meddelande från IoT Hub till enheten:

1. Högerklicka på enheten och välj **Skicka C2D-meddelande**.

1. Ange meddelandet i **Skicka C2D-meddelande** och välj **OK**.

   Resultaten visas i **Utdata**.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du använder Cloud Explorer för Visual Studio med olika hanteringsalternativ.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
