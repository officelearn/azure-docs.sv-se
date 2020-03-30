---
title: Felsökning av Azure IoT Hub-fel 403006 DeviceMaximumActiveFileUploadLimitExceed
description: Förstå hur du åtgärdar fel 403006 DeviceMaximumActiveFileUploadLimitExceed
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1e3c05e4cc3ccf34573b55d3729aded16e26d66e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960846"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

I den här artikeln beskrivs orsakerna och lösningarna för **403006 DeviceMaximumActiveFileUploadLimitExceed-fel.**

## <a name="symptoms"></a>Symtom

Begäran om filöverföring misslyckas med felkoden **403006** och meddelandet "Antalet begäranden om aktiv filöverföring får inte överstiga 10".

## <a name="cause"></a>Orsak

Varje enhetsklient är begränsad till [10 samtidiga filuppladdningar](./iot-hub-devguide-quotas-throttling.md#other-limits). 

Du kan enkelt överskrida gränsen om enheten inte meddelar IoT Hub när filuppladdningarna är slutförda. Det här problemet orsakas ofta av ett otillförlitligt nätverk på enhetens sida.

## <a name="solution"></a>Lösning

Se till att enheten snabbt kan [meddela IoT Hub-filuppladdningen.](./iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload) Försök sedan [att minska SAS-token TTL för filuppladdningskonfiguration](iot-hub-configure-file-upload.md).

## <a name="next-steps"></a>Nästa steg

Mer information om filöverföringar finns i [Ladda upp filer med IoT Hub](./iot-hub-devguide-file-upload.md) och Konfigurera [IoT Hub-filöverföringar med Azure-portalen](./iot-hub-configure-file-upload.md).