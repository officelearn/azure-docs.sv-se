---
title: IoT-alternativ för Microsoft Azure | Microsoft Azure
description: Välj hur du vill implementera din Azure IoT-lösning – med Azure IoT Central, IoT-lösningsacceleratorer eller IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 2ec6e1d00d331a7bb8dd7182dd4b0c91dd3d25a7
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74049095"
---
# <a name="compare-azure-iot-central-and-azure-iot-options"></a>Jämför alternativen Azure IoT Central och Azure IoT

Microsoft Azure IoT Central och Azure IoT erbjuder flera alternativ för att skapa en IoT-lösning. De här alternativen är lämpliga för olika uppsättningar av kundkrav:

* [Azure IoT Central](overview-iot-central.md) är en IoT-programplattform som använder en modell baserad metod för att hjälpa dig att bygga IoT-lösningar i företags klass utan att kräva expert kunskaper om utveckling av moln lösningar.

* [Azure IoT-lösningsacceleratorer](https://docs.microsoft.com/azure/iot-accelerators/) är en samling [lösningsacceleratorer](../../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md) i företagsklass som bygger på Azure PaaS (Platform as a Service), som du kan använda för att påskynda utvecklingen av anpassade IoT-lösningar.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Azure IoT Hub är kärnan i Azure PaaS och används av både Azure IoT Central och Azure IoT-lösningsacceleratorer. IoT Hub stödjer tillförlitlig och säker dubbelriktad kommunikation mellan miljontals IoT-enheter och en molnlösning. IoT Hub hjälper dig att klara av utmaningar för IoT-implementering såsom:

* Höga volymer med enhetsanslutning och hantering
* Höga volymer med inmatning av telemetri
* Styrning och kontroll av enheter
* Tillämpning av enhetssäkerhet

## <a name="compare-azure-iot-central-and-azure-iot-solution-accelerators"></a>Jämför Azure IoT Central och Azure IoT-lösningsacceleratorer

Att välja Azure IoT-produkt är en viktig del av planeringen av IoT-lösningen. IoT Hub är en enskild Azure-tjänst som inte är ensamt tillhandahåller en IoT-lösning slutpunkt till slutpunkt. Du kan använda IoT Hub som startpunkt för vilken IoT-lösning som helst. Du behöver heller inte använda Azure IoT-lösningsacceleratorer eller Azure IoT Central för att använda IoT Hub. Både IoT-lösningsacceleratorer och Azure IoT Central använder IoT Hub tillsammans med andra Azure-tjänster.

I följande tabell sammanfattas de största skillnaderna mellan IoT-lösningsacceleratorer och Azure IoT Central, så att det blir lättare för dig att välja rätt metod för dina behov:

|     | Azure IoT Central | Azure IoT-lösningsacceleratorer |
| --- | ----------- | --------- |
| Primär användning                      | Om du vill påskynda processen att få ut lösningen på marknaden för enkla IoT-lösningar som inte kräver mycket tjänstanpassning.                                                    | Om du vill påskynda utvecklingen av en anpassad IoT-lösning som behöver maximal flexibilitet.                                                                                                                             |
| Åtkomst till underliggande PaaS-tjänster | Eftersom det är en fullständigt hanterad lösning görs de underliggande tjänsterna inte tillgängliga.                                                                                            | Du har åtkomst till de underliggande Azure-tjänsterna och kan hantera eller ersätta dem efter behov.                                                                                                                    |
| Flexibilitet                        | Medel. Du kan använda den inbyggda webbläsarbaserade användarupplevelsen för att anpassa lösningsmodellen och delar av användargränssnittet. Infrastrukturen kan inte anpassas eftersom de olika komponenterna inte görs tillgängliga. | Hög. Mikrotjänsterna har öppen källkod, och du kan göra ändringar i den som du vill. Du kan dessutom anpassa distributionsinfrastrukturen.                                               |
| Kompetensnivå                        | Låg. Du behöver modelleringskunskaper för att anpassa lösningen. Inga kunskaper om kodning krävs.                                                                          | Medelhög. Du behöver kunskaper i Java eller .NET för att anpassa lösningens serverdel. Du behöver kunskaper i JavaScript för att anpassa visualiseringen.                                                                       |
| Komma igång med upplevelsen             | Programmallar och enhetsmallar tillhandahåller inbyggda modeller. Kan distribueras på minuter.                                                                                                  | Förkonfigurerade lösningar implementerar vanliga IoT-scenarier. Kan distribueras på minuter.                                                                                                                            |
| Priser                            | Enkel förutsägbar prissättningsstruktur.                                                                                                                           | Du kan finjustera tjänsterna för att kontrollera kostnaden.                                                                                                                                                            |

Vilka produkter som ska användas för att skapa din IoT-lösning beror på:

* Dina affärskrav.
* Den typ av lösning som du vill skapa.
* Din organisations kunskaper för att skapa och hantera lösningen på lång sikt.

## <a name="next-steps"></a>Nästa steg

Baserat på den valda produkten och metoden är nästa föreslagna steg:

* **Azure IoT Central**: [vad är Azure IoT Central?](overview-iot-central.md)
* **IoT Solution Accelerators**: [Vad är Azure IoT Solution-acceleratorer?](../../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md)
* **IoT Hub**: [vad är Azure IoT Hub?](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub)