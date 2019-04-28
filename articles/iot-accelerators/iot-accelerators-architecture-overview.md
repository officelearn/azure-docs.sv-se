---
title: Referensarkitektur för IoT-Lösningsacceleratorer – Azure | Microsoft Docs
description: Läs mer om Azure IoT-Lösningsacceleratorer referens intranät. Befintliga Lösningsacceleratorer utnyttja den här referensarkitekturen. Du kan också använda referensarkitekturen när du skapar dina egna anpassade IoT-lösningar.
author: dominicbetts
ms.author: dobett
ms.date: 12/04/2018
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: philmea
ms.openlocfilehash: ba5eb50dcf800c186124db348ac584ff6f55cebb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450316"
---
# <a name="introduction-to-the-azure-iot-reference-architecture"></a>Introduktion till Azure IoT-Referensarkitektur

Den här artikeln introducerar de [Azure IoT-Referensarkitektur](https://aka.ms/iotrefarchitecture) och ger exempel på hur [Azure IoT-Lösningsacceleratorer](about-iot-accelerators.md) följa sina rekommendationer.

Öppen källkod [fjärrövervakning](iot-accelerators-remote-monitoring-sample-walkthrough.md) och [ansluten fabrik](iot-accelerators-connected-factory-sample-walkthrough.md) Lösningsacceleratorer följer många av rekommendationerna för referens-arkitektur. Du kan använda Lösningsacceleratorer som en startpunkt för dina egna IoT-lösning eller som learning-verktyg.

## <a name="overview"></a>Översikt

Referensarkitekturen beskriver elementen i en IoT-lösning som teknikprinciper, sammansättning av Azure IoT-tjänster och enheter. Arkitekturen gör även rekommendationer om implementering av tekniker.

På den högsta nivån, kan du visa en IoT-lösning som som består av:

* Saker som genererar och skickar telemetri till exempel mått och händelser. Enheter, till exempel lastbilar eller hissar är de saker som skickar telemetri i lösningsacceleratorn för fjärrövervakning.
* Information som genererats från telemetri som skickas från sakerna. En regel genererar en insikt i lösningsacceleratorn för fjärrövervakning. En regel kan till exempel identifiera när temperaturen i en motor når ett tröskelvärde.
* Åtgärder baserat på information som hjälp till att förbättra en företags- eller process. En e poståtgärd kan meddela en operatör om ett potentiellt problem med en i lösningsacceleratorn för fjärrövervakning.

Den [Azure IoT-Referensarkitektur](https://aka.ms/iotrefarchitecture) är ett levande dokument uppdateras i takt med att tekniken utvecklas.

## <a name="core-subsystems"></a>Core-undersystem

Referensarkitekturen identifierar core-undersystem som visas i följande diagram:

![Core-undersystem](media/iot-accelerators-architecture-overview/CoreSubsystems.png)

I följande avsnitt beskrivs hur komponenterna i lösningsacceleratorn för fjärrövervakning mappas till core-undersystem.

### <a name="iot-devices"></a>IoT-enheter

En IoT-lösning bör aktivera säker, effektiv och stabil kommunikation mellan nästan alla slags enheter och en molngateway. Enheter är företagets tillgångar mellan enkla temperatursensorer och komplexa fabriksproduktionsrader med hundratals sensorer och komponenter.

En fält-gateway eller edge-enhet är en särskild enhet-installation eller allmänna programvara som kan fungera som:

* Kommunikation-verktyg för att hantera protokollet konvertering.
* Kontrollsystem för lokal enhet och enheten telemetribearbetning hub. En edge-enhet kan bearbeta telemetri lokalt för att styra enheter utan att kommunicera med molnet. En edge-enhet kan också filtrera eller enhetens sammanställda telemetri för att minska mängden telemetri som överförs till molnet.

I lösningen för fjärrövervakning, enheter ansluter till en IoT-hubb och skicka telemetri för bearbetning. Lösningen för fjärrövervakning kan operatörer hantera enheter med jobb eller automatisk enhetshantering. Du kan använda SDK: er för Azure IoT-enheter för att implementera dina enheter.

Lösningen för fjärrövervakning kan distribuera och hantera IoT Edge-enheter. Bearbetning i utkanten hjälper till att minska mängden telemetri som skickas till molnet och snabba svar på enhetshändelser.

### <a name="cloud-gateway"></a>Molngatewayen

En molngateway möjliggör kommunikation till och från enheter och edge-enheter. Dessa enheter kan vara på många fjärranslutna platser.

Gatewaytjänsten hanterar enhetskommunikation, inklusive hantering av anslutningar, skydd av kommunikationsvägen, autentisering och auktorisering. Gatewayen också tillämpar anslutning och dataflöde kvoter och samlar in telemetri som används för fakturering, diagnostik och andra övervakningsaktiviteter.

Lösningen för fjärrövervakning distribuerar en IoT-hubb för att tillhandahålla en säker slutpunkt för enheter för att skicka telemetri till. IoT hub även:

* Innehåller en identity-enhetsbutiken för att hantera enheter som tillåts ansluta till lösningen.
* Gör att lösningen skicka kommandon till enheter. Till exempel för att öppna en kran Frisläpp hög belastning.
* Aktiverar bulk enhetshantering. Till exempel att uppgradera den inbyggda programvaran på en uppsättning enheter.

### <a name="stream-processing"></a>Strömbearbetning

När lösningen matar in telemetri, är det viktigt att förstå hur flödet av bearbetning av telemetriska data kan variera. Beroende på scenario, kan dataposter flöda via olika faser:

* Lagring, till exempel InMemory-cacheminnen, tillfällig köer och permanenta Arkiv.

* Analys för att köra inkommande telemetri via en uppsättning villkor, vilket kan ge olika utdata dataposter. Inkommande telemetri som kodats i Avro kan till exempel returnera utdata telemetri kodning i JSON.

* Ursprungliga inkommande telemetri och analys utdata poster lagras vanligtvis och blir tillgängliga för visning. Inkommande telemetri och utdata poster kan också utlösa åtgärder som e-postmeddelanden, incident biljetter och enhetskommandon.

Routning kan skicka telemetri till en eller flera slutpunkter för lagring, analys processer och åtgärder. En lösning kan kombinera steg i olika ordning och bearbeta dem med samtidiga parallella uppgifter.

Lösningen för fjärrövervakning använder [Azure Stream Analytics](/azure/stream-analytics/) för bearbetning av dataströmmen. Regelmotor i lösningen använder Stream Analytics-frågor för att generera aviseringar och åtgärder. Lösningen kan till exempel använda en fråga för att identifiera när medeltemperaturen i en lastbil storage avdelning under en femminutersperiod understiger 36 grader.

### <a name="storage"></a>Storage

IoT-lösningar kan generera stora mängder data, vilket ofta är time series-data. Dessa data behöver lagras där det kan användas för visualisering och rapportering. En lösning kan också behöva åtkomst till data senare för analys eller ytterligare bearbetning. Det är vanligt att ha data delas upp i varma och kalla datalager. Varma datalagret innehåller de senaste data för låglatensåtkomst. Kalla data store lagras vanligtvis historiska data.

Lösningen för fjärrövervakning använder [Azure Time Series Insights](/azure/time-series-insights/) som varma datalager och Cosmos DB dess kall datalagring.

### <a name="ui-and-reporting-tools"></a>Användargränssnittet och Rapporteringsverktyg

Lösningen Användargränssnittet kan tillhandahålla:

* Åtkomst till och visualisering av resultat för data och analys av enheten.
* Identifiering av enheter via registret.
* Kontroll av enheter.
* Arbetsflöden för etablering av enheter.
* Meddelanden och aviseringar.
* Integrering med live, interaktiva instrumentpaneler för att visa data från ett stort antal enheter.  
* Geografiska plats och geo-medveten tjänster.

Lösningen för fjärrövervakning innehåller ett webbgränssnitt för att användas. Webb-UI innehåller:

* En interaktiv karta som visar platsen för enheter.
* Åtkomst till Time Series Insights explorer att fråga efter och analysera telemetri.

### <a name="business-integration"></a>Business-integrering

Företag integration lagret hanterar integreringen av IoT-lösning med affärssystem som CRM, ERP och av branschspecifika program. Exempel innefattar tjänstens debitering, kundtjänst, och ange reservdelar.

Lösningen för fjärrövervakning använder regler för att skicka e-post när ett villkor uppfylls. Lösningen kan till exempel meddela en operatör när temperaturen i en lastbil understiger 36 grader.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om Azure IoT-Referensarkitektur och såg några exempel på hur lösningsacceleratorn för fjärrövervakning följer sina rekommendationer. Nästa steg är att läsa den [Referensarkitektur för Microsoft Azure IoT](https://aka.ms/iotrefarchitecture).