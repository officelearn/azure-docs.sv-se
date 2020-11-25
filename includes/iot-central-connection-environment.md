---
title: ta med fil
description: ta med fil
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 11/03/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 03df889c44f7a375a15f110ec7b859656f8dcf8b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017526"
---
Ställ in miljövariabler för att konfigurera exemplet. Följande kodfragment visar hur du ställer in miljövariablerna i kommando tolken i Windows. Om du använder ett **bash** -gränssnitt ersätter du `set` kommandona med `export` kommandon:

```cmd
set IOTHUB_DEVICE_SECURITY_TYPE=DPS
set IOTHUB_DEVICE_DPS_ID_SCOPE=<The ID scope you made a note of previously>
set IOTHUB_DEVICE_DPS_DEVICE_ID=sample-device-01
set IOTHUB_DEVICE_DPS_DEVICE_KEY=<The group primary key you made a note of previously>
set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
```
