---
title: Felsöka Azure IoT Hub-fel 404103 DeviceNotOnline
description: Förstå hur du åtgärdar fel 404103 DeviceNotOnline
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960820"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

I den här artikeln beskrivs orsakerna till och lösningarna för **404103 DeviceNotOnline-fel.**

## <a name="symptoms"></a>Symtom

En direkt metod till en enhet misslyckas med felet **404103 DeviceNotOnline** även om enheten är online. 

## <a name="cause"></a>Orsak

Om du vet att enheten är online och fortfarande får felet beror det sannolikt på att direktmetodens motringning inte är registrerad på enheten.

## <a name="solution"></a>Lösning

Information om hur du konfigurerar enheten korrekt för direkt metodåteruppringning finns i [Hantera en direkt metod på en enhet](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device).