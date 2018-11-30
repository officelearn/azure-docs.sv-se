---
title: Kundens datafunktioner
author: dominicbetts
ms.author: dobett
manager: timlt
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 5dd027c886e8102e77ddefe93817daee0e1ec29b
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52584161"
---
# <a name="summary-of-customer-data-request-features"></a>Sammanfattning av kundens datafunktioner

Azure IoT Hub Device Provisioning-tjänsten är en REST API-baserad molntjänst riktad mot företagskunder som möjliggör sömlös, automatiserad etablering av enheter till Azure IoT Hub med säkerhet som börjar vid enheten och slutar med molnet.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Enskilda enheter tilldelas en registrerings-ID: T och enhets-ID som en Innehavaradministratör. Data från och om dessa enheter baseras på dessa ID: N. Microsoft har ingen information och har ingen åtkomst till data som skulle låta korrelation av dessa enheter till en enskild person.

Många av de enheter som hanteras i Device Provisioning-tjänsten är inte personliga enheter, till exempel en office termostat eller factory robot. Kunder kan dock tänka på vissa enheter är personligt identifierbar och deras godtycke kan underhålla sina egna tillgång eller inventeringssamling metoder som koppla enheter till enskilda användare. Device Provisioning-tjänsten hanterar och lagrar alla data kopplade till enheter som om det vore personliga data.

Innehavaradministratörer kan använda Azure portal eller tjänstens REST API: er för att utföra information begäranden genom att exportera eller ta bort data som är associerade med en enhets-ID eller registrering-ID.

> [!NOTE]
> Enheter som har etablerats i Azure IoT Hub via Device Provisioning-tjänsten har ytterligare data som lagras i Azure IoT Hub-tjänsten. Se den [referensdokumentation för Azure IoT Hub](../iot-hub/iot-hub-customer-data-requests.md) för att kunna slutföra en fullständig begäran för en given enhet.

## <a name="deleting-customer-data"></a>Tar bort kunddata

Device Provisioning-tjänsten lagrar registreringar och av Registreringsposter. Registreringar innehåller information om enheter som ska kunna etableras och registrering posterna visar vilka enheter som redan har gått igenom etableringsprocessen.

Innehavaradministratörer kan ta bort registreringar från Azure-portalen och detta tar bort alla associerade Registreringsposter.

Mer information finns i [hantera enhetsregistreringar](how-to-manage-enrollments.md).

Det är också möjligt att utföra delete-åtgärder för registreringar och av Registreringsposter med hjälp av REST API: er:

* Du kan använda för att ta bort registreringsinformation för en enskild enhet [Enhetsregistrering – ta bort](/rest/api/iot-dps/deleteindividualenrollment/deleteindividualenrollment).
* Om du vill ta bort registreringsinformation för en grupp av enheter, kan du använda [grupp för registrering av enheter – ta bort](/rest/api/iot-dps/deleteenrollmentgroup/deleteenrollmentgroup).
* Du kan använda för att ta bort information om enheter som har etablerats [registreringstillstånd - ta bort registreringstillstånd](/rest/api/iot-dps/deletedeviceregistrationstate/deletedeviceregistrationstate).

## <a name="exporting-customer-data"></a>Exportera kunddata

Device Provisioning-tjänsten lagrar registreringar och av Registreringsposter. Registreringar innehåller information om enheter som ska kunna etableras och registrering posterna visar vilka enheter som redan har gått igenom etableringsprocessen.

Innehavaradministratörer kan visa registreringar och Registreringsposter via Azure portal och exportera dem med hjälp av kopiera och klistra in.

Läs mer om hur du hanterar registreringar [hantera enhetsregistreringar](how-to-manage-enrollments.md).

Det är också möjligt att utföra exportåtgärder för registreringar och av Registreringsposter med hjälp av REST API: er:

* Du kan använda för att exportera registreringsinformationen för en enskild enhet, [Enhetsregistrering – Get](/rest/api/iot-dps/getindividualenrollment/getindividualenrollment).
* Om du vill exportera registreringsinformation för en grupp av enheter, kan du använda [grupp för registrering av enhet - Get](/rest/api/iot-dps/getenrollmentgroup/getenrollmentgroup).
* Om du vill exportera information om enheter som redan har etablerats kan du använda [registreringstillstånd - Get-registreringstillstånd](/rest/api/iot-dps/getdeviceregistrationstate/getdeviceregistrationstate).

> [!NOTE]
> När du använder Microsoft enterprise services genererar Microsoft viss information som kallas systemgenererade loggar. Vissa systemgenererade loggar i Device Provisioning-tjänsten är inte tillgänglig eller exportera av innehavaradministratörer. Dessa loggar utgör faktiska åtgärder i tjänsten och diagnostiska data som är relaterade till enskilda enheter.

## <a name="links-to-additional-documentation"></a>Länkar till ytterligare dokumentation

Fullständig dokumentation för Device Provisioning Service API: er finns i [ https://docs.microsoft.com/rest/api/iot-dps ](https://docs.microsoft.com/rest/api/iot-dps).

Azure IoT Hub [kunddata begär funktioner](../iot-hub/iot-hub-customer-data-requests.md).