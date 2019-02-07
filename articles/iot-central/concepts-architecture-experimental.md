---
title: Arkitektoniska begrepp i Azure IoT Central | Microsoft Docs
description: Den här artikeln beskriver viktiga begrepp gällande arkitekturen i Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 89da922b7a17f8a3b6a68a335cfeeffa5a8b236c
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824341"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central-arkitektur

Den här artikeln innehåller en översikt över Microsoft Azure IoT Central-arkitektur.

![Översta arkitektur](media/concepts-architecture-experimental/architecture.png)

## <a name="devices"></a>Enheter

Enheter utbyta data med Azure IoT Central programmet. En enhet kan:

- Skicka mätning av faktisk användning, till exempel telemetri.
- Synkronisera inställningar med ditt program.

De data som en enhet kan utbyta med ditt program har angetts i en mall för enheten i Azure IoT Central. Mer information om enheten mallar finns i [Metadata management](#metadata-management).

Läs mer om hur enheterna ska ansluta till Azure IoT Central programmet i [enhetsanslutning](concepts-connectivity-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="cloud-gateway"></a>Molngatewayen

Azure IoT Central använder Azure IoT Hub som en molngateway som möjliggör anslutningar på enheten. IoT Hub kan:

- Datainmatning i stor skala i molnet.
- Hantering av enheter.
- Skydda enhetsanslutning.

Läs mer om IoT Hub i [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

Mer information om enhetsanslutning i Azure IoT Central finns [enhetsanslutning](concepts-connectivity-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="data-stores"></a>Datalager

Azure IoT Central lagrar programdata i molnet. Programdata lagras innehåller:

- Mallar för enheten.
- Enhetsidentiteter.
- Enhetens metadata.
- Data för användaren och rollen.

Azure IoT Central använder en time series lagra för mätdata som skickas från dina enheter. Time series-data från enheter som används av analytics-tjänsten.

## <a name="analytics"></a>Analytics

Analytics-tjänsten ansvarar för att skapa anpassade reporting data som visas i programmet. En operatör kan [anpassa analyser](howto-create-analytics-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) visas i programmet. Analytics-tjänsten är byggt ovanpå [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) och bearbetar mätdata som skickas från dina enheter.

## <a name="rules-and-actions"></a>Regler och åtgärder

[Regler och åtgärder](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) samverkar att automatisera uppgifter i programmet. Hjälpverktyg kan definiera regler baserat på enhetstelemetri, till exempel temperaturen som överskrider ett angivet tröskelvärde. Azure IoT Central använder en stream-processor för att avgöra när villkor är uppfyllda. När en regelvillkor är uppfyllt, utlöser en åtgärd som definieras av tillverkaren. En åtgärd kan exempelvis skicka ett e-postmeddelande för att meddela en tekniker att temperaturen i en enhet är för högt.

## <a name="metadata-management"></a>Hantering av metadata

I ett Azure IoT Central program definierar enheten mallar beteende och möjligheterna för typer av enheter. Exempelvis anger en kylskåp enheten mall telemetri kylskåp skickar till ditt program.

![Mall-arkitektur](media/concepts-architecture-experimental/template_architecture.png)

I en mall för enheten:

- **Mätning av faktisk användning** ange telemetri som enheten skickar till programmet.
- **Inställningar för** anger de konfigurationer som en operatör kan ställa in.
- **Egenskaper för** ange metadata som en operatör kan ställa in.
- **Regler** automatisera beteende i appen baserat på data som skickas från en enhet.
- **Instrumentpaneler** är anpassningsbara vyer för en enhet i programmet.

Ett program kan ha en eller flera simulerade och verkliga enheter baserat på varje enhet-mall.

## <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)

En [administratören kan definiera åtkomstregler](howto-administer-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) för ett Azure IoT Central-program med hjälp av de fördefinierade rollerna. En administratör kan tilldela användare till roller som bestämmer vilka delar av programmet användaren har åtkomst till.

## <a name="security"></a>Säkerhet

Säkerhetsfunktionerna i Azure IoT Central omfattar:

- Data krypteras vid överföring och i vila.
- Autentisering tillhandahålls genom Azure Active Directory eller Account. Tvåfaktorsautentisering stöds.
- Fullständig klientisolering.
- Enheten på radnivå.

## <a name="ui-shell"></a>UI-gränssnittet

UI-gränssnittet är en modern, dynamiskt, HTML5 webbläsarbaserade program.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om arkitekturen i Azure IoT Central, föreslagna nästa steg är att lära dig om [enhetsanslutning](concepts-connectivity-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) i Azure IoT Central.