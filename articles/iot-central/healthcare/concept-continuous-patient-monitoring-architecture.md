---
title: Kontinuerlig övervaknings arkitektur för patient i Azure IoT Central | Microsoft Docs
description: Lär dig mer om en kontinuerlig arkitektur för övervakning av patient lösningar.
author: philmea
ms.author: philmea
ms.date: 10/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 2a03d47dc2b50cd9410cb3efa94012bd18680953
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957540"
---
# <a name="continuous-patient-monitoring-architecture"></a>Kontinuerlig övervaknings arkitektur för patient
Kontinuerliga övervaknings lösningar för patienter kan skapas genom att dra nytta av appens mall och använda den arkitektur som beskrivs nedan som vägledning.

>[!div class="mx-imgBorder"] 
>![CPM-arkitektur](media/cpm-architecture.png)

1. Medicinska enheter som kommunicerar via Bluetooth låg energi (Bell)
1. Mobil telefon Gateway tar emot tabell data och skickar till IoT Central
1. Kontinuerlig data export av patient hälso data till Azure API för FHIR&reg;
1. Maskin inlärning baserat på driftskompatibla data
1. Vård Teams instrument panel som bygger på FHIR-data

## <a name="details"></a>Information
I det här avsnittet beskrivs varje del av arkitektur diagrammet mer detaljerat.

### <a name="ble-medical-devices"></a>Bell-medicintekniska enheter
Många medicinska wearables som används i hälsovårds området för sjukvård är Bluetooth-enheter med låg energi, vilket innebär att de inte kan prata direkt till molnet och behöver släppas genom en gateway. Den här arkitekturen föreslår användning av ett mobil telefon program som denna gateway.

### <a name="mobile-phone-gateway"></a>Mobil telefon Gateway
Mobil telefon programmets primära funktion är att mata in tabell data från medicinska enheter och kommunicera med Azure IoT Central. Dessutom kan appen hjälpa dig att hjälpa dig att få hjälp med att hjälpa dig med en enhets konfiguration och ett etablerings flöde och hjälpa dem att se en vy över sina personliga hälso data. Andra lösningar kan använda en surfplatta gateway eller en statisk Gateway om det är i ett sjukhus rum för att uppnå samma kommunikations flöde.

### <a name="export-to-azure-api-for-fhirreg"></a>Exportera till Azure API för FHIR&reg;
Azure IoT Central är HIPAA-kompatibelt och HITRUST&reg; certifierat, men du kanske också vill skicka information om patient hälsa till Azure-API: et för FHIR. [Azure API för FHIR](../../healthcare-apis/overview.md) är ett fullständigt hanterat, standardbaserat, kompatibelt API för kliniska hälso data som gör det möjligt att skapa nya system för engagemang med dina hälso data. Det möjliggör snabbt utbyte av data via FHIR-API: er som backas upp av ett hanterat PaaS-erbjudande (Platform-as-a-Service) i molnet. Med hjälp av funktionerna för kontinuerlig data export i IoT Central kan du skicka data till Azure API för FHIR.

### <a name="machine-learning"></a>Machine Learning
När du har samlat in dina data och översätter dem till FHIR-format kan du skapa maskin inlärnings modeller som kan utveckla insikter och aktivera smartare besluts fattande för ditt vård team. Det finns en mängd olika tjänster som kan användas för att skapa, träna och distribuera maskin inlärnings modeller. Mer information om hur du använder Azures Machine Learning-erbjudanden finns i vår [Machine Learning-dokumentation](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Instrument panel för Provider
De data som finns i Azure-API: et för FHIR kan användas för att bygga en patient instrument panel eller kan integreras direkt i en EMR för att hjälpa team att visualisera patient status. Vård team kan använda den här instrument panelen för att vidta åtgärder för patienter som behöver hjälp och för att upptäcka varnings tecken. Om du vill veta hur du skapar en Power BI instrument panel för real tids leverantörer följer [du vår instruktions guide](howto-health-data-triage.md).

## <a name="next-steps"></a>Nästa steg
* [Lär dig hur du distribuerar en kontinuerlig övervaknings program mal len](tutorial-continuous-patient-monitoring.md)