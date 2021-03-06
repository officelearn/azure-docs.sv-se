---
title: Fel sökning av Azure IoT Hub-fel 412002 DeviceMessageLockLost
description: Förstå hur du åtgärdar fel 412002 DeviceMessageLockLost
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 7d48474d88a60c73f6094d3b9e65017c23404d8a
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2020
ms.locfileid: "92144264"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

I den här artikeln beskrivs orsaker och lösningar för **412002 DeviceMessageLockLost** -fel.

## <a name="symptoms"></a>Symtom

När du försöker skicka ett meddelande från molnet till enheten Miss lyckas begäran med felet **412002 DeviceMessageLockLost**.

## <a name="cause"></a>Orsak

När en enhet tar emot ett meddelande från molnet till enheten från kön (t. ex. med [`ReceiveAsync()`](/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet) ) låses meddelandet av IoT Hub för en tids gräns för låsning av en minut. Om enheten försöker att slutföra meddelandet när tids gränsen för låsning går ut, utlöses detta undantag av IoT Hub.

## <a name="solution"></a>Lösning

Om IoT Hub inte får meddelandet inom en tids gräns på en minut, ställer det tillbaka meddelandet i *kö* . Enheten kan försöka ta emot meddelandet igen. Om du vill förhindra att felet uppstår i framtiden implementerar du enhets sidans logik för att slutföra meddelandet inom en minut med att ta emot meddelandet. Timeout för en minut kan inte ändras.