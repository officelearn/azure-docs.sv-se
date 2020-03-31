---
title: Felsöka interna azure IoT Hub 500xxx-fel
description: Förstå hur du åtgärdar interna fel på 500xxx
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 7f3f5177e084693c45bed1088a4e1d091be100ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271049"
---
# <a name="500xxx-internal-errors"></a>500xxx Interna fel

I den här artikeln beskrivs orsaker och lösningar för **interna fel på 500xxx**.

## <a name="symptoms"></a>Symtom

Din begäran till IoT Hub misslyckas med ett fel som börjar med 500 och/eller någon form av "serverfel". Några möjligheter är:

* **500001 ServerError**: IoT Hub stötte på ett problem på serversidan.

* **500008 GenericTimeout:** IoT Hub kunde inte slutföra anslutningsbegäran innan du tajmar ut.

* **ServiceUnavailable (ingen felkod)**: IoT Hub påträffade ett internt fel.

* **InternalServerError (ingen felkod):** IoT Hub påträffade ett internt fel.

## <a name="cause"></a>Orsak

Det kan finnas ett antal orsaker till ett 500xxx-felsvar. I samtliga fall är problemet troligen övergående. Medan IoT Hub-teamet arbetar hårt för att underhålla [SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/)kan små delmängder av IoT Hub-noder ibland uppleva tillfälliga fel. När enheten försöker ansluta till en nod som har problem visas det här felet.

## <a name="solution"></a>Lösning

Om du vill minska 500xxx-fel utfärdar du ett nytt försök från enheten. Om du [vill hantera återförsök automatiskt](./iot-hub-reliability-features-in-sdks.md#connection-and-retry)kontrollerar du att du använder den senaste versionen av Azure [IoT SDK:er](./iot-hub-devguide-sdks.md). Bästa praxis för tillfällig felhantering och återförsök finns i [Övergående felhantering](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults).  Om problemet kvarstår kontrollerar du [Resurshälsa](./iot-hub-monitor-resource-health.md#use-azure-resource-health) och [Azure-status](https://status.azure.com/) för att se om IoT Hub har ett känt problem. Du kan också använda den [manuella redundansfunktionen](./tutorial-manual-failover.md). Om det inte finns några kända problem och problemet kvarstår, [kontakta support](https://azure.microsoft.com/support/options/) för vidare utredning.
