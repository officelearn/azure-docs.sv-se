---
title: IoT-alternativ för Microsoft Azure | Microsoft Azure
description: Välj hur du implementerar Azure IoT-lösningen med hjälp av Azure IoT Central, IoT Suite eller IoT Hub.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: overview
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 8ed524e87f820f6c1e2e05da0bcbc7241bdd1ec1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201466"
---
# <a name="compare-azure-iot-central-and-azure-iot-options"></a>Jämför alternativen Azure IoT Central och Azure IoT

Microsoft Azure IoT Central och Azure IoT erbjuder flera alternativ för att implementera en IoT-lösning, och vart och ett passar för en mängd olika kundbehov:

* [Azure IoT Central](overview-iot-central.md) är en SaaS-lösning (programvara som en tjänst) som använder en modellbaserad metod. Med Azure IoT Central kan du skapa IoT-lösningar i företagsklass utan expertkunskaper inom utveckling av molnlösningar.

* [Azure IoT Suite](https://docs.microsoft.com/azure/iot-suite/) är en samling [förkonfigurerade lösningar](https://docs.microsoft.com/azure/iot-suite/iot-suite-what-are-preconfigured-solutions) i företagsklass som bygger på Azure PaaS (plattform som en tjänst) som gör det möjligt att påskynda utvecklingen av anpassade IoT-lösningar.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Azure IoT Hub är kärnan i Azure PaaS (plattform som en tjänst) som används av både Azure IoT Central och Azure IoT Suite. IoT Hub möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan flera miljoner IoT-enheter och en molnlösning. IoT Hub hjälper dig att möta utmaningarna för IoT-implementering som:

* Omfattande enhetsanslutning och hantering.
* Omfattande inmatning av telemetri.
* Kontroll av enheter.
* Tillämpning av enhetssäkerhet.

## <a name="compare-azure-iot-central-and-azure-iot-suite"></a>Jämför Azure IoT Central och Azure IoT Suite

Att välja Azure IoT-produkt är en viktig del av planeringen av IoT-lösningen. IoT Hub är en enskild Azure-tjänst som inte är ensamt tillhandahåller en IoT-lösning slutpunkt till slutpunkt. IoT Hub kan användas som en startpunkt för vilken IoT-lösning som helst och du behöver inte använda Azure IoT Suite eller Azure IoT Central för att använda det. Både IoT Suite och Azure IoT Central använder IoT Hub tillsammans med andra Azure-tjänster. I följande tabell sammanfattas de största skillnaderna mellan IoT Suite och Azure IoT Central som hjälper dig att välja rätt för dina behov:

|     | Azure IoT Central | Azure IoT Suite |
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

* **Azure IoT Central**: [Azure IoT Central](overview-iot-central.md).
* **IoT Suite**: [Vad är förkonfigurerade lösningar i Azure IoT Suite?](https://docs.microsoft.com/azure/iot-suite/iot-suite-what-are-preconfigured-solutions).
* **IoT Hub**: [Översikt över tjänsten Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub).