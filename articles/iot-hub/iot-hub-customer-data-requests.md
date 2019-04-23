---
title: Kundens datafunktioner
description: Sammanfattning av kundens datafunktioner
author: robinsh
manager: philmea
ms.author: robinsh
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 1519637eddf909040131a1efac5738fc7cc8e565
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791698"
---
# <a name="summary-of-customer-data-request-features"></a>Sammanfattning av kundens datafunktioner

Azure IoT Hub är en REST API-baserad molntjänst riktad mot företagskunder som möjliggör säker, dubbelriktad kommunikation mellan miljontals enheter och en partitionerad Azure-tjänst.

[!INCLUDE [gdpr-related guidance](../../includes/gdpr-intro-sentence.md)]

Enskilda enheter tilldelas en enhets-ID (enhets-ID) som en Innehavaradministratör. Enhetsdata baseras på tilldelade enhets-ID. Microsoft har ingen information och har ingen åtkomst till data som skulle låta enhets-ID för användaren korrelation.

Många av de enheter som hanteras i Azure IoT Hub är inte personliga enheter, till exempel en office termostat eller factory robot. Kunder kan dock tänka på vissa enheter är personligt identifierbar och deras godtycke kan underhålla sina egna tillgång eller inventeringssamling metoder som koppla enheter till enskilda användare. Azure IoT Hub hanterar och lagrar alla data kopplade till enheter som om det vore personliga data.

Innehavaradministratörer kan använda Azure portal eller tjänstens REST API: er för att utföra information begäranden genom att exportera eller ta bort data som är associerade med ett enhets-ID.

Om du använder funktionen routning i Azure IoT Hub-tjänsten för att vidarebefordra meddelanden från enhet till andra tjänster kan måste sedan databegäranden utföras av administratör för varje routning slutpunkt för att kunna slutföra en fullständig begäran för en given enhet. Mer information finns i referensdokumentationen för varje slutpunkt. Mer information om slutpunkter som stöds finns i [referens – IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md).

Om du använder funktionen Azure Event Grid-integration i Azure IoT Hub-tjänsten måste sedan databegäranden utföras av administratör för varje prenumerant av dessa händelser. Mer information finns i [reagera på IoT Hub-händelser med hjälp av Event Grid](iot-hub-event-grid.md).

Om du använder funktionen Azure Monitor-integrering i Azure IoT Hub-tjänsten för att skapa diagnostikloggar, måste databegäranden utföras av administratör mot lagrade loggarna. Mer information finns i [övervaka hälsotillståndet för Azure IoT Hub](iot-hub-monitor-resource-health.md).

## <a name="deleting-customer-data"></a>Tar bort kunddata

Innehavaradministratörer kan använda bladet IoT-enheter i Azure IoT Hub-tillägget i Azure-portalen för att ta bort en enhet, vilket tar bort data som hör till enheten.

Det är också möjligt att utföra delete-åtgärder för enheter med hjälp av REST API: er. Mer information finns i [Service – ta bort enheten](/rest/api/iothub/service/deletedevice).

## <a name="exporting-customer-data"></a>Exportera kunddata

Innehavaradministratörer kan använda kopiera och klistra in inom bladet IoT-enheter i Azure IoT Hub-tillägget i Azure portal för att exportera data som hör till en enhet.

Det är också möjligt att utföra exportåtgärder för enheter med hjälp av REST API: er. Mer information finns i [Service – få enheten](/rest/api/iothub/service/getdevice).

> [!NOTE]
> När du använder Microsoft enterprise services genererar Microsoft viss information som kallas systemgenererade loggar. Vissa Azure IoT Hub systemgenererade loggar är inte tillgänglig eller exportera av innehavaradministratörer. Dessa loggar utgör faktiska åtgärder i tjänsten och diagnostiska data som är relaterade till enskilda enheter.

## <a name="links-to-additional-documentation"></a>Länkar till ytterligare dokumentation

Fullständig dokumentation för Azure IoT Hub Service API: er finns i [IoT Hub Service API: er](https://docs.microsoft.com/rest/api/iothub/service).
