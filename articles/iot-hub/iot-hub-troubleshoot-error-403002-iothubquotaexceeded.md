---
title: Felsökning av Azure IoT Hub-fel 403002 IoTHubQuotaExceed
description: Förstå hur du åtgärdar fel 403002 IoTHubQuotaExceed
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 8312c3267e826088f34b3bffe1520703eec00bdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961119"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

I den här artikeln beskrivs orsakerna och lösningarna för **403002 IoTHubQuotaExceed-fel.**

## <a name="symptoms"></a>Symtom

Alla begäranden till IoT Hub misslyckas med felet **403002 IoTHubQuotaExceed.** I Azure-portalen läses inte IoT-hubbenhetslistan in.

## <a name="cause"></a>Orsak

Den dagliga meddelandekvoten för IoT-hubben överskrids. 

## <a name="solution"></a>Lösning

[Uppgradera eller öka antalet enheter på IoT-hubben](iot-hub-upgrade.md) eller vänta på nästa UTC-dag innan den dagliga kvoten ska uppdateras.

## <a name="next-steps"></a>Nästa steg

* Information om hur åtgärder räknas in i kvoten, till exempel dubbla frågor och direkta metoder, finns [i Förstå IoT Hub-prissättning](iot-hub-devguide-pricing.md#charges-per-operation)
* Om du vill ställa in övervakning för daglig kvotanvändning ställer du in en avisering med måttet *Totalt antal meddelanden som används*. Stegvisa instruktioner finns i [Konfigurera mått och aviseringar med IoT Hub](tutorial-use-metrics-and-diags.md#set-up-metrics)