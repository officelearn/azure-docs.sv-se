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
ms.openlocfilehash: abc09ac1a13537c31fe96fae14edefd0d06b6aef
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880259"
---
# <a name="azure-iot-central-architecture-preview-features"></a>Azure IoT Central Architecture (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Den här artikeln ger en översikt över Microsoft Azure IoT Central-arkitekturen.

![Arkitektur på översta nivån](media/concepts-architecture-pnp/architecture.png)

## <a name="devices"></a>Enheter

Enheter utbyter data med ditt Azure IoT Central-program. En enhet kan:

- Skicka mått som telemetri.
- Synkronisera inställningar med ditt program.

I Azure IoT Central anges de data som en enhet kan utbyta med ditt program i en enhets mall. Mer information om enhetsspecifika finns i hantering av [metadata](#metadata-management).

Mer information om hur enheter ansluter till ditt Azure IoT Central-program finns i [enhets anslutning](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="cloud-gateway"></a>Cloud Gateway

Azure IoT Central använder Azure IoT Hub som en molnbaserad gateway som aktiverar enhets anslutningen. IoT Hub aktiverar:

- Data inmatning i skala i molnet.
- Enhets hantering.
- Skydda enhets anslutningen.

Mer information om IoT Hub finns i [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

Mer information om enhets anslutning i Azure IoT Central finns i [enhets anslutning](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="data-stores"></a>Datalager

Azure IoT Central lagrar program data i molnet. Program data som lagras innehåller:

- Enhets mallar.
- Enhets identiteter.
- Metadata för enheten.
- Användar-och roll data.

Azure IoT Central använder ett tids serie lager för Mät data som skickas från dina enheter. Tids serie data från enheter som används av Analytics-tjänsten.

## <a name="analytics"></a>Analytics

Analytics-tjänsten ansvarar för att skapa anpassade rapporterings data som visas i programmet. En operatör kan [Anpassa den analys](howto-create-analytics.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) som visas i programmet. Analytics-tjänsten bygger på [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) och bearbetar Mät data som skickas från dina enheter.

## <a name="rules-and-actions"></a>Regler och åtgärder

[Regler och åtgärder](howto-create-telemetry-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) fungerar nära varandra för att automatisera uppgifter i programmet. Ett verktyg kan definiera regler baserat på enhets telemetri, till exempel temperaturen som överskrider ett definierat tröskelvärde. Azure IoT Central använder en Stream-processor för att fastställa när regel villkoren är uppfyllda. När ett regel villkor uppfylls utlöser den en åtgärd som definierats av verktyget. En åtgärd kan till exempel skicka ett e-postmeddelande för att meddela en tekniker om att temperaturen i en enhet är för hög.

## <a name="metadata-management"></a>Hantering av metadata

I ett Azure IoT Central-program definierar enhets mallarna beteende och funktion för enhets typer. En mall för kyl skåps enhet anger till exempel vilken telemetri ett kyl skåp skickar till ditt program.

![Arkitektur för mall](media/concepts-architecture-pnp/template-architecture.png)

I en IoT Central för hands version av program enhet:

- **Enhets kapacitets modeller** anger funktioner för en enhet, till exempel den telemetri som skickas, egenskaper som definierar enhetens tillstånd och de kommandon som enheten svarar på. Enhetens kapacitet är indelat i ett eller flera gränssnitt. Mer information om enhets kapacitets modeller finns i [IoT plug and Play](https://aka.ms/iot-pnp-docs) -dokumentationen.
- **Moln egenskaper** ange egenskaper IoT Central arkiv för en enhet. Dessa egenskaper lagras endast i IoT Central och skickas aldrig till en enhet.
- **Vyer** anger vilka instrument paneler och formulär som Builder skapar för att låta operatören övervaka och hantera enheterna.
- **Anpassningar** gör att verktyget åsidosätter vissa av definitionerna i enhetens kapacitets modell för att göra dem mer relevanta för det IoT Central programmet.

Ett program kan ha en eller flera simulerade och riktiga enheter som baseras på varje enhets mall.

## <a name="data-export"></a>Data export

I ett Azure IoT Central-program kan du [kontinuerligt exportera dina data](howto-export-data-event-hubs-service-bus-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) till dina egna Azure-Event Hubs och Azure Service Bus instanser. Du kan också regelbundet exportera dina data till ditt Azure Blob Storage-konto. IoT Central kan exportera mått, enheter och mallar för enheter.

## <a name="batch-device-updates"></a>Uppdateringar av batch-enhet

I ett Azure IoT Central-program kan du [skapa och köra jobb](howto-run-a-job.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) för att hantera anslutna enheter. Med de här jobben kan du göra Mass uppdateringar av enhets egenskaper eller inställningar eller köra kommandon. Du kan till exempel skapa ett jobb för att öka fläkt hastigheten för flera kylda Vending-datorer.

## <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)

En [administratör kan definiera åtkomst regler](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) för ett Azure IoT Central-program med hjälp av fördefinierade roller. En administratör kan tilldela användare till roller som fastställer vilka delar av programmet som användaren har åtkomst till.

## <a name="security"></a>Säkerhet

Säkerhetsfunktionerna i Azure IoT Central inkluderar:

- Data krypteras under överföring och i vila.
- Autentisering tillhandahålls antingen av Azure Active Directory eller Microsoft-konto. Autentisering med två faktorer stöds.
- Fullständig klient isolering.
- Säkerhet på enhets nivå.

## <a name="ui-shell"></a>UI-gränssnitt

UI Shell är ett modernt, överdrivet HTML5 webbläsarbaserat program.
En administratör kan anpassa programmets användar gränssnitt genom att använda anpassade teman och ändra hjälp länkar så att de pekar på dina egna anpassade hjälp resurser. Mer information om hur du anpassar gränssnittet finns i artikeln [Anpassa Azure IoT Central UI](howto-customize-ui.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

En operatör kan skapa anpassade instrument paneler för program. Du kan ha flera instrument paneler som visar olika data och växlar mellan dem.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om arkitekturen i Azure IoT Central, är det föreslagna nästa steg att lära dig om [enhets anslutning](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) i Azure IoT Central.