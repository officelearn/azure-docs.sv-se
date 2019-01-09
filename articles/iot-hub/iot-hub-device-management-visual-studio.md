---
title: Azure IoT-enhetshantering med Cloud Explorer för Visual Studio | Microsoft Docs
description: Använda Cloud Explorer för Visual Studio för Azure IoT Hub-enhetshantering med direkta metoder och hanteringsalternativ för den läsningen önskade egenskaper.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: xshi
ms.openlocfilehash: f0cb740f04970aeed4342511a046862f05aaa736
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54108204"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Använd Cloud Explorer för Visual Studio för Azure IoT Hub-enhetshantering

![Slutpunkt till slutpunkt-diagram](media/iot-hub-get-started-e2e-diagram/2.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) är ett användbart Visual Studio-tillägg som gör att du kan visa dina Azure-resurser, granska deras egenskaper och utföra viktiga developer åtgärder från Visual Studio. Den innehåller alternativ som du kan använda för att utföra olika uppgifter.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Management-alternativ          | Aktivitet                    |
|----------------------------|--------------------------------|
| Direkta metoder             | Se en enhet som fungerar, till exempel starta eller stoppa skicka meddelanden eller att enheten startas om.                                        |
| Läs enhetstvilling           | Hämta det rapporterade tillståndet för en enhet. Enheten rapporterar till exempel LAMPAN blinkar nu.                                    |
| Uppdatera enhetstvillingen         | Placera en enhet i vissa lägen, till exempel ställa in en LED grönt eller 30 minuter att ställa in skicka telemetriintervall.         |
| Meddelanden från moln till enhet   | Skicka meddelanden till en enhet. Exempel: ”det är mycket troligt att regn idag. Glöm inte att ta med en samlingsnamnet ”.              |

Mer detaljerad förklaring om skillnaderna och hjälp med att använda de här alternativen finns i [enhet till molnet kommunikation vägledning](iot-hub-devguide-d2c-guidance.md) och [moln till enhet kommunikation vägledning](iot-hub-devguide-c2d-guidance.md).

Enhetstvillingar är JSON-dokument som lagrar information om enhetstillstånd (metadata, konfigurationer och villkor). IoT Hub lagrar en enhetstvilling för varje enhet som ansluter till den. Läs mer om enhetstvillingar [Kom igång med enhetstvillingar](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Detta får du får lära dig

Du lär dig använda Cloud Explorer för Visual Studio med olika hanteringsalternativ på utvecklingsdatorn.

## <a name="what-you-do"></a>Vad du gör

Kör Cloud Explorer för Visual Studio med olika alternativ.

## <a name="what-you-need"></a>Vad du behöver

- En aktiv Azure-prenumeration.
- En Azure IoT-hubb i din prenumeration.
- Microsoft Visual Studio 2017 Update 8 eller senare
- Cloud Explorer-komponenten från installationsprogrammet för Visual Studio (valt som standard med Azure-arbetsbelastning)

## <a name="update-cloud-explorer-to-latest-version"></a>Uppdatera Cloud Explorer till senaste versionen

Cloud Explorer-komponenten från installationsprogrammet för Visual Studio stöder endast övervakning enhet till moln och från moln till enhet-meddelanden. Du måste hämta och installera senast [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) att komma åt hanteringsalternativ.

## <a name="sign-in-to-access-your-iot-hub"></a>Logga in att få åtkomst till din IoT-hubb

1. I Visual Studio **Cloud Explorer** fönstret klickar du på ikonen kontohantering. Du kan öppna fönstret Cloud Explorer från **visa** > **Cloud Explorer** menyn.

    ![Klicka på hantering](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)

1. Klicka på **hantera konton** i Cloud Explorer.
1. Klicka på **Lägg till ett konto...**  i nytt fönster för att logga in på Azure för första gången.
1. När du har loggat in visas listan Azure-prenumeration. Välj de Azure-prenumerationer du vill visa och klicka på **tillämpa**.
1. Expandera **prenumerationen** > **IoT-hubbar** > **din IoT Hub**, enhetslistan visas under din IoT Hub-nod. Högerklicka på en enhet för att komma åt hanteringsalternativ.

    ![Alternativ för hantering](media/iot-hub-device-management-visual-studio/management-options.png)

## <a name="direct-methods"></a>Direkta metoder

1. Högerklicka på enheten och välj **anropa enheten direkt metod**.
1. Ange metodnamnet och nyttolast i textrutan.
1. Resultaten visas i den **IoT Hub** utdatarutan.

## <a name="read-device-twin"></a>Läs enhetstvilling

1. Högerklicka på enheten och välj **redigera Enhetstvilling**.
1. En **azure-iot-device-twin.json** filen öppnas med innehållet i enhetstvillingen.

## <a name="update-device-twin"></a>Uppdatera enhetstvillingen

1. Göra vissa ändringar av **taggar** eller **properties.desired** automatiskt till den **azure-iot-device-twin.json** fil.
1. Tryck på **Ctrl + S** att uppdatera enhetstvillingen.
1. Resultaten visas i den **IoT Hub** utdatarutan.

## <a name="send-cloud-to-device-messages"></a>Skicka meddelanden från moln till enhet

Om du vill skicka ett meddelande från IoT Hub till enheten, Följ dessa steg:

1. Högerklicka på enheten och välj **skicka C2D meddelande**.
1. Skriv meddelandet i textrutan.
1. Resultaten visas i den **IoT Hub** utdatarutan.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du använder Cloud Explorer för Visual Studio med olika alternativ.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]