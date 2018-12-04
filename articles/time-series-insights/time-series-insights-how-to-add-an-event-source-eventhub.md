---
title: Lägga till en Event Hub-händelsekälla till Azure Time Series Insights | Microsoft Docs
description: Den här artikeln beskriver hur du lägger till en händelsekälla som är ansluten till en Händelsehubb i miljön för Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/30/2018
ms.openlocfilehash: 93076a8b0d11e05b86c20868d4bc23f4826f107f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52832453"
---
# <a name="how-to-add-an-event-hub-event-source-to-time-series-insights-environment"></a>Hur du lägger till en Event Hub-händelsekälla till Time Series Insights-miljö

Den här artikeln beskriver hur du använder Azure-portalen för att lägga till en händelsekälla som läser data från en Händelsehubb i din Time Series Insights-miljö.

> [!NOTE]
> Dessa anvisningar gäller för båda Time Series Insights GA och Time Series Insights uppdatera privat förhandsgranskning miljöer.

## <a name="prerequisites"></a>Förutsättningar

- Skapa en Time Series Insights-miljö. Mer information finns i [skapa en Azure Time Series Insights-miljö](./time-series-insights-update-create-environment.md).
- Skapar en händelsehubb. Mer information om Händelsehubbar finns i [skapa ett Event Hubs-namnområde och en event hub med Azure-portalen](../event-hubs/event-hubs-create.md).
- Event Hub måste ha aktiva meddelandehändelser som skickas. Mer information finns i [skicka händelser till Azure Event Hubs med .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Skapa en dedikerad konsumentgrupp i Händelsehubb för Time Series Insight-miljö du använder från. Varje Time Series Insights-händelsekälla måste ha sin egen dedikerad konsumentgrupp som inte delas med andra användare. Om flera läsare konsumera händelser från samma konsumentgruppen, förmodligen alla läsare ser fel. Observera att det finns också en gräns på 20 konsumentgrupper per Event Hub. Mer information finns i [Programmeringsguide för Event Hubs](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Lägg till en konsumentgrupp till din Event Hub

Konsumentgrupper används av program för att hämta data från Azure Event Hubs. Tillhandahåller en dedikerad konsumentgrupp för användning av den här Time Series Insights-miljön, att på ett tillförlitligt sätt läsa data från din Event Hub.

Följ dessa steg för att lägga till en ny konsumentgrupp i din Event Hub:

1. Leta upp och öppna din Event Hub i Azure-portalen.

1. Under den **entiteter** väljer **konsumentgrupper**.

   ![Händelsehubb – Lägg till en konsumentgrupp](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)

1. Välj **+ konsumentgrupp** att lägga till en ny konsumentgrupp. 

1. På den **konsumentgrupper** anger du ett nytt unikt **namn**.  Använd det här namnet när du skapar en ny händelsekälla i Time Series Insights-miljö.

1. Välj **skapa** att skapa nya konsumentgruppen.

## <a name="add-a-new-event-source"></a>Lägg till en ny händelsekälla

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Leta upp din befintliga Time Series Insights-miljö. Klicka på **alla resurser** på menyn på vänster sida av Azure-portalen. Välj Time Series Insights-miljö.

1. Under den **miljöns topologi** klickar **händelsekällor**.

   ![Händelsen datakällor + Lägg till](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)

1. Klicka på **+ Lägg till**.

1. Ange en **Händelsekällans namn för** unika för den här Time Series Insights-miljön som **händelseströmmen**.

   ![Fyll i de första tre parametrarna i formuläret.](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)

1. Välj den **källa** som **Händelsehubb**.

1. Välj lämplig **importalternativ**.
   - Välj **Använd Event Hub från tillgängliga prenumerationer** när du redan har en befintlig Händelsehubb på en av dina prenumerationer. Detta är den enklaste metoden.
   - Välj **ange Event Hub-inställningar manuellt** när Event Hub är externa för dina prenumerationer eller du vill välja avancerade alternativ.

1. Om du har valt den **Använd Event Hub från tillgängliga prenumerationer** alternativet i följande tabell beskrivs varje obligatorisk egenskap:

   ![Prenumeration och Event hub-information](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)

   | Egenskap  | Beskrivning |
   | --- | --- |
   | Prenumeration-ID | Välj den prenumeration som den här händelsehubben skapades.
   | service bus namnrymd | Välj Service Bus-namnområde som innehåller Event Hub.
   | Namn på händelsehubb | Välj namnet på Händelsehubben.
   | Principnamn för Event hub | Välj den princip för delad åtkomst som kan skapas på fliken Konfigurera för Event Hub. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. Princip för delad åtkomst för din händelsekälla *måste* har **läsa** behörigheter.
   | Principnyckel för Event hub | Nyckelvärdet kan fylls i automatiskt.
   | Händelsehubbkonsumentgrupp | Konsumentgrupp att läsa händelser från Event Hub. Vi rekommenderar starkt att använda en dedikerad konsumentgrupp för din händelsekälla. |
   | Händelseserialiseringsformat | JSON är för närvarande endast tillgängligt serialisering. Händelsemeddelanden måste vara i formatet eller inga data kan läsas. |
   | Egenskapsnamnet för tidsstämpeln | För att fastställa det här värdet, måste du förstå meddelandeformat för meddelandedata som skickas till Event Hub. Det här värdet är den **namn** för egenskapen specifik händelse i meddelandedata som du vill använda som tidsstämpel för händelsen. Värdet är skiftlägeskänsligt. När lämnas tomt används det **sätta händelsetid** i händelsen används källa som tidsstämpel för händelsen. |

1. Om du har valt den **ange Event Hub-inställningar manuellt** alternativet i följande tabell beskrivs varje obligatorisk egenskap:

   | Egenskap  | Beskrivning |
   | --- | --- |
   | Prenumerations-ID:t | Den prenumeration som den här händelsehubben skapades.
   | Resursgrupp | Den resursgrupp som den här händelsehubben skapades.
   | service bus namnrymd | Ett Service Bus-namnområde är en behållare för en uppsättning meddelandeentiteter. När du har skapat en ny Händelsehubb, skapade du även en Service Bus-namnrymd.
   | Namn på händelsehubb | Namnet på din Event Hub. När du skapade din event hub gav du den även ett specifikt namn.
   | Principnamn för Event hub | Den princip för delad åtkomst som kan skapas på fliken Konfigurera för Event Hub. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. Princip för delad åtkomst för din händelsekälla *måste* har **läsa** behörigheter.
   | Principnyckel för Event hub | Den delade åtkomstnyckeln som används för att autentisera åtkomst till Service Bus-namnområdet. Ange den primära eller sekundära nyckeln här.
   | Händelsehubbkonsumentgrupp | Konsumentgrupp att läsa händelser från Event Hub. Vi rekommenderar starkt att använda en dedikerad konsumentgrupp för din händelsekälla.
   | Händelseserialiseringsformat | JSON är för närvarande endast tillgängligt serialisering. Händelsemeddelanden måste vara i formatet eller inga data kan läsas. |
   | Egenskapsnamnet för tidsstämpeln | För att fastställa det här värdet, måste du förstå meddelandeformat för meddelandedata som skickas till Event Hub. Det här värdet är den **namn** för egenskapen specifik händelse i meddelandedata som du vill använda som tidsstämpel för händelsen. Värdet är skiftlägeskänsligt. När lämnas tomt används det **sätta händelsetid** i händelsen används källa som tidsstämpel för händelsen. |

1. Lägg till dedikerade TSI konsument gruppnamnet som du lade till din Event hub.

1. Välj **skapa** att lägga till nya händelsekällan.

   ![Klicka på Skapa](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)

   När du har skapat händelsekällan börjar Time Series Insights automatiskt att strömma data till din miljö.

## <a name="next-steps"></a>Nästa steg

[Definiera dataåtkomstprinciper](time-series-insights-data-access.md) att skydda data.

[Skicka händelser](time-series-insights-send-events.md) till händelsekällan.

Få tillgång till den i den [Time Series Insights explorer](https://insights.timeseries.azure.com).
