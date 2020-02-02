---
title: Fel sökning av Azure IoT Hub-fel 409001 DeviceAlreadyExists
description: Förstå hur du åtgärdar fel 409001 DeviceAlreadyExists
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960794"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

I den här artikeln beskrivs orsaker och lösningar för **409001 DeviceAlreadyExists** -fel.

## <a name="symptoms"></a>Symtom

När du försöker registrera en enhet i IoT Hub Miss lyckas begäran med felet **409001 DeviceAlreadyExists**.

## <a name="cause"></a>Orsak

Det finns redan en enhet med samma enhets-ID i IoT Hub. 

## <a name="solution"></a>Lösning

Använd ett annat enhets-ID och försök igen.