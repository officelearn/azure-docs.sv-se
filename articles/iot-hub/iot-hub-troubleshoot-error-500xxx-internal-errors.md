---
title: Felsöka internt fel i Azure IoT Hub 500xxx
description: Lär dig hur du åtgärdar 500xxx internt fel
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f83ec4d09389d91c2dd427c9840885c0b829eafb
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150316"
---
# <a name="500xxx-internal-errors"></a>500xxx Interna fel

I den här artikeln beskrivs orsaker och lösningar för **500Xxx internt fel**.

## <a name="symptoms"></a>Symtom

Din begäran om IoT Hub Miss lyckas med ett fel som börjar med 500 och/eller en viss typ av "Server fel". Några möjligheter är:

* **500001 ServerError**: IoT Hub stötte på ett problem på Server sidan.

* **500008 GenericTimeout**: det gick inte att slutföra anslutningsbegäran innan tids gränsen nåddes för IoT Hub.

* **ServiceUnavailable (ingen felkod)**: IoT Hub påträffade ett internt fel.

* **InternalServerError (ingen felkod)**: IoT Hub påträffade ett internt fel.

## <a name="cause"></a>Orsak

Det kan finnas ett antal orsaker till ett 500xxx-felsvar. I samtliga fall är problemet troligt vis tillfälligt. Medan IoT Hub team arbetar hårt för att underhålla [service avtalet](https://azure.microsoft.com/support/legal/sla/iot-hub/)kan små del mängder av IoT Hub-noder ibland uppleva tillfälliga fel. När enheten försöker ansluta till en nod som har problem får du det här felet.

## <a name="solution"></a>Lösning

Om du vill undvika 500xxx-fel skickar du ett nytt försök från enheten. Kontrol lera att du använder den senaste versionen av [Azure IoT SDK](./iot-hub-devguide-sdks.md): er för att [automatiskt hantera återförsök](./iot-hub-reliability-features-in-sdks.md#connection-and-retry). Bästa praxis vid hantering av tillfälliga fel och återförsök finns i [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).  Om problemet kvarstår kontrollerar du [Resource Health](./iot-hub-monitor-resource-health.md#use-azure-resource-health) och Azure- [status](https://status.azure.com/) för att se om IoT Hub har ett känt problem. Du kan också använda [funktionen manuell redundans](./tutorial-manual-failover.md). Om det inte finns några kända problem och problemet kvarstår kan du [kontakta supporten](https://azure.microsoft.com/support/options/) för ytterligare undersökning.