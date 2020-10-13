---
title: Integrering med andra tjänster
titleSuffix: Azure Digital Twins
description: Förstå ingångs-och utgångs kraven när du distribuerar Azure Digital-dubbla.
author: baanders
ms.author: baanders
ms.date: 3/16/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 7e360c158c7887109684d13f774cbbda1813373e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91729142"
---
# <a name="integrate-azure-digital-twins-with-other-services"></a>Integrera Azure Digitals dubbla med andra tjänster

Azures digitala dubblare används vanligt vis tillsammans med andra tjänster. Med hjälp av [**händelse vägar**](concepts-route-events.md)tar Azure Digitals dubbla data från överordnade tjänster som [IoT Hub](../iot-hub/about-iot-hub.md) eller [Logic Apps](../logic-apps/logic-apps-overview.md)som används för att leverera telemetri och meddelanden. 

Digitala Azure-enheter kan också dirigera data till underordnade tjänster, till exempel [Azure Maps](../azure-maps/about-azure-maps.md) och [Time Series Insights](../time-series-insights/time-series-insights-update-overview.md), för lagring, arbets flödes integrering, analys med mera. 

## <a name="data-ingress"></a>Inkommande data

Azures digitala dubblare kan drivas med data och händelser från vilken tjänst som helst –[IoT Hub](../iot-hub/about-iot-hub.md), [Logic Apps](../logic-apps/logic-apps-overview.md), din egen anpassade tjänst och mer. På så sätt kan du samla in telemetri från fysiska enheter i din miljö och bearbeta dessa data med hjälp av Azures digitala dubbla diagram i molnet.

Azure Digital-dubbla har inte någon inbyggd IoT Hub. Du kan använda en befintlig IoT Hub som du för närvarande har i produktions miljön eller distribuera en ny. Detta ger dig fullständig åtkomst till alla enhets hanterings funktioner i IoT Hub.

Använd en [**Azure-funktion**](../azure-functions/functions-overview.md)för att mata in data från vilken källa som helst i Azure Digitals dubbla. Lär dig mer om det här mönstret i [*How-to: intag av telemetri från IoT Hub*](how-to-ingest-iot-hub-data.md)eller prova själv i självstudien om Azure Digitals dubblare [*: Anslut en lösning från slut punkt till slut punkt*](tutorial-end-to-end.md). 

Du kan också lära dig hur du ansluter Azure Digitals dubbla till en Logic Apps-utlösare i [*How-to: integration med Logic Apps*](how-to-integrate-logic-apps.md).

## <a name="data-egress-services"></a>Utgående tjänster för data

Azure Digitals dubbla, kan skicka data till anslutna **slut punkter**. Slut punkter som stöds kan vara:
* [Händelsehubb](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Slut punkter är kopplade till Azure Digitals dubbla med hanterings-API: er eller Azure Portal. Lär dig mer om hur du ansluter en slut punkt till Azure Digitals dubbla i [*anvisningar: hantera slut punkter och vägar*](how-to-manage-routes-apis-cli.md).

Det finns många andra tjänster där du kanske vill dirigera dina data i slut ändan, till exempel [Azure Storage](../storage/common/storage-introduction.md), [Azure Maps](../azure-maps/about-azure-maps.md)eller [Time Series Insights](../time-series-insights/time-series-insights-update-overview.md). Om du vill skicka dina data till tjänster som de här kan du koppla mål tjänsten till en slut punkt.

Om du till exempel också använder Azure Maps och vill korrelera platsen med dina Azure Digitals dubbla [grafer](concepts-twins-graph.md)kan du använda Azure Functions med event Grid för att upprätta kommunikation mellan alla tjänster i din distribution. Lär dig mer om det här i [ *How-to: använda Azure Digitals flätas för att uppdatera en Azure Maps inomhus karta*](how-to-integrate-maps.md)

Du kan också lära dig hur du dirigerar data på ett liknande sätt som Time Series Insights, i [*How-to: integration with Time Series Insights*](how-to-integrate-time-series-insights.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om slut punkter och vidarebefordran av händelser till externa tjänster:
* [*Koncept: routning av Azure Digitals dubbla händelser*](concepts-route-events.md)

Mer information finns i så här konfigurerar du Azure Digital-dubblare för att mata in data från IoT Hub:
* [*How-to: intag av telemetri från IoT Hub*](how-to-ingest-iot-hub-data.md)
