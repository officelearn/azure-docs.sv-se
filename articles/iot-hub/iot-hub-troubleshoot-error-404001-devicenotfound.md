---
title: Felsöka Azure IoT Hub-fel 404001 DeviceNotFound
description: Förstå hur du åtgärdar fel 404001 DeviceNotFound
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 15aa21c2ec2c11bb251f7208fd22c92ceb859d6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960833"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

I den här artikeln beskrivs orsakerna till och lösningarna för **404001 DeviceNotFound-fel.**

## <a name="symptoms"></a>Symtom

Under en C2D-kommunikation mellan molnet och enheten, till exempel C2D-meddelande, dubbel uppdatering eller direkt metod, misslyckas åtgärden med fel **404001 DeviceNotFound**.

## <a name="cause"></a>Orsak

Åtgärden misslyckades eftersom enheten inte kan hittas av IoT Hub. Enheten är antingen inte registrerad eller inaktiverad.

## <a name="solution"></a>Lösning

Registrera enhets-ID:t som du använde och försök sedan igen.