---
title: Skapa hälso- och sjukvårdslösningar med Azure IoT Central | Microsoft-dokument
description: Lär dig att skapa hälso- och sjukvårdslösning med hjälp av Azure IoT Central-programmallar.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 2aee5c56b267f9243b69a48dfd0f5f64196c5f23
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77021499"
---
# <a name="building-healthcare-solutions-with-azure-iot-central"></a>Skapa lösningar för hälso- och sjukvården med Azure IoT Central 



Lär dig att skapa hälso- och sjukvårdslösningar med Azure IoT Central med hjälp av programmallar.

## <a name="what-is-continuous-patient-monitoring-template"></a>Vad är kontinuerlig patientövervakningsmall?

Inom hälso- och sjukvårdens IoT-utrymme är kontinuerlig patientövervakning en av de viktigaste drivkrafterna för att minska risken för återtaganden, hantera kroniska sjukdomar mer effektivt och förbättra patientresultaten. Kontinuerlig patientövervakning kan delas in i två huvudkategorier:

1. **Övervakning av patienter**: Med hjälp av medicinska wearables och andra enheter på sjukhuset kan vårdteam övervaka patientens vitala tecken och medicinska tillstånd utan att behöva skicka en sjuksköterska för att kontrollera en patient flera gånger om dagen. Vårdteam kan förstå det ögonblick då en patient behöver kritisk uppmärksamhet genom meddelanden och prioriterar sin tid effektivt.
1. **Fjärrövervakning**av patienter : Genom att använda medicinska wearables och patientrapporterade resultat (PROs) för att övervaka patienter utanför sjukhuset kan risken för återtagande sänkas. Data från patienter med kronisk sjukdom och rehabiliteringspatienter kan samlas in för att säkerställa att patienterna följer vårdplaner och att varningar om patientförsämring kan tas upp på vårdteam innan de blir kritiska.

Denna applikationsmall kan användas för att skapa lösningar för båda kategorierna kontinuerlig patientövervakning. Fördelarna innefattar:

* Sömlöst ansluta olika typer av medicinska wearables till en IoT Central instans.
* Övervaka och hantera enheterna för att säkerställa att de förblir felfria.
* Skapa anpassade regler kring enhetsdata för att utlösa lämpliga aviseringar.
* Exportera dina patienthälsodata till Azure API för FHIR, ett kompatibelt datalager.
* Exportera de aggregerade insikterna till befintliga eller nya affärsprogram.

>[!div class="mx-imgBorder"] 
>![CPM-instrumentpanel](media/in-patient-dashboard.png)

## <a name="next-steps"></a>Nästa steg

Så här kommer du igång med att bygga en kontinuerlig patientövervakningslösning:

* [Distribuera programmallen](tutorial-continuous-patient-monitoring.md)
* [Visa en exempelarkitektur](concept-continuous-patient-monitoring-architecture.md)