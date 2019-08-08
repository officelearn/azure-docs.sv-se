---
title: Lägga till en händelsekälla för Händelsehubbar i Azure Time Series Insights | Microsoft Docs
description: Den här artikeln beskriver hur du lägger till en händelsekälla som är ansluten till Azure Event Hubs till din Time Series Insights-miljö.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 8403c8fbc4faf35e0ccd3c87347e88a46f0769ff
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854464"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Lägg till en event hub-händelsekälla till Time Series Insights-miljön

Den här artikeln beskriver hur du använder Azure-portalen för att lägga till en händelsekälla som läser data från Azure Event Hubs till Azure Time Series Insights-miljön.

> [!NOTE]
> De steg som beskrivs i den här artikeln gäller både Time Series Insights GA och Time Series Insights Preview-miljöer.

## <a name="prerequisites"></a>Förutsättningar

- Skapa en Time Series Insights-miljö enligt beskrivningen i [skapa en Azure Time Series Insights-miljö](./time-series-insights-update-create-environment.md).
- Skapa en händelsehubb. Se [skapa ett Event Hubs-namnområde och en Event Hub med hjälp av Azure Portal](../event-hubs/event-hubs-create.md).
- Event hub måste ha aktiva meddelandehändelser skickas till den. Lär dig hur du [skickar händelser till Azure Event Hubs med hjälp av .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Skapa en dedikerad konsumentgrupp i händelsehubb som Time Series Insights-miljön kan använda från. Varje Time Series Insights-händelsekälla måste ha sin egen dedikerad konsumentgrupp som inte delas med andra konsumenter. Om flera läsare konsumera händelser från samma konsumentgruppen, förmodligen alla läsare ser fel. Det finns en gräns på 20 konsumentgrupper per händelsehubb. Mer information finns i [programmeringsguiden för Event Hubs](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Lägg till en konsumentgrupp till din event hub

Program använda konsumentgrupper att hämta data från Azure Event Hubs. Om du vill läsa data på ett tillförlitligt sätt från händelsehubben kan du ange en dedikerad konsument grupp som endast används av den här Time Series Insightss miljön.

Så här lägger du till en ny konsumentgrupp i din event hub

1. Leta upp och öppna din event hub i Azure-portalen.

1. Under **entiteter**väljer **konsumentgrupper**, och välj sedan **konsumentgrupp**.

   [![Händelsehubben – Lägg till en konsument grupp](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png#lightbox)

1. På den **konsumentgrupper** anger du ett nytt unikt värde för **namn**.  Använd det här namnet när du skapar en ny händelsekälla i Time Series Insights-miljö.

1. Välj **Skapa**.

## <a name="add-a-new-event-source"></a>Lägg till en ny händelsekälla

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Leta upp din befintliga Time Series Insights-miljö. I den vänstra menyn väljer du **alla resurser**, och välj sedan din Time Series Insights-miljö.

1. Under **miljöns topologi**väljer **händelsekällor**, och välj sedan **Lägg till**.

   [![Under händelse källor väljer du knappen Lägg till](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png#lightbox)

1. Ange ett värde för **Händelsekällans namn för** som är unik för den här Time Series Insights-miljön, till exempel **händelseströmmen**.

1. För **källa**väljer **Event Hub**.

1. Välj lämpliga värden för **importalternativ**:
   - Om du har en befintlig händelsehubb i en av dina prenumerationer, väljer **Använd Event Hub från tillgängliga prenumerationer**. Det här alternativet är den enklaste metoden.

       [![I fönstret ny händelse källa anger du värden för de första tre parametrarna](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png#lightbox)


       [![Information om prenumeration och händelsehubben](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png#lightbox)

     I följande tabell beskrivs de nödvändiga egenskaperna för den **Använd Event Hub från tillgängliga prenumerationer** alternativet:

     | Egenskap | Beskrivning |
     | --- | --- |
     | Prenumerations-ID:t | Välj den prenumeration som den här händelsehubben skapades.
     | Service Bus-namnområde | Välj det Azure Service Bus-namnområde som innehåller event hub.
     | Namn på händelsehubb | Välj namnet på händelsehubben.
     | Principnamn för Event hub | Välj princip för delad åtkomst. Du kan skapa princip för delad åtkomst i event hub **konfigurera** fliken. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. Princip för delad åtkomst för din händelsekälla *måste* har **läsa** behörigheter.
     | Principnyckel för Event hub | Nyckelvärdet kan fylls i automatiskt.
     | Händelsehubbkonsumentgrupp | Konsumentgruppen som läser händelser från event hub. Vi rekommenderar starkt att du använder en dedikerad konsumentgrupp för din händelsekälla. |
     | Händelseserialiseringsformat | JSON är för närvarande endast tillgängligt serialiseringsformatet. Händelse meddelanden måste ha det här formatet eller så går det inte att läsa data. |
     | Egenskapsnamnet för tidsstämpeln | För att fastställa det här värdet, måste du förstå meddelandeformat för meddelandedata som skickas till händelsehubben. Det här värdet är den **namn** för egenskapen specifik händelse i meddelandedata som du vill använda som tidsstämpel för händelsen. Värdet är skiftlägeskänsligt. Om det lämnas tomt används det **sätta händelsetid** i källan används som tidsstämpel för händelsen. |

    - Om event hub är extern till dina prenumerationer eller om du vill välja avancerade alternativ väljer **ange Event Hub-inställningar manuellt**.

      I följande tabell beskrivs de nödvändiga egenskaperna för den **ange Event Hub-inställningar manuellt** alternativet:
 
      | Egenskap | Beskrivning |
      | --- | --- |
      | Prenumerations-ID:t | Den prenumeration som den här händelsehubben skapades.
      | Resursgrupp | Den resursgrupp som den här händelsehubben skapades.
      | Service Bus-namnområde | Ett Service Bus-namnområde är en behållare för en uppsättning meddelandeentiteter. När du har skapat en ny händelsehubb, skapade du även en Service Bus-namnrymd.
      | Namn på händelsehubb | Namnet på din event hub. När du skapade din event hub gav du den även ett specifikt namn.
      | Principnamn för Event hub | Princip för delad åtkomst. Du kan skapa en princip för delad åtkomst i event hub **konfigurera** fliken. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. Princip för delad åtkomst för din händelsekälla *måste* har **läsa** behörigheter.
      | Principnyckel för Event hub | Den delade åtkomstnyckeln som används för att autentisera åtkomsten till Service Bus-namnområdet. Ange den primära eller sekundära nyckeln här.
      | Händelsehubbkonsumentgrupp | Konsumentgruppen som läser händelser från event hub. Vi rekommenderar starkt att du använder en dedikerad konsumentgrupp för din händelsekälla.
      | Händelseserialiseringsformat | JSON är för närvarande endast tillgängligt serialiseringsformatet. Händelse meddelanden måste ha det här formatet eller så går det inte att läsa data. |
      | Egenskapsnamnet för tidsstämpeln | För att fastställa det här värdet, måste du förstå meddelandeformat för meddelandedata som skickas till händelsehubben. Det här värdet är den **namn** för egenskapen specifik händelse i meddelandedata som du vill använda som tidsstämpel för händelsen. Värdet är skiftlägeskänsligt. Om det lämnas tomt används det **sätta händelsetid** i källan används som tidsstämpel för händelsen. |

1. Lägg till dedikerade Time Series Insights konsument gruppnamnet som du lade till din event hub.

1. Välj **Skapa**.

   [![Välj Skapa](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png#lightbox)

   När du har skapat händelsekällan börjar Time Series Insights automatiskt strömmande data i din miljö.

## <a name="next-steps"></a>Nästa steg

* [Definiera dataåtkomstprinciper](time-series-insights-data-access.md) att skydda data.

* [Skicka händelser](time-series-insights-send-events.md) till händelsekällan.

* Få tillgång till den i den [Time Series Insights explorer](https://insights.timeseries.azure.com).
