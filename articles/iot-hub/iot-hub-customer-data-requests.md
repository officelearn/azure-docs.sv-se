---
title: Kunden datafunktioner för begäran
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
ms.openlocfilehash: 3af76fe22e93b6c5d502733196994bda61b9a93e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="summary-of-customer-data-request-features"></a>Sammanfattning av kunden datafunktioner för begäran

Azure IoT Hub är en REST API-baserad tjänst i molnet riktad mot enterprise-kunder som aktiverar säker, dubbelriktad kommunikation mellan miljoner enheter och en partitionerad Azure-tjänsten.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Enskilda enheter tilldelas en enhets-ID (enhets-ID) som en Innehavaradministratör. Data på enheten är baserad på tilldelade enhets-ID. Microsoft har ingen information och har inte åtkomst till data som skulle låta enhets-ID till användaren korrelation.

Många av de enheter som hanteras i Azure IoT Hub är inte personliga enheter, till exempel ett office termostat eller factory robot. Kunder kan emellertid tänka vissa enheter ska vara identifierbar och deras gottfinnande behålla sina egna tillgång eller inventering spårning metoder som koppla enheter till enskilda användare. Azure IoT-hubb hanterar och lagrar alla data som associeras med enheter som om det vore personliga data.

Innehavaradministratörer kan använda Azure-portalen eller tjänstens REST API: er för att uppfylla begäranden om information genom att exportera eller ta bort data som är associerade med en enhets-ID.

Om du använder funktionen routning i Azure IoT Hub-tjänsten för att vidarebefordra meddelanden till andra tjänster, måste sedan databegäranden utföras av innehavaradministration för varje routningsdomän slutpunkt för att kunna slutföra en fullständig begäran om en viss enhet. Finns i referensdokumentationen för varje slutpunkt för mer information. Mer information om stöds slutpunkter finns [referens - IoT-hubbslutpunkter](iot-hub-devguide-endpoints.md).

Om du använder funktionen Azure händelse rutnätet integration av Azure IoT Hub-tjänsten måste sedan databegäranden utföras av klient-administratören för varje prenumerant av dessa händelser. Mer information finns i [reagerar på händelser för IoT-hubb med hjälp av händelse rutnätet](iot-hub-event-grid.md).

Om du använder funktionen Azure-Monitor integration av Azure IoT Hub-tjänsten för att skapa diagnostikloggar måste begäranden utföras av innehavaradministration mot lagrade loggarna. Mer information finns i [övervaka hälsotillståndet hos Azure IoT Hub](iot-hub-monitor-resource-health.md).

## <a name="deleting-customer-data"></a>Om du tar bort kundinformation

Innehavaradministratörer kan använda bladet IoT-enheter till Azure IoT Hub-tillägget i Azure-portalen för att ta bort en enhet som tar bort data som hör till enheten.

Det är också möjligt att utföra delete-åtgärder för enheter med hjälp av REST API: er. Mer information finns i [Device Api - ta bort enheten](https://docs.microsoft.com/rest/api/iothub/deviceapi/deletedevice).

## <a name="exporting-customer-data"></a>Exportera kundinformation

Innehavaradministratörer kan använda kopiera och klistra in i bladet IoT-enheter till Azure IoT Hub-tillägget i Azure portal för att exportera data som är associerade med en enhet.

Det är också möjligt att utföra exportåtgärder för enheter med hjälp av REST API: er. Mer information finns i [Device Api - hämta enheten](https://docs.microsoft.com/rest/api/iothub/deviceapi/getdevice).

> [!NOTE]
> När du använder Microsoft enterprise services genererar Microsoft viss information kallas systemgenererade loggar. Vissa Azure IoT Hub systemgenererade loggar är inte tillgänglig eller exportera av innehavaradministratörer. Dessa loggar utgöra faktiska åtgärder i tjänsten och diagnostikdata som rör enskilda enheter.

## <a name="links-to-additional-documentation"></a>Länkar till ytterligare dokumentation

Fullständig dokumentation för Azure IoT Hub-enhet API: er finns i [ https://docs.microsoft.com/rest/api/iothub/deviceapi ](https://docs.microsoft.com/rest/api/iothub/deviceapi).