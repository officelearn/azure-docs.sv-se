---
title: Arkitektur koncept i Azure IoT Central | Microsoft Docs
description: Den här artikeln beskriver viktiga begrepp om arkitekturen för Azure IoT Central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: c97db3eb2c0fe1a5ec3c743ca75c595ec127823e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central-arkitektur

Den här artikeln innehåller en översikt över arkitekturen i Microsoft Azure IoT Central.

![Översta arkitektur](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Enheter

Enheter utbyta data med Azure IoT centrala programmet. En enhet kan:

- Skicka mätningar som telemetri.
- Synkronisera inställningar med ditt program.

I Azure IoT Central anges de data som en enhet kan utbyta med ditt program i en mall för enheten. Mer information om enheten mallar finns [Metadata management](#metadata-management).

Läs mer om hur enheter ansluta till dina Azure IoT centralt program i [enhetsanslutning](concepts-connectivity.md).

## <a name="cloud-gateway"></a>Gateway för moln

Azure IoT Central använder Azure IoT-hubb som en molngateway som gör att enhetsanslutning. IoT-hubb kan:

- Datapåfyllning i skala i molnet.
- Hantering av enheter.
- Skydda enhetsanslutning.

Läs mer om IoT-hubb i [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

Läs mer om enhetsanslutning i Azure IoT Central i [enhetsanslutning](concepts-connectivity.md).

## <a name="data-stores"></a>Datalager

Azure centrallager IoT som programdata i molnet. Programdata lagras innehåller:

- Mallar för enheten.
- Enheten identiteter.
- Enhetens metadata.
- Användar- och data.

Azure IoT Central använder en serie lagra tid för mätning data som skickas från dina enheter. Tid series-data från enheter som används av analytics-tjänsten.

## <a name="analytics"></a>Analytics

Analytics-tjänsten ansvarar för att skapa anpassade reporting data som visas i programmet. En operatör kan [anpassa analyserna](howto-create-analytics.md) visas i programmet. Analytics-tjänsten är byggt ovanpå [Azure tid serien Insights](https://azure.microsoft.com/services/time-series-insights/) och bearbetar mätdata som skickas från dina enheter.

## <a name="rules-and-actions"></a>Regler och åtgärder

[Regler och åtgärder](howto-create-telemetry-rules.md) samverkar att automatisera uppgifter i programmet. En builder kan definiera regler baserat på enheter telemetri, till exempel temperaturen överskrider ett angivet tröskelvärde. Azure IoT Central använder en stream-processor för att avgöra när villkor är uppfyllda. När en regel är uppfyllt, utlöser en åtgärd som definieras av tillverkaren. En åtgärd kan exempelvis skicka ett e-postmeddelande för att meddela en ingenjör att temperaturen i en enhet är för högt.

## <a name="metadata-management"></a>Hantering av metadata

I ett Azure IoT centrala program definierar enheten mallar beteendet möjligheterna för olika typer av enheter. Exempelvis anger en kylskåpet enheten mall telemetri kylskåp skickar till ditt program.

![Mall-arkitektur](media/concepts-architecture/template_architecture.png)

I en mall för enhet:

- **Mått** ange telemetri enheten skickar till programmet.
- **Inställningar för** anger de konfigurationer som operatör kan ställa in.
- **Egenskaper** ange metadata som anger en operator.
- **Regler** automatisera beteende i program baserat på data som skickas från en enhet.
- **Instrumentpaneler** är anpassningsbara vyer för en enhet i programmet.

Ett program kan ha en eller flera simulerade och verkliga enheter baserat på varje enhet mall.

## <a name="rbac"></a>RBAC

En [administratören kan definiera regler för åtkomst](howto-administer.md) för ett Azure IoT centrala program med hjälp av de fördefinierade rollerna. En administratör kan tilldela användare till roller som bestämmer vilka områden i programmet användaren har åtkomst till.

## <a name="security"></a>Säkerhet

Säkerhetsfunktionerna i Azure IoT centrala omfattar:

- Data krypteras under överföring och i vila.
- Autentisering tillhandahålls genom Azure Active Directory eller Account. Tvåfaktorsautentisering stöds.
- Fullständig klientisolering.
- Säkerhet på enheten.

## <a name="ui-shell"></a>UI-gränssnittet

UI-gränssnittet är en modern och dynamisk, HTML5 webbläsarbaserat program.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om arkitekturen för Azure IoT Central, föreslagna nästa steg är att lära dig om [enhetsanslutning](concepts-connectivity.md) i Azure IoT Central.