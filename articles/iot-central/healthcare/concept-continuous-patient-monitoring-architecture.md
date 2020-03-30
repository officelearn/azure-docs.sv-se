---
title: Kontinuerlig patientövervakningsarkitektur i Azure IoT Central | Microsoft-dokument
description: Lär dig mer om en kontinuerlig patientövervakningslösningsarkitektur.
author: philmea
ms.author: philmea
ms.date: 10/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 92eb4157abb55b7056952d1fb064c7c7d7500335
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77021704"
---
# <a name="continuous-patient-monitoring-architecture"></a>Arkitektur för kontinuerlig övervakning av patienter



Kontinuerlig patientövervakningslösningar kan byggas med hjälp av appmallen som tillhandahålls och med hjälp av arkitekturen som beskrivs nedan som vägledning.

>[!div class="mx-imgBorder"] 
>![CPM-arkitektur](media/cpm-architecture.png)

1. Medicintekniska produkter som kommunicerar med Bluetooth Low Energy (BLE)
1. Mobiltelefon gateway ta emot BLE-data och skicka till IoT Central
1. Kontinuerlig dataexport av patienthälsodata till Azure API för FHIR&reg;
1. Maskininlärning baserad på driftskompatibla data
1. Instrumentpanel för vårdteam som bygger på FHIR-data

## <a name="details"></a>Information
I det här avsnittet beskrivs varje del av arkitekturdiagrammet mer i detalj.

### <a name="ble-medical-devices"></a>BLE medicintekniska produkter
Många medicinska wearables som används i hälso-IoT utrymme är Bluetooth Low Energy enheter. De kan inte tala direkt till molnet och måste passera genom en gateway. Den här arkitekturen föreslår att du använder ett mobiltelefonprogram som den här gatewayen.

### <a name="mobile-phone-gateway"></a>Gateway för mobiltelefon
Mobiltelefonprogrammets primära funktion är att inta BLE-data från medicintekniska produkter och kommunicera dem till Azure IoT Central. Dessutom kan appen hjälpa till att vägleda patienter genom en enhetsinstallation och etableringsflöde och hjälpa dem att se en bild av deras personliga hälsodata. Andra lösningar kan använda en tablet gateway eller en statisk gateway om inne i ett sjukhusrum för att uppnå samma kommunikationsflöde.

### <a name="export-to-azure-api-for-fhirreg"></a>Exportera till Azure API för FHIR&reg;
Azure IoT Central är HIPAA-kompatibel och HITRUST-certifierad,&reg; men du kanske också vill skicka patienthälsorelaterade data till Azure API för FHIR. [Azure API för FHIR](../../healthcare-apis/overview.md) är ett fullständigt hanterat, standardbaserat, kompatibelt API för kliniska hälsodata som gör att du kan skapa nya system för interaktion med dina hälsodata. Det möjliggör snabbt utbyte av data via FHIR API:er, med stöd av ett hanterat PaaS-erbjudande (Platform-as-a Service) i molnet. Med hjälp av funktionen Kontinuerlig dataexport i IoT Central kan du skicka data till Azure API för FHIR.

### <a name="machine-learning"></a>Maskininlärning
När du har aggregerat dina data och översatt dem till FHIR-format kan du skapa maskininlärningsmodeller som kan berika insikter och möjliggöra smartare beslutsfattande för ditt vårdteam. Det finns olika typer av tjänster som kan användas för att skapa, träna och distribuera maskininlärningsmodeller. Mer information om hur du använder Azures maskininlärningserbjudanden finns i vår [maskininlärningsdokumentation](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Instrumentpanel för provider
Data som finns i Azure API för FHIR kan användas för att skapa en instrumentpanel för patientinsikter eller kan integreras direkt i en EMR för att hjälpa vårdteam att visualisera patientstatus. Vårdteam kan använda den här instrumentpanelen för att ta hand om patienter i behov av hjälp och upptäcka tidiga varningssignaler om försämring. Om du vill veta hur du skapar en Power BI-instrumentpanel i realtid följer du vår [guide](howto-health-data-triage.md).

## <a name="next-steps"></a>Nästa steg
* [Lär dig hur du distribuerar en kontinuerlig patientövervakningsprogrammall](tutorial-continuous-patient-monitoring.md)