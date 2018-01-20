---
title: "Lär dig hur du använder Azure Service Bus-anslutningen i dina logic apps | Microsoft Docs"
description: "Skapa logikappar med Azure App service. Ansluta till Azure Service Bus skickar och tar emot meddelanden. Du kan utföra åtgärder, till exempel skicka kö, skickar till avsnitt, tar emot från kön och tar emot från prenumerationen."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/02/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 89bf0ffec759fca4af5f99af1b6a2dd8d641ff6f
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-azure-service-bus-connector"></a>Kom igång med Azure Service Bus-koppling
Ansluta till Azure Service Bus skickar och tar emot meddelanden. Du kan utföra åtgärder, till exempel skicka kö, skickar till avsnitt, tar emot från kön och tar emot från prenumerationen.

Att använda [alla anslutningar](apis-list.md), måste du först skapa en logikapp. Du kan komma igång med [att skapa en logikapp nu](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-service-bus"></a>Ansluta till Service Bus
Innan din logikapp kan komma åt någon tjänst, måste du först skapa en anslutning till tjänsten. En [anslutning](connectors-overview.md) tillhandahåller anslutningen mellan en logikapp och en annan tjänst.  

> [!INCLUDE [Steps to create a connection to Azure Service Bus](../../includes/connectors-create-api-servicebus.md)]
> 
> 

## <a name="use-a-service-bus-trigger"></a>Använda en Service Bus-utlösare
En utlösare är en händelse som kan användas för att starta arbetsflödet som definierats i en logikapp. [Mer information om utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

> [!INCLUDE [Steps to create a Service Bus trigger](../../includes/connectors-create-api-servicebus-trigger.md)]
> 
> 

## <a name="use-a-service-bus-action"></a>Använda en Service Bus-åtgärd
En åtgärd är en åtgärd som utförs av arbetsflödet som definierats i en logikapp. [Mer information om åtgärder](../logic-apps/logic-apps-overview.md#logic-app-concepts).

[!INCLUDE [Steps to create a Service Bus action](../../includes/connectors-create-api-servicebus-action.md)]

## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/servicebus/). 

## <a name="next-steps"></a>Nästa steg
[Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

