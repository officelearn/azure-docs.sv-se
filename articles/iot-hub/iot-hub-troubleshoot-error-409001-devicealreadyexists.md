---
title: Felsökning av Azure IoT Hub-fel 409001-enhet Redan
description: Förstå hur du åtgärdar fel 409001 DeviceAlreadyExists
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960794"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

I den här artikeln beskrivs orsaker och lösningar för **409001 DeviceAlreadyExists** fel.

## <a name="symptoms"></a>Symtom

NÃ¤s fÃ¤ndes fÃ¤ndes fÃ¤nder en enhet i IoT Hub misslyckas begäran med felet **409001 DeviceAlreadyExists**.

## <a name="cause"></a>Orsak

Det finns redan en enhet med samma enhets-ID i IoT-hubben. 

## <a name="solution"></a>Lösning

Använd ett annat enhets-ID och försök igen.