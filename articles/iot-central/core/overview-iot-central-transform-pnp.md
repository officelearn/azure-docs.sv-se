---
title: Översikt över transformering
description: Lär dig mer om att transformera
author: viv-liu
ms.author: viviali
ms.date: 10/11/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 45adcaab427d4e28570b573e913369879bc909c6
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992020"
---
# <a name="transform-your-iot-data-preview-features"></a>Transformera IoT-data (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Som en program plattform ger IoT Central flera viktiga aspekter som hjälper dig att transformera dina IoT-data till affärs insikter som enhets åtgärds resultat. IoT Central tillhandahåller metoder för att utöka dina IoT-data till externa system för att skapa integreringar med branschspecifika program och anpassade program. Du kan övervaka enhetens hälso tillstånd och åtgärder genom att skapa regler som meddelar tekniker via en mobil tjänst. Du kan generera specifika affärs insikter med anpassad analys och maskin inlärning genom att exportera rå telemetridata till tjänster i Azure. Du kan skapa tjänster och verktyg som övervakar och kontrollerar enheter och hanterar din IoT Central-app med hjälp av offentliga API: er. 

![Transformering i IoT Central översikt](media/overview-iot-central-transform-pnp/transform.png)

## <a name="monitor-device-health-and-operations-using-rules"></a>Övervaka enhetens hälso tillstånd och åtgärder med hjälp av regler
När dina datorer är anslutna och skickar data kan du identifiera vilka datorer som har problem eller skicka fel meddelanden så att de kan åtgärdas för att köras normalt med minimal stillestånds tid. Du kan bygga regler i din IoT Central-app för att övervaka Telemetrin som dina enheter skickar och varna dig när ett tröskelvärde har överskridits eller ett meddelande om att ett särskilt händelse meddelande har skickats. Du kan konfigurera åtgärder som e-poståtgärder och Webhooks för reglerna för att meddela rätt personer och rätt underordnade system. Lär dig mer om regler här.

## <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>Kör anpassad analys och bearbetning på dina exporterade data
Du kan generera mycket anpassade affärs insikter, till exempel för att fastställa maskin effektivitets trender eller förutsäga framtida energi förbrukning på en fabriks våning genom att skapa anpassade analys pipelines för att bearbeta din obehandlade IoT-telemetri och spara slut resultatet. Du kan skapa data exporter i din IoT Central app för att exportera telemetri, enhets egenskaper och enhets mal len ändrar information till andra Azure-tjänster så att du kan analysera, lagra och visualisera data i de verktyg som är mest värdefulla för dig. Läs mer om data export här.

## <a name="build-custom-iot-solutions-and-integrations-with-apis"></a>Bygg anpassade IoT-lösningar och integreringar med API: er
Du kan skapa IoT-lösningar som Mobile Companion-appar som kan fjärrstyra enheter och konfigurera nya enheter, eller anpassade integreringar med befintliga branschspecifika program för att interagera med dina IoT-enheter och data som är skräddarsydda för din verksamhet. Du kan använda IoT Central som stamnät för enhets modellering, onboarding, hantering och data åtkomst. Läs mer om det offentliga API: et i den här inlärnings modulen.
