---
title: Kontinuerlig övervaknings arkitektur för patient i Azure IoT Central | Microsoft Docs
description: Lär dig mer om en kontinuerlig arkitektur för övervakning av patient lösningar.
author: philmea
ms.author: philmea
ms.date: 7/23/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 0032f341330ad394241806a4fe61add530253f09
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87116861"
---
# <a name="continuous-patient-monitoring-architecture"></a>Arkitektur för kontinuerlig övervakning av patienter



Kontinuerliga övervaknings lösningar för patienter kan skapas med hjälp av den app-mall som anges och med hjälp av den arkitektur som beskrivs nedan som vägledning.

>[!div class="mx-imgBorder"] 
>![CPM-arkitektur](media/cpm-architecture.png)

1. Medicinska enheter som kommunicerar med Bluetooth låg energi (Bell)
1. Mobil telefon Gateway tar emot tabell data och skickar till IoT Central
1. Kontinuerlig data export av patient hälso data till Azure API för FHIR&reg;
1. Maskin inlärning baserat på driftskompatibla data
1. Vård Teams instrument panel som bygger på FHIR-data

## <a name="details"></a>Information
I det här avsnittet beskrivs varje del av arkitektur diagrammet mer detaljerat.

### <a name="ble-medical-devices"></a>Bell-medicintekniska enheter
Många medicinska wearables som används i hälsovårds området för sjukvård är Bluetooth-enheter med låg energi. De kan inte prata direkt till molnet och behöver släppa igenom en gateway. Den här arkitekturen föreslår användning av ett mobil telefon program som denna gateway. 

### <a name="mobile-phone-gateway"></a>Mobil telefon Gateway
Mobil telefon programmets primära funktion är att mata in tabell data från medicinska enheter och kommunicera med Azure IoT Central. Dessutom kan du med hjälp av appen hjälpa dig att få hjälp med en enhets konfiguration och ett etablerings flöde och hjälpa dem se en vy över sina personliga hälso data. Andra lösningar kan använda en surfplatta gateway eller en statisk Gateway om det är i ett sjukhus rum för att uppnå samma kommunikations flöde. Vi har skapat ett mobilt exempel mobil program med öppen källkod som är tillgängligt för Android och iOS som du kan använda som utgångs punkt för att komma igång med dina program utvecklings åtgärder. Mer information om exemplet IoT Central kontinuerlig övervakning av mobilappar finns i [Azure-exempel](https://docs.microsoft.com/samples/iot-for-all/iotc-cpm-sample/iotc-cpm-sample/).

### <a name="export-to-azure-api-for-fhirreg"></a>Exportera till Azure API för FHIR&reg;
Azure IoT Central är HIPAA-kompatibelt och HITRUST- &reg; certifierat, men du kanske också vill skicka information om patient hälsa till Azure-API: et för FHIR. [Azure API för FHIR](../../healthcare-apis/overview.md) är ett fullständigt hanterat, standardbaserat, kompatibelt API för kliniska hälso data som gör det möjligt att skapa nya system för engagemang med dina hälso data. Det möjliggör snabbt utbyte av data via FHIR-API: er som backas upp av ett hanterat PaaS-erbjudande (Platform-as-a-Service) i molnet. Med hjälp av funktionerna för kontinuerlig data export i IoT Central kan du skicka data till Azure API för FHIR via [Azure IoT-anslutningen för FHIR](https://docs.microsoft.com/azure/healthcare-apis/iot-fhir-portal-quickstart).

### <a name="machine-learning"></a>Maskininlärning
När du har samlat in dina data och översätter dem till FHIR-format kan du skapa maskin inlärnings modeller som kan utveckla insikter och aktivera smartare besluts fattande för ditt vård team. Det finns olika typer av tjänster som kan användas för att skapa, träna och distribuera maskin inlärnings modeller. Mer information om hur du använder Azures Machine Learning-erbjudanden finns i vår [Machine Learning-dokumentation](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Instrument panel för Provider
De data som finns i Azure-API: et för FHIR kan användas för att bygga en patient instrument panel eller kan integreras direkt i en EMR för att hjälpa team att visualisera patient status. Vård team kan använda den här instrument panelen för att ta hand om patienter i behov av hjälp och för att upptäcka varnings tecken. Om du vill veta hur du skapar en Power BI instrument panel för real tids leverantörer följer [du vår instruktions guide](howto-health-data-triage.md).

## <a name="next-steps"></a>Nästa steg
* [Lär dig hur du distribuerar en kontinuerlig övervaknings program mal len](tutorial-continuous-patient-monitoring.md)