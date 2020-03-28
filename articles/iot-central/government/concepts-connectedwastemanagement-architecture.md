---
title: Referensarkitektur för lösning för ansluten avfallshantering byggd med Azure IoT Central| Microsoft-dokument
description: Lär dig begrepp för en ansluten avfallshanteringslösning som skapats med Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 763fb82e0725e5a98112af3a79856c3703a5c2c3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77016093"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>Referensarkitektur för ansluten avfallsövervakning 



En ansluten avfallshanteringslösning kan byggas med **azure IoT Central-appmallen** som ett kick starter IoT-program. Den här artikeln innehåller en vägledning om referensarkitektur på hög nivå om hur du skapar en lösning från på på. 

![Arkitektur för uppkopplad avfallshantering](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)


Koncept:

1. Enheter och anslutning  
1. IoT Central 
2. Utökningsförmåga och integrationer
3. Affärsprogram

Låt oss ta en titt på viktiga komponenter som i allmänhet spelar en roll i en vattenförbrukning övervakningslösning.

## <a name="devices-and-connectivity"></a>Enheter och anslutning 
Enheter som används i öppna miljöer, som soptunnor, kan anslutas via lågeffektsnät (LPWAN), via en tredjepartsnätverksoperatör. För dessa typer av enheter kan du använda [Azure IoT Central Device Bridge](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge) för att skicka enhetsdata till ditt IoT-program i Azure IoT Central. Alternativt kan du ha enhetsgateways som är IP-kompatibla och kan ansluta direkt till IoT Central.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central är en IoT App-plattform som snabbt hjälper dig att komma igång på din IoT-lösning. Du kan skapa varumärken, anpassa och integrera din lösning med tjänster från tredje part.
När du har anslutit dina smarta vattenenheter till IoT Central får du enhetskommando och kontroll, övervakning och avisering, användargränssnitt med inbyggd RBAC, konfigurerbara insights-instrumentpaneler och alternativ för utökningsbarhet. 

## <a name="extensibility-and-integrations"></a>Utökningsförmåga och integrationer 
Du kan utöka din IoT-applikation i IoT Central och eventuellt:
* omvandla och integrera dina IoT-data för avancerad analys, till exempel utbildning av maskininlärningsmodeller, genom kontinuerlig dataexport från IoT Central-programmet. 
* automatisera arbetsflöden i andra system genom att utlösa åtgärder med Microsoft Flow eller webhooks från IoT Central-programmet
* programatiskt komma åt din IoT-applikation i IoT Central via IoT Central API: er.

## <a name="business-applications"></a>Affärsprogram 
IoT-data kan användas för att driva en mängd olika affärsapplikationer inom ett avfallsverktyg. Om du vill veta hur du ansluter ditt IoT Central-anslutna avfallshanteringsprogram med fälttjänster följer du självstudien om [hur du integrerar med Dynamics 365 Field Services](./how-to-configure-connected-field-services.md) 

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [skapar en ansluten avfallshantering](./tutorial-connected-waste-management.md) IoT Central ansökan
* Läs mer om [IoT Centrala myndigheters mallar](./overview-iot-central-government.md)
* Mer information om IoT Central finns i [IoT Central översikt](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)

