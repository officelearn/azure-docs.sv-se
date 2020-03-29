---
title: Felsöka Azure IoT Hub-fel 412002 DeviceMessageLockLost
description: Förstå hur du åtgärdar fel 412002 DeviceMessageLockLost
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 66461b23432a3e8b7ae4ad1fdc078fba9ca05646
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960768"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

I den här artikeln beskrivs orsakerna och lösningarna för **412002 DeviceMessageLockLost-fel.**

## <a name="symptoms"></a>Symtom

NÃ¤s nÃ¤s fÃ¤ndes fÃ¤ndes fÃ¤ndesã¤nstÃ¤ser du intiga en gÃ¤s Ã¤ndar-fÃ¤nderning med felet **412002 DeviceMessageLockLost**.

## <a name="cause"></a>Orsak

När en enhet tar emot ett meddelande från molnet till [`ReceiveAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)enheten från kön (till exempel med hjälp) låses meddelandet av IoT Hub under en tidsgräns på en minut. Om enheten försöker slutföra meddelandet när låstidsgränsen har gått ut, genererar IoT Hub det här undantaget.

## <a name="solution"></a>Lösning

Om IoT Hub inte får meddelandet inom en minuts tidsgräns för lås, ställs meddelandet tillbaka till *enqueued-tillstånd.* Enheten kan försöka ta emot meddelandet igen. Om du vill förhindra att felet inträffar i framtiden implementerar du logik på enheten för att slutföra meddelandet inom en minut efter att du tagit emot meddelandet. Den här time-outen på en minut kan inte ändras.