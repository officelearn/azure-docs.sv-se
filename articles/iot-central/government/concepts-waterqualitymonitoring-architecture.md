---
title: Referensarkitektur för vattenkvalitetsövervakningslösning byggd med Azure IoT Central| Microsoft-dokument
description: Lär dig begrepp för en lösning för övervakning av vattenkvalitet som skapats med Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 0c4b13c56a68205195bd5ad4b696d9e01786a8dd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77016552"
---
# <a name="water-quality-monitoring-reference-architecture"></a>Referensarkitektur för övervakning av vattenkvalitet 



Övervakningslösningar för vattenkvalitet kan byggas med **Azure IoT Central-appmallen** som ett kick starter IoT-program. Den här artikeln innehåller en vägledning om referensarkitektur på hög nivå om hur du skapar en lösning från på på. 


![Arkitektur för övervakning av vattenkvalitet](./media/concepts-waterqualitymonitoring-architecture/concepts-waterqualitymonitoring-architecture1.png)

Koncept:

1. Enheter och anslutning  
1. IoT Central 
2. Utökningsförmåga och integrationer
3. Affärsprogram

Låt oss ta en titt på viktiga komponenter som i allmänhet spelar en roll i en lösning för övervakning av vattenkvaliteten.

## <a name="devices-and-connectivity"></a>Enheter och anslutning 
I det här avsnittet kommer vi att hänvisa till enheter som används vid övervakning av vattenkvalitet eller övervakning av vattenförbrukning, i allmänhet som smarta vattenenheter. Smarta vattenenheter kan vara flödesmätare, vattenkvalitetsmonitorer, smarta ventiler, läckdetektorer och så vidare.

Enheter som används i smarta vattenlösningar kommer i allmänhet att anslutas via lågeffektsnät (LPWAN), via en tredjepartsnätoperatör. För dessa typer av enheter kan du utnyttja [Azure IoT Central Device Bridge](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge) för att skicka enhetsdata till ditt IoT-program i Azure IoT Central. Alternativt kan du ha enhetsgateways som är IP-kompatibla och kan ansluta direkt till IoT Central.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central är en IoT App-plattform som snabbt hjälper dig att komma igång på din IoT-lösning. Du kan skapa varumärken, anpassa och integrera din lösning med tjänster från tredje part.
När du har anslutit dina smarta vattenenheter till IoT Central får du enhetskommando och kontroll, övervakning och avisering, användargränssnitt med inbyggd RBAC, konfigurerbara insights-instrumentpaneler och alternativ för utökningsbarhet. 

## <a name="extensibility-and-integrations"></a>Utökningsförmåga och integrationer 
Du kan utöka din IoT-applikation i IoT Central och eventuellt:
* omvandla och integrera dina IoT-data för avancerad analys, till exempel utbildning av maskininlärningsmodeller, genom kontinuerlig dataexport från IoT Central-programmet
* automatisera arbetsflöden i andra system genom att utlösa åtgärder med Microsoft Flow eller webhooks från IoT Central-programmet
* programatiskt komma åt din IoT-applikation i IoT Central via IoT Central API:er

## <a name="business-applications"></a>Affärsprogram 
IoT-data kan användas för att driva en mängd olika affärsapplikationer inom ett vattenverktyg. Om du vill veta hur du ansluter ditt IoT Central vattenkvalitetsövervakningsprogram med fälttjänster följer du artikeln om [hur du integrerar med Dynamics 365 Field Services](./how-to-configure-connected-field-services.md). 


## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [skapar en vattenkvalitet övervakning](./tutorial-water-quality-monitoring.md) IoT Central ansökan
* Läs mer om [IoT Centrala myndigheters mallar](./overview-iot-central-government.md)
* Mer information om IoT Central finns i [IoT Central översikt](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)

