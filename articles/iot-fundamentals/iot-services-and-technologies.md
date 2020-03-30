---
title: Azure Sakernas Internet (IoT)-tekniker och -lösningar
description: Beskriver insamlingen av tekniker och tjänster som du kan använda för att skapa en Azure IoT-lösning.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.openlocfilehash: 0b04b5170c13f6f6c3fd74976461f03e4367060a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77046066"
---
# <a name="azure-technologies-and-services-for-creating-iot-solutions"></a>Azure-tekniker och -tjänster för att skapa IoT-lösningar

Azure IoT-tekniker och -tjänster ger dig alternativ för att skapa en mängd olika IoT-lösningar som möjliggör digital omvandling för din organisation. Du kan till exempel:

- Använd [Azure IoT Central](https://apps.azureiotcentral.com), en hanterad IoT-programplattform, för att skapa och distribuera en säker IoT-lösning i företagsklass. IoT Central har en samling branschspecifika programmallar, till exempel detaljhandel och hälso- och sjukvård, för att påskynda din lösningsutvecklingsprocess.
- Utöka den öppna källkodsbasen för en Azure [IoT-lösningsaccelerator](https://www.azureiotsolutions.com) för att implementera ett gemensamt IoT-scenario, till exempel fjärrövervakning eller förutsägande underhåll.
- Använd Azure IoT-plattformstjänster som [Azure IoT Hub](../iot-hub/about-iot-hub.md) och [Azure IoT-enheten SDK:er](../iot-hub/iot-hub-devguide-sdks.md) för att skapa en anpassad IoT-lösning från grunden.

![Azure IoT-teknik, tjänster och lösningar](./media/iot-services-and-technologies/iot-technologies-services.png)

## <a name="azure-iot-central"></a>Azure IoT Central

[IoT Central-applikationsplattformen](https://apps.azureiotcentral.com) minskar bördan och kostnaderna för att utveckla, hantera och underhålla IoT-lösningar i företagsklass. IoT Centrals anpassningsbara webbgränssnitt i gör att du kan övervaka enhetsvillkor, skapa regler och hantera miljontals enheter och deras data under hela livscykeln. API-ytan inom IoT Central ger dig programmatisk åtkomst för att konfigurera och interagera med din IoT-lösning.

Azure IoT Central är en fullständigt hanterad programplattform som du kan använda för att skapa anpassade IoT-lösningar. IoT Central använder programmallar för att skapa lösningar. Det finns mallar för generiska lösningar och för specifika branscher som energi, hälso- och sjukvård, myndigheter och detaljhandel. Med IoT Central-programmallar kan du distribuera ett IoT Central-program på några minuter som du sedan kan anpassa med teman, instrumentpaneler och vyer.

Välj enheter från [Azure Certified for IoT-enhetskatalogen](https://catalog.azureiotsolutions.com) för att snabbt ansluta till din lösning. Använd webbgränssnittet i IoT Central för att övervaka och hantera dina enheter för att hålla dem felfria och anslutna. Använd kopplingar och API:er för att integrera ditt IoT Central-program med andra affärsprogram.

Som en fullständigt hanterad applikationsplattform har IoT Central en enkel och förutsägbar prismodell.

## <a name="azure-iot-solution-accelerators"></a>Azure IoT-lösningsacceleratorer

[Azure IoT-lösningsacceleratorer](https://www.azureiotsolutions.com) är en samling anpassningsbara lösningar i företagsklass. Du kan distribuera dessa lösningar som de är eller utveckla en anpassad IoT-lösning med hjälp av Java- eller .NET-källkoden med öppen källkod.

Azure IoT-lösningsacceleratorer ger en hög nivå av kontroll över din IoT-lösning. Lösningsacceleratorerna innehåller färdiga lösningar för vanliga IoT-scenarier som du kan distribuera till din Azure-prenumeration på några minuter. Scenarierna omfattar:

  - Fjärrövervakning
  - Ansluten fabrik
  - Förebyggande underhåll
  - Enhetssimulering

Bas för öppen källkod för alla lösningsacceleratorer finns på GitHub. Ladda ner koden för att anpassa en lösningsaccelerator för att uppfylla dina specifika IoT-krav.

Lösningsacceleratorerna använder Azure-tjänster som Azure IoT Hub och Azure Storage som du måste hantera i din Azure-prenumeration.

## <a name="custom-solutions"></a>Anpassade lösningar

Om du vill skapa en IoT-lösning från grunden eller utöka en lösning som skapats med IoT Central eller en lösningsaccelerator använder du en eller flera av följande Azure IoT-tekniker och -tjänster:

### <a name="devices"></a>Enheter

Utveckla dina IoT-enheter med hjälp av ett av [Azure IoT Starter Kits](https://catalog.azureiotsolutions.com/kits) eller välj en enhet att använda från [Azure Certified for IoT-enhetskatalogen](https://catalog.azureiotsolutions.com). Implementera den inbäddade koden med [SDK:erna med](../iot-hub/iot-hub-devguide-sdks.md)öppen källkod . Enhetens SDK-filer stöder flera operativsystem, till exempel Linux, Windows och operativsystem i realtid. Det finns SDK:er för flera programmeringsspråk, till exempel [C,](https://github.com/Azure/azure-iot-sdk-c) [Node.js](https://github.com/Azure/azure-iot-sdk-node), [Java](https://github.com/Azure/azure-iot-sdk-java), [.NET](https://github.com/Azure/azure-iot-sdk-csharp)och [Python](https://github.com/Azure/azure-iot-sdk-python).

Du kan förenkla ytterligare hur du skapar den inbäddade koden för dina enheter med hjälp av tjänsten [IoT Plug and Play Preview.](../iot-pnp/overview-iot-plug-and-play.md) IoT Plug and Play gör det möjligt för lösningsutvecklare att integrera enheter med sina lösningar utan att skriva någon inbäddad kod. Kärnan i IoT Plug and Play är ett modellschema för _enhetskapacitet_ som beskriver enhetsfunktioner. Använd enhetskapacitetsmodellen för att generera din inbäddade enhetskod och konfigurera en molnbaserad lösning, till exempel ett IoT Central-program.

[Med Azure IoT Edge](../iot-edge/about-iot-edge.md) kan du avlasta delar av din IoT-arbetsbelastning från dina Azure-molntjänster till dina enheter. IoT Edge kan minska svarstiden i din lösning, minska mängden data som dina enheter utbyter med molnet och aktivera offlinescenarier. Du kan hantera IoT Edge-enheter från IoT Central och vissa lösningsacceleratorer.

[Azure Sphere](https://docs.microsoft.com/azure-sphere/product-overview/what-is-azure-sphere) är en säker programplattform på hög nivå med inbyggda kommunikations- och säkerhetsfunktioner för internetanslutna enheter. Den innehåller en säker mikrokontrollerenhet, ett anpassat Linux-baserat operativsystem och en molnbaserad säkerhetstjänst som ger kontinuerlig, förnybar säkerhet.

### <a name="cloud-connectivity"></a>Molnanslutning

[Azure IoT Hub-tjänsten](../iot-hub/about-iot-hub.md) möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan miljontals IoT-enheter och en molnbaserad lösning. [Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md) är en hjälptjänst för IoT Hub. Tjänsten ger zero-touch, just-in-time-etablering av enheter till rätt IoT-hubb utan att kräva mänsklig inblandning. Dessa funktioner gör det möjligt för kunder att tillhandahålla miljontals enheter på ett säkert och skalbart sätt.

IoT Hub är en kärnkomponent i lösningsacceleratorerna och du kan använda den för att möta IoT-implementeringsutmaningar som:

* Omfattande enhetsanslutning och hantering.
* Omfattande inmatning av telemetri.
* Kontroll av enheter.
* Tillämpning av enhetssäkerhet.

### <a name="bridging-the-gap-between-the-physical-and-digital-worlds"></a>Överbrygga klyftan mellan de fysiska och digitala världarna

[Azure Digital Twins](../digital-twins/about-digital-twins.md) är en IoT-tjänst som gör att du kan modellera en fysisk miljö. Den använder en rumslig intelligens diagram för att modellera relationer mellan människor, utrymmen och enheter. Genom att kärna data över de digitala och fysiska världarna kan du skapa kontextuellt medvetna lösningar.

Iot Central använder digitala tvillingar för att synkronisera enheter och data i den verkliga världen med de digitala modeller som gör det möjligt för användare att övervaka och hantera dessa anslutna enheter.

### <a name="data-and-analytics"></a>Data och analys

IoT-enheter genererar vanligtvis stora mängder tidsseriedata, till exempel temperaturavläsningar från sensorer. [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) kan ansluta till en IoT-hubb, läsa telemetriströmmen från dina enheter, lagra dessa data och göra att du kan fråga och visualisera den.

[Azure Maps](/azure/azure-maps) är en samling geospatiala tjänster som använder nya mappningsdata för att ge korrekt geografisk kontext till webb- och mobilappar. Du kan använda ett REST API, en webbaserad JavaScript-kontroll eller en Android SDK för att skapa dina program.

## <a name="next-steps"></a>Nästa steg

För en praktisk upplevelse kan du prova en av snabbstarterna:

- [Skapa ett Azure IoT Central-program](../iot-central/core/quick-deploy-iot-central.md)
- [Skicka telemetri från en enhet till en IoT-hubb](../iot-hub/quickstart-send-telemetry-cli.md)
- [Testa en molnbaserad fjärrövervakningslösning](../iot-accelerators/quickstart-remote-monitoring-deploy.md)
