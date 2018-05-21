---
title: IoT-alternativ för Microsoft Azure | Microsoft Azure
description: Välj hur du vill implementera din IoT-lösning – med Azure IoT-lösningsacceleratorer, Azure IoT Central eller Azure IoT Hub.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.assetid: 2d38d08a-4133-4e5c-8b28-f93cadb5df05
ms.service: iot-suite
ms.topic: get-started-article
ms.date: 11/10/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0d6fc2a49034b2e01a0397fe256a2b44030777b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="compare-azure-iot-options"></a>Jämför Azure IoT-alternativ

Artikeln [Azure och Sakernas Internet](iot-suite-what-is-azure-iot.md) beskriver en typisk IoT-lösningsarkitektur med följande nivåer:

* Enhetsanslutning och hantering
* Databearbetning och analys
* Presentation och företagsintegrering

För att implementera den här arkitekturen erbjuder Azure IoT flera olika alternativ. Varje alternativ är lämpligt för olika uppsättningar av kundbehov:

* [Azure IoT-lösningsacceleratorer](index.md) är en samling [lösningsacceleratorer](iot-suite-what-are-solution-accelerators.md) i företagsklass som bygger på Azure PaaS (Platform as a Service), som gör det möjligt att påskynda utvecklingen av anpassade IoT-lösningar.

* [Azure IoT Central](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions) är en SaaS-lösning (programvara som en tjänst) som använder en modellbaserad metod. Med Azure IoT Central kan du skapa IoT-lösningar i företagsklass utan expertkunskaper inom utveckling av molnlösningar.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Azure IoT Hub är kärnan i Azure PaaS och används av både Azure IoT Central och Azure IoT-lösningsacceleratorer. IoT Hub möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan flera miljoner IoT-enheter och är en molnlösning. IoT Hub hjälper dig att möta utmaningarna för IoT-implementering som:

* Omfattande enhetsanslutning och hantering.
* Omfattande inmatning av telemetri.
* Kontroll av enheter.
* Tillämpning av enhetssäkerhet.

## <a name="compare-azure-iot-solution-accelerators-and-azure-iot-central"></a>Jämför Azure IoT-lösningsacceleratorer och Azure IoT Central

Att välja Azure IoT-produkt är en viktig del av planeringen av IoT-lösningen. IoT Hub är en enskild Azure-tjänst som inte är ensamt tillhandahåller en IoT-lösning slutpunkt till slutpunkt. IoT Hub kan användas som utgångspunkt för alla slags IoT-lösningar. Du behöver inte använda Azure IoT-lösningsacceleratorer eller Azure IoT Central för att kunna använda IoT Hub. Både Azure IoT-lösningsacceleratorer och Azure IoT Central använder IoT Hub tillsammans med andra Azure-tjänster. I följande tabell sammanfattas de största skillnaderna mellan Azure IoT-lösningsacceleratorer och Azure IoT Central, så att det blir lättare för dig att välja rätt metod för dina behov:

|                        | Azure IoT-lösningsacceleratorer | Azure IoT Central |
| ---------------------- | --------- | ----------- |
| Primär användning | Om du vill påskynda utvecklingen av en anpassad IoT-lösning som behöver maximal flexibilitet. | Om du vill påskynda processen att få ut lösningen på marknaden för enkla IoT-lösningar som inte kräver mycket tjänstanpassning. |
| Åtkomst till underliggande PaaS-tjänster          | Du har åtkomst till de underliggande Azure-tjänsterna och kan hantera eller ersätta dem efter behov. | SaaS. Fullständigt hanterad lösning, de underliggande tjänsterna är inte gjorda tillgängliga. |
| Flexibilitet            | Hög. Koden för mikrotjänsterna är öppen källkod och du kan göra ändringar i den som du vill. Du kan dessutom anpassa distributionsinfrastrukturen.| Medel. Du kan använda den inbyggda webbläsarbaserade användarupplevelsen för att anpassa lösningsmodellen och delar av användargränssnittet. Infrastrukturen kan inte anpassas eftersom de olika komponenterna inte har gjorts tillgängliga.|
| Kompetensnivå                 | Mellanhög. Du behöver kunskaper i Java eller .NET för att anpassa lösningens serverdel. Du behöver kunskaper i JavaScript för att anpassa visualiseringen. | Låg. Du behöver modelleringskunskaper för att anpassa lösningen. Inga kunskaper om kodning krävs. |
| Kom igång | Lösningsacceleratorer implementerar vanliga IoT-scenarier. Kan distribueras på minuter. | Programmallar tillhandahåller förskapade modeller. Kan distribueras på minuter. |
| Prissättning                | Du kan finjustera tjänsterna för att kontrollera kostnaden. | Enkel förutsägbar prissättningsstruktur. |

Beslutet vilken produkt som ska användas för att skapa IoT-lösningen bestäms slutligen av:

* Dina affärskrav.
* Typ av lösning som du vill skapa
* Din organisations kunskaper för att skapa och hantera lösningen på lång sikt.

## <a name="next-steps"></a>Nästa steg

Baserat på den valda produkten och metoden är nästa föreslagna steg:

* **Azure IoT-lösningsacceleratorer**: [Vad är Azure IoT-lösningsacceleratorer för något?](iot-suite-what-are-solution-accelerators.md)
* **Azure IoT Central**: [Azure IoT Central](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions).
* **IoT Hub**: [Översikt över tjänsten Azure IoT Hub](../iot-hub/iot-hub-what-is-iot-hub.md).
