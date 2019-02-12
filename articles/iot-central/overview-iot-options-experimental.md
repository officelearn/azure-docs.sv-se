---
title: IoT-alternativ för Microsoft Azure | Microsoft Azure
description: Välj hur du vill implementera din Azure IoT-lösning – med Azure IoT Central, IoT-lösningsacceleratorer eller IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 370e9068ea4395069a34e70a44664ffd72edb3fb
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823944"
---
# <a name="compare-azure-iot-central-and-azure-iot-options"></a>Jämför alternativen Azure IoT Central och Azure IoT

Microsoft Azure IoT Central och Azure IoT erbjuder flera alternativ för att implementera en IoT-lösning, och vart och ett passar för en mängd olika kundbehov:

* [Azure IoT Central](overview-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) är en SaaS-lösning (programvara som en tjänst) som använder en modellbaserad metod. Med Azure IoT Central kan du skapa IoT-lösningar i företagsklass utan expertkunskaper inom utveckling av molnlösningar.

* [Azure IoT-lösningsacceleratorer](https://docs.microsoft.com/azure/iot-accelerators/) är en samling [lösningsacceleratorer](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) i företagsklass som bygger på Azure PaaS (Platform as a Service), som gör det möjligt att påskynda utvecklingen av anpassade IoT-lösningar.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Azure IoT Hub är kärnan i Azure PaaS och används av både Azure IoT Central och Azure IoT-lösningsacceleratorer. IoT Hub möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan flera miljoner IoT-enheter och en molnlösning. IoT Hub hjälper dig att möta utmaningarna för IoT-implementering som:

* Omfattande enhetsanslutning och hantering.
* Omfattande inmatning av telemetri.
* Kontroll av enheter.
* Tillämpning av enhetssäkerhet.

## <a name="compare-azure-iot-central-and-azure-iot-solution-accelerators"></a>Jämför Azure IoT Central och Azure IoT-lösningsacceleratorer

Att välja Azure IoT-produkt är en viktig del av planeringen av IoT-lösningen. IoT Hub är en enskild Azure-tjänst som inte är ensamt tillhandahåller en IoT-lösning slutpunkt till slutpunkt. IoT Hub kan användas som utgångspunkt för alla slags IoT-lösningar. Du behöver inte använda Azure IoT-lösningsacceleratorer eller Azure IoT Central för att kunna använda IoT Hub. Både IoT-lösningsacceleratorer och Azure IoT Central använder IoT Hub tillsammans med andra Azure-tjänster. I följande tabell sammanfattas de största skillnaderna mellan IoT-lösningsacceleratorer och Azure IoT Central, så att det blir lättare för dig att välja rätt metod för dina behov:

|     | Azure IoT Central | Azure IoT-lösningsacceleratorer |
| --- | ----------- | --------- |
| Primär användning                      | Om du vill påskynda processen att få ut lösningen på marknaden för enkla IoT-lösningar som inte kräver mycket tjänstanpassning.                                                    | Om du vill påskynda utvecklingen av en anpassad IoT-lösning som behöver maximal flexibilitet.                                                                                                                             |
| Åtkomst till underliggande PaaS-tjänster | SaaS. Fullständigt hanterad lösning, de underliggande tjänsterna är inte gjorda tillgängliga.                                                                                            | Du har åtkomst till de underliggande Azure-tjänsterna och kan hantera eller ersätta dem efter behov.                                                                                                                    |
| Flexibilitet                        | Medel. Du kan använda den inbyggda webbläsarbaserade användarupplevelsen för att anpassa lösningsmodellen och delar av användargränssnittet. Infrastrukturen kan inte anpassas eftersom de olika komponenterna inte har gjorts tillgängliga. | Hög. Koden för mikrotjänsterna är öppen källkod och du kan göra ändringar i den som du vill. Du kan dessutom anpassa distributionsinfrastrukturen.                                               |
| Kompetensnivå                        | Låg. Du behöver modelleringskunskaper för att anpassa lösningen. Inga kunskaper om kodning krävs.                                                                          | Mellanhög. Du behöver kunskaper i Java eller .NET för att anpassa lösningens serverdel. Du behöver kunskaper i JavaScript för att anpassa visualiseringen.                                                                       |
| Kom igång             | Programmallar tillhandahåller förskapade modeller. Kan distribueras på minuter.                                                                                                  | Förkonfigurerade lösningar implementerar vanliga IoT-scenarier. Kan distribueras på minuter.                                                                                                                            |
| Prissättning                            | Enkel förutsägbar prissättningsstruktur.                                                                                                                           | Du kan finjustera tjänsterna för att kontrollera kostnaden.                                                                                                                                                            |

Beslutet vilken produkt som ska användas för att skapa IoT-lösningen bestäms slutligen av:

* Dina affärskrav.
* Typ av lösning som du vill skapa
* Din organisations kunskaper för att skapa och hantera lösningen på lång sikt.

## <a name="next-steps"></a>Nästa steg

Baserat på den valda produkten och metoden är nästa föreslagna steg:

* **Azure IoT Central**: [Azure IoT Central](overview-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
* **IoT-lösningsacceleratorer**: [Vad är Azure IoT-lösningsacceleratorer?](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md).
* **IoT Hub**: [Översikt över Azure IoT Hub-tjänsten](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub).