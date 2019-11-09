---
title: Arkitektur koncept i Azure IoT Central | Microsoft Docs
description: Den här artikeln beskriver viktiga begrepp som rör arkitekturen i Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 25b0ec1b86a59b944cdb895bd536da32a1f8595b
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73884478"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central-arkitektur

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Den här artikeln ger en översikt över Microsoft Azure IoT Central-arkitekturen.

![Arkitektur på översta nivån](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Enheter

Enheter utbyter data med ditt Azure IoT Central-program. En enhet kan:

- Skicka mått som telemetri.
- Synkronisera inställningar med ditt program.

I Azure IoT Central anges de data som en enhet kan utbyta med ditt program i en enhets mall. Mer information om enhetsspecifika finns i hantering av [metadata](#metadata-management).

Mer information om hur enheter ansluter till ditt Azure IoT Central-program finns i [enhets anslutning](concepts-connectivity.md).

## <a name="cloud-gateway"></a>Cloud Gateway

Azure IoT Central använder Azure IoT Hub som en molnbaserad gateway som aktiverar enhets anslutningen. IoT Hub aktiverar:

- Data inmatning i skala i molnet.
- Enhets hantering.
- Skydda enhets anslutningen.

Mer information om IoT Hub finns i [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

Mer information om enhets anslutning i Azure IoT Central finns i [enhets anslutning](concepts-connectivity.md).

## <a name="data-stores"></a>Data lager

Azure IoT Central lagrar program data i molnet. Program data som lagras innehåller:

- Enhets mallar.
- Enhets identiteter.
- Metadata för enheten.
- Användar-och roll data.

Azure IoT Central använder ett tids serie lager för Mät data som skickas från dina enheter. Tids serie data från enheter som används av Analytics-tjänsten.

## <a name="analytics"></a>Analys

Analytics-tjänsten ansvarar för att skapa anpassade rapporterings data som visas i programmet. En operatör kan [Anpassa den analys](howto-create-analytics.md) som visas i programmet. Analytics-tjänsten bygger på [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) och bearbetar Mät data som skickas från dina enheter.

## <a name="rules-and-actions"></a>Regler och åtgärder

[Regler och åtgärder](howto-create-telemetry-rules.md) fungerar nära varandra för att automatisera uppgifter i programmet. Ett verktyg kan definiera regler baserat på enhets telemetri, till exempel temperaturen som överskrider ett definierat tröskelvärde. Azure IoT Central använder en Stream-processor för att fastställa när regel villkoren är uppfyllda. När ett regel villkor uppfylls utlöser den en åtgärd som definierats av verktyget. En åtgärd kan till exempel skicka ett e-postmeddelande för att meddela en tekniker om att temperaturen i en enhet är för hög.

## <a name="metadata-management"></a>Hantering av metadata

I ett Azure IoT Central-program definierar enhets mallarna beteende och funktion för enhets typer. En mall för kyl skåps enhet anger till exempel vilken telemetri ett kyl skåp skickar till ditt program.

![Arkitektur för mall](media/concepts-architecture/template_architecture.png)

I en enhets mall:

- **Mått** anger vilken telemetri som enheten skickar till programmet.
- **Inställningar** anger de konfigurationer som en operatör kan ange.
- **Egenskaper** anger metadata som en operatör kan ange.
- **Regler** automatisera beteendet i programmet baserat på data som skickas från en enhet.
- **Instrument paneler** är anpassningsbara vyer av en enhet i programmet.

Ett program kan ha en eller flera simulerade och riktiga enheter som baseras på varje enhets mall.

## <a name="data-export"></a>Data export

I ett Azure IoT Central-program kan du [kontinuerligt exportera dina data](howto-export-data-event-hubs-service-bus.md) till dina egna Azure Event Hubs-, Azure Service Bus-och Azure Blob Storage-instanser. IoT Central kan exportera mått, enheter och mallar för enheter.

## <a name="batch-device-updates"></a>Uppdateringar av batch-enhet

I ett Azure IoT Central-program kan du [skapa och köra jobb](howto-run-a-job.md) för att hantera anslutna enheter. Med de här jobben kan du göra Mass uppdateringar av enhets egenskaper eller inställningar eller köra kommandon. Du kan till exempel skapa ett jobb för att öka fläkt hastigheten för flera kylda Vending-datorer.

## <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)

En [administratör kan definiera åtkomst regler](howto-administer.md) för ett Azure IoT Central-program med hjälp av fördefinierade roller. En administratör kan tilldela användare till roller som fastställer vilka delar av programmet som användaren har åtkomst till.

## <a name="security"></a>Säkerhet

Säkerhetsfunktionerna i Azure IoT Central inkluderar:

- Data krypteras under överföring och i vila.
- Autentisering tillhandahålls antingen av Azure Active Directory eller Microsoft-konto. Autentisering med två faktorer stöds.
- Fullständig klient isolering.
- Säkerhet på enhets nivå.

## <a name="ui-shell"></a>UI-gränssnitt

UI Shell är ett modernt, överdrivet HTML5 webbläsarbaserat program.
En administratör kan anpassa programmets användar gränssnitt genom att använda anpassade teman och ändra hjälp länkar så att de pekar på dina egna anpassade hjälp resurser. Mer information om hur du anpassar gränssnittet finns i artikeln [Anpassa Azure IoT Central UI](howto-customize-ui.md) .

En operatör kan skapa anpassade instrument paneler för program. Du kan ha flera instrument paneler som visar olika data och växlar mellan dem.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om arkitekturen i Azure IoT Central, är det föreslagna nästa steg att lära dig om [enhets anslutning](concepts-connectivity.md) i Azure IoT Central.