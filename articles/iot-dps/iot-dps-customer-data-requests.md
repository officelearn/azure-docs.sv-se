---
title: Kunden datafunktioner för begäran
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
ms.openlocfilehash: 9600d927ffefa8a211afcb3fe2bf7510aafc9fc0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="summary-of-customer-data-request-features"></a>Sammanfattning av kunden datafunktioner för begäran

Azure IoT-hubb Device etablering Service är en REST API-baserade molntjänst riktad mot enterprise-kunder som möjliggör sömlös, automatiserad zero touch etablering av enheter till Azure IoT-hubb med säkerhet som börjar på enheten och slutar med molnet.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Enskilda enheter tilldelas en registrering ID och enhets-ID som en Innehavaradministratör. Data från och om enheterna är baserat på dessa ID: N. Microsoft har ingen information och har inte åtkomst till data som skulle låta korrelation av dessa enheter till en enskild person.

Många av de enheter som hanteras i Azure etablering av tjänst är inte personliga enheter, till exempel ett office termostat eller factory robot. Kunder kan emellertid tänka vissa enheter ska vara identifierbar och deras gottfinnande behålla sina egna tillgång eller inventering spårning metoder som koppla enheter till enskilda användare. Tjänsten för etablering av Azure enheten hanterar och lagrar alla data som associeras med enheter som om det vore personliga data.

Innehavaradministratörer kan använda Azure-portalen eller tjänstens REST API: er för att uppfylla begäranden om information genom att exportera eller ta bort data som är associerade med en enhets-ID eller registrering-ID.

> [!NOTE]
> Enheter som har etablerats i Azure IoT Hub via Azure IoT-hubb Device etablering Service har ytterligare data som lagras i Azure IoT Hub-tjänsten. Finns det [Azure IoT Hub-referensdokumentation](../iot-hub/iot-hub-customer-data-requests.md) för att slutföra en fullständig begäran om en viss enhet.

## <a name="deleting-customer-data"></a>Om du tar bort kundinformation

Azure IoT-hubb enheten Etableringstjänsten lagrar registreringar och Registreringsposter. Registreringar innehåller information om enheter som ska kunna etableras och registrering poster visas enheter redan har gått igenom etableringsprocessen.

Innehavaradministratörer kan ta bort registreringar från Azure-portalen och Registreringsposter tas bort.

Mer information finns i [hantera enhetsregistreringar](how-to-manage-enrollments.md).

Det är också möjligt att utföra delete-åtgärder för registreringar och Registreringsposter med hjälp av REST API: er:

* Du kan använda för att ta bort registreringsinformation om för en enskild enhet [Enhetsregistrering - ta bort](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollment/delete).
* Du kan använda för att ta bort registreringsinformation om för en grupp av enheter, [registrering enhetsgrupp - ta bort](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollmentgroup/delete).
* Du kan använda för att ta bort information om enheter som har etablerats [registreringstillstånd - ta bort registreringstillstånd](https://docs.microsoft.com/rest/api/iot-dps/registrationstate/deleteregistrationstate).

## <a name="exporting-customer-data"></a>Exportera kundinformation

Azure IoT-hubb enheten Etableringstjänsten lagrar registreringar och Registreringsposter. Registreringar innehåller information om enheter som ska kunna etableras och registrering poster visas enheter redan har gått igenom etableringsprocessen.

Innehavaradministratörer kan visa registreringar och Registreringsposter via Azure portal och exportera dem med hjälp av kopiera och klistra in.

Mer information om hur du hanterar registreringar finns [hantera enhetsregistreringar](how-to-manage-enrollments.md).

Det är också möjligt att utföra exportåtgärder för registreringar och Registreringsposter med hjälp av REST API: er:

* Du kan använda för att exportera registreringsinformation för en enda enhet, [Enhetsregistrering - Get](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollment/get).
* Om du vill exportera registreringsinformation för en grupp av enheter, kan du använda [registrering enhetsgrupp - Get](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollmentgroup/get).
* Om du vill exportera information om enheter som redan har etablerats kan du använda [registreringstillstånd - Get registreringstillstånd](https://docs.microsoft.com/rest/api/iot-dps/registrationstate/getregistrationstate).

> [!NOTE]
> När du använder Microsoft enterprise services genererar Microsoft viss information kallas systemgenererade loggar. Vissa Azure IoT Hub enheten etablering systemgenererade tjänstloggar är inte tillgänglig eller exportera av innehavaradministratörer. Dessa loggar utgöra faktiska åtgärder i tjänsten och diagnostikdata som rör enskilda enheter.

## <a name="links-to-additional-documentation"></a>Länkar till ytterligare dokumentation

Fullständig dokumentation för Azure IoT Hub-Enhetsetableringen service API: er finns i [ https://docs.microsoft.com/rest/api/iot-dps ](https://docs.microsoft.com/rest/api/iot-dps).

Azure IoT-hubb [kundinformation begära funktioner](../iot-hub/iot-hub-customer-data-requests.md).