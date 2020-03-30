---
title: Funktioner för kunddatabegäran för Azure DPS-enheter
description: För enheter som hanteras i DPS (Azure Device Provisioning Service) som är personliga visar den här artikeln administratörer hur du exporterar eller tar bort personuppgifter.
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 1dcf1b9f62f94b8f75ef2fe77f3e237a387c53eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890656"
---
# <a name="summary-of-customer-data-request-features"></a>Sammanfattning av funktionerna för kunddatabegäran

Azure IoT Hub Device Provisioning Service är en REST API-baserad molntjänst som riktar sig till företagskunder som möjliggör sömlös, automatiserad zero-touch-etablering av enheter till Azure IoT Hub med säkerhet som börjar på enheten och slutar med molnet.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Enskilda enheter tilldelas ett registrerings-ID och enhets-ID av en klientadministratör. Data från och om dessa enheter baseras på dessa ID:er. Microsoft har ingen information och har ingen tillgång till data som skulle tillåta korrelation av dessa enheter till en individ.

Många av de enheter som hanteras i enhetsetableringstjänsten är inte personliga enheter, till exempel en kontorstermostat eller fabriksrobot. Kunder kan dock betrakta vissa enheter som personligt identifierbara och efter eget gottfinnande kan behålla sina egna metoder för tillgång eller lagerspårning som binder enheter till individer. Enhetsetableringstjänsten hanterar och lagrar alla data som är associerade med enheter som om det vore personuppgifter.

Klientadministratörer kan använda antingen Azure-portalen eller tjänstens REST-API:er för att uppfylla informationsbegäranden genom att exportera eller ta bort data som är associerade med ett enhets-ID eller registrerings-ID.

> [!NOTE]
> Enheter som har etablerats i Azure IoT Hub via enhetsetableringstjänsten har ytterligare data som lagras i Azure IoT Hub-tjänsten. Se [referensdokumentationen för Azure IoT Hub](../iot-hub/iot-hub-customer-data-requests.md) för att slutföra en fullständig begäran om en viss enhet.

## <a name="deleting-customer-data"></a>Ta bort kunddata

Enhetsetableringstjänsten lagrar registreringar och registreringsposter. Registreringar innehåller information om enheter som tillåts etableras, och registreringsposter visar vilka enheter som redan har gått igenom etableringsprocessen.

Klientadministratörer kan ta bort registreringar från Azure-portalen, och detta tar också bort alla associerade registreringsposter.

Mer information finns i [Så här hanterar du enhetsregistreringar](how-to-manage-enrollments.md).

Det är också möjligt att utföra borttagningsåtgärder för registreringar och registreringsposter med REST-API:er:

* Om du vill ta bort registreringsinformation för en enskild enhet kan du använda [Enhetsregistrering - Ta bort](/rest/api/iot-dps/deleteindividualenrollment/deleteindividualenrollment).
* Om du vill ta bort registreringsinformation för en grupp enheter kan du använda [Enhetsregistreringsgrupp - Ta bort](/rest/api/iot-dps/deleteenrollmentgroup/deleteenrollmentgroup).
* Om du vill ta bort information om enheter som har etablerats kan du använda [registreringstillstånd - Ta bort registreringstillstånd](/rest/api/iot-dps/deletedeviceregistrationstate/deletedeviceregistrationstate).

## <a name="exporting-customer-data"></a>Exportera kunddata

Enhetsetableringstjänsten lagrar registreringar och registreringsposter. Registreringar innehåller information om enheter som tillåts etableras, och registreringsposter visar vilka enheter som redan har gått igenom etableringsprocessen.

Klientadministratörer kan visa registreringar och registreringsposter via Azure-portalen och exportera dem med hjälp av kopiera och klistra in.

Mer information om hur du hanterar registreringar finns i [Så här hanterar du enhetsregistreringar](how-to-manage-enrollments.md).

Det är också möjligt att utföra exportåtgärder för registreringar och registreringsposter med REST API:er:

* Om du vill exportera registreringsinformation för en enskild enhet kan du använda [Enhetsregistrering - Get](/rest/api/iot-dps/getindividualenrollment/getindividualenrollment).
* Om du vill exportera registreringsinformation för en grupp enheter kan du använda [Enhetsregistreringsgrupp - Get](/rest/api/iot-dps/getenrollmentgroup/getenrollmentgroup).
* Om du vill exportera information om enheter som redan har etablerats kan du använda [registreringstillstånd - Hämta registreringstillstånd](/rest/api/iot-dps/getdeviceregistrationstate/getdeviceregistrationstate).

> [!NOTE]
> När du använder Microsofts företagstjänster genererar Microsoft viss information, så kallade systemgenererade loggar. Vissa systemgenererade loggar för enhetsetableringstjänsten är inte tillgängliga eller kan exporteras av klientadministratörer. Dessa loggar utgör faktiska åtgärder som utförs inom tjänsten och diagnostiska data relaterade till enskilda enheter.

## <a name="links-to-additional-documentation"></a>Länkar till ytterligare dokumentation

Fullständig dokumentation för API:er för [https://docs.microsoft.com/rest/api/iot-dps](https://docs.microsoft.com/rest/api/iot-dps)enhetsetableringstjänster finns på .

Azure IoT Hub [kunddata begäran funktioner](../iot-hub/iot-hub-customer-data-requests.md).