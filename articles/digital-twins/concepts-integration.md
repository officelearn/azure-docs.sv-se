---
title: Integrering med andra tjänster
titleSuffix: Azure Digital Twins
description: Förstå ingångs-och utgångs kraven när du distribuerar Azure Digital-dubbla.
author: baanders
ms.author: baanders
ms.date: 3/16/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 9c7c1000ed5229886d4a964161042eca6517c230
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87367445"
---
# <a name="integrate-azure-digital-twins-with-other-services"></a>Integrera Azure Digitals dubbla med andra tjänster

Azures digitala dubblare används vanligt vis tillsammans med andra tjänster. Med hjälp av [**händelse vägar**](concepts-route-events.md)tar Azure Digitals dubbla data från överordnade tjänster, till exempel [IoT Hub](../iot-hub/about-iot-hub.md), som används för att leverera telemetri och meddelanden. Azure Digitals-enheter kan också dirigera data till underordnade tjänster för lagring, arbets flödes integrering, analys och annan användning. 

## <a name="data-ingress"></a>Inkommande data

Azures digitala dubblare kan drivas med data och händelser från vilken tjänst som helst – IoT Hub, Logic Apps, din egen anpassade tjänst och mer. På så sätt kan du samla in telemetri från fysiska enheter i din miljö och bearbeta dessa data med hjälp av Azures digitala dubbla diagram i molnet.

Azure Digital-dubbla har inte någon inbyggd IoT Hub. Du kan använda en befintlig IoT Hub som du för närvarande har i produktions miljön eller distribuera en ny. Detta ger dig fullständig åtkomst till alla enhets hanterings funktioner i IoT Hub.

Använd en [Azure-funktion](../azure-functions/functions-overview.md)för att mata in data från vilken källa som helst i Azure Digitals dubbla. Lär dig mer om det här mönstret i [*How-to: intag av telemetri från IoT Hub*](how-to-ingest-iot-hub-data.md)eller prova själv i självstudien om Azure Digitals dubblare [*: Anslut en lösning från slut punkt till slut punkt*](tutorial-end-to-end.md).

## <a name="data-egress-services"></a>Utgående tjänster för data

Azure Digitals dubbla, kan skicka data till anslutna **slut punkter**. Slut punkter som stöds kan vara:
* [Händelsehubb](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Slut punkter är kopplade till Azure Digitals dubbla med hanterings-API: er eller Azure Portal. Lär dig mer om hur du ansluter en slut punkt till Azure Digitals dubbla i [*anvisningar: hantera slut punkter och vägar*](how-to-manage-routes.md).

Det finns många andra tjänster där du kanske vill dirigera dina data i slut ändan, till exempel [Azure Storage](../storage/common/storage-introduction.md) eller [Time Series Insights](../time-series-insights/time-series-insights-update-overview.md). Om du vill skicka dina data till tjänster som de här kan du koppla mål tjänsten till en slut punkt.

Om du till exempel också använder [Azure Maps](../azure-maps/about-azure-maps.md) och vill korrelera platsen med dina Azure Digitals dubbla [grafer](concepts-twins-graph.md)kan du använda Azure Functions med event Grid för att upprätta kommunikation mellan alla tjänster i din distribution.

## <a name="next-steps"></a>Nästa steg

Läs mer om slut punkter och vidarebefordran av händelser till externa tjänster:
* [*Koncept: routning av Azure Digitals dubbla händelser*](concepts-route-events.md)

Mer information finns i så här konfigurerar du Azure Digital-dubblare för att mata in data från IoT Hub:
* [*How-to: intag av telemetri från IoT Hub*](how-to-ingest-iot-hub-data.md)
