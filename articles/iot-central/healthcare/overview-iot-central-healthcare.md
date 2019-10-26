---
title: Bygg sjukvårds lösningar med Azure IoT Central | Microsoft Docs
description: Lär dig att bygga sjukvårds lösningar med hjälp av Azure IoT Central programmallar.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 7051a4257bc45f0c9266be14678a1409be66fe9e
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72958030"
---
# <a name="building-healthcare-solutions-with-azure-iot-central"></a>Skapa sjukvårds lösningar med Azure IoT Central 

Lär dig att bygga sjukvårds lösningar med Azure IoT Central att använda programmallar.

## <a name="what-is-continuous-patient-monitoring-template"></a>Vad är en mall för kontinuerliga patient övervakning?

I IoT-området för sjukvård är kontinuerlig övervakning av patienter en av de viktigaste sätten att minska risken för återtagande, hantera kroniska sjukdomar och förbättra patient resultat. Kontinuerlig övervakning av patienter kan delas upp i två huvud kategorier:

1. **Pågående övervakning**: användning av medicinska wearables och andra enheter i sjukhus kan du övervaka patients viktiga tecken och medicinska villkor utan att behöva skicka en till gång för att checka in en patient flera gånger per dag. Vård team kan förstå hur lång tid en patient behöver tänka igenom meddelanden och prioriterar deras tid effektivt.
1. **Fjärrövervakning av patienter**: genom att använda medicinska wearables och patienter som rapporterade resultat för att övervaka patienter utanför sjukhus kan risken för åter användning vara betydligt lägre. Data från patienter för kroniska sjukdomar och upprustning-patienter kan samlas in för att säkerställa att patienter följer avtals planer och att aviseringar om patient försämring kan ställas på att kunna användas i teamet innan de blir kritiska.

Den här program mal len kan användas för att bygga lösningar för båda kategorierna av kontinuerlig patient övervakning. Fördelarna är:

* Anslut en rad medicinska wearables till en IoT Central-instans sömlöst.
* Övervaka och hantera enheterna för att säkerställa att de är felfria.
* Skapa anpassade regler runt enhets data för att utlösa lämpliga aviseringar.
* Exportera dina patient hälso data till Azure API för FHIR, ett kompatibelt data lager.
* Exportera sammanställda insikter till befintliga eller nya affärs program som ger vård lag.

>[!div class="mx-imgBorder"] 
>![CPM-instrument panel](media/in-patient-dashboard.png)

## <a name="next-steps"></a>Nästa steg

För att komma igång med att skapa en kontinuerlig övervaknings lösning för patienter:

* [Distribuera program mal len](tutorial-continuous-patient-monitoring.md)
* [Se en exempel arkitektur](concept-continuous-patient-monitoring-architecture.md)