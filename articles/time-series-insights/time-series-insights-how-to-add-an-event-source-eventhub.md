---
title: Lägg till en Event Hubs händelse källa – Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du lägger till en händelse källa för Azure-Event Hubs i din Time Series Insights-miljö.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/02/2019
ms.custom: seodec18
ms.openlocfilehash: c8f123871f1e87a18dadfa82ad6bb27d1c145dc4
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863350"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Lägg till en event hub-händelsekälla till Time Series Insights-miljön

Den här artikeln beskriver hur du använder Azure-portalen för att lägga till en händelsekälla som läser data från Azure Event Hubs till Azure Time Series Insights-miljön.

> [!NOTE]
> De steg som beskrivs i den här artikeln gäller både Time Series Insights GA och Time Series Insights Preview-miljöer.

## <a name="prerequisites"></a>Krav

- Skapa en Time Series Insights-miljö enligt beskrivningen i [skapa en Azure Time Series Insights-miljö](./time-series-insights-update-create-environment.md).
- Skapa en händelsehubb. Läs [skapa ett Event Hubs-namnområde och en Event Hub med hjälp av Azure Portal](../event-hubs/event-hubs-create.md).
- Event hub måste ha aktiva meddelandehändelser skickas till den. Lär dig hur du [skickar händelser till Azure Event Hubs med hjälp av .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Skapa en dedikerad konsumentgrupp i händelsehubb som Time Series Insights-miljön kan använda från. Varje Time Series Insights-händelsekälla måste ha sin egen dedikerad konsumentgrupp som inte delas med andra konsumenter. Om flera läsare förbrukar händelser från samma konsument grupp, kommer alla läsare att kunna uppvisa problem. Det finns en gräns på 20 konsumentgrupper per händelsehubb. Mer information finns i [programmerings guiden för Event Hubs](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Lägg till en konsumentgrupp till din event hub

Program använda konsumentgrupper att hämta data från Azure Event Hubs. Om du vill läsa data på ett tillförlitligt sätt från händelsehubben kan du ange en dedikerad konsument grupp som endast används av den här Time Series Insightss miljön.

Så här lägger du till en ny konsumentgrupp i din event hub

1. I [Azure Portal](https://portal.azure.com)letar du reda på och öppnar Event Hub-instansen från fönstret **Översikt** i Event Hub-namnområdet. Välj **entiteter > Event Hubs** eller Sök efter din instans under **namn**.

    [![öppna ditt Event Hub-namnområde](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. I Event Hub-instansen väljer du **entiteter > konsument grupper**. Välj sedan **+ konsument grupp** för att lägga till en ny konsument grupp. 

   [![Event Hub – Lägg till en konsument grupp](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   Annars väljer du en befintlig konsument grupp och går vidare till nästa avsnitt.

1. På den **konsumentgrupper** anger du ett nytt unikt värde för **namn**.  Använd det här namnet när du skapar en ny händelsekälla i Time Series Insights-miljö.

1. Välj **Skapa**.

## <a name="add-a-new-event-source"></a>Lägg till en ny händelsekälla

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Leta upp din befintliga Time Series Insights-miljö. I den vänstra menyn väljer du **alla resurser**, och välj sedan din Time Series Insights-miljö.

1. Välj **händelse källor**och välj sedan **Lägg till**.

   [Välj knappen Lägg till ![under händelse källor](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. Ange ett värde för **händelse källans namn** som är unikt för den här Time Series Insightss miljön, till exempel `Contoso-TSI-GA-Event-Hub-ES`.

1. För **källa**väljer **Event Hub**.

1. Välj lämpliga värden för **importalternativ**:

   * Om du har en befintlig händelsehubb i en av dina prenumerationer, väljer **Använd Event Hub från tillgängliga prenumerationer**. Det här alternativet är den enklaste metoden.

     [![Välj ett import alternativ för händelse källa](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

    *  I följande tabell beskrivs de nödvändiga egenskaperna för den **Använd Event Hub från tillgängliga prenumerationer** alternativet:

       [![information om prenumeration och händelsehubben](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

       | Egenskap | Beskrivning |
       | --- | --- |
       | Prenumeration | Prenumerationen på den önskade Event Hub-instansen och namn området tillhör. |
       | Namnområde för händelsehubb | Event Hub-namnområdet som den önskade Event Hub-instansen tillhör. |
       | Namn på händelsehubb | Namnet på den önskade Event Hub-instansen. |
       | Princip värde för Event Hub | Välj önskad princip för delad åtkomst. Du kan skapa principen för delad åtkomst på fliken **Konfigurera konfiguration** av händelsehubben. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomst nycklar. Princip för delad åtkomst för din händelsekälla *måste* har **läsa** behörigheter. |
       | Principnyckel för Event Hub | Fylls i automatiskt från det valda värdet för Event Hub-principen. |

    * Om event hub är extern till dina prenumerationer eller om du vill välja avancerade alternativ väljer **ange Event Hub-inställningar manuellt**.

       I följande tabell beskrivs de nödvändiga egenskaperna för den **ange Event Hub-inställningar manuellt** alternativet:
 
       | Egenskap | Beskrivning |
       | --- | --- |
       | Prenumerations-ID | Prenumerationen på den önskade Event Hub-instansen och namn området tillhör. |
       | Resursgrupp | Resurs gruppen som den önskade Event Hub-instansen och namn området tillhör. |
       | Namnområde för händelsehubb | Event Hub-namnområdet som den önskade Event Hub-instansen tillhör. |
       | Namn på händelsehubb | Namnet på den önskade Event Hub-instansen. |
       | Princip värde för Event Hub | Välj önskad princip för delad åtkomst. Du kan skapa principen för delad åtkomst på fliken **Konfigurera konfiguration** av händelsehubben. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomst nycklar. Princip för delad åtkomst för din händelsekälla *måste* har **läsa** behörigheter. |
       | Principnyckel för Event Hub | Den delade åtkomstnyckeln som används för att autentisera åtkomsten till Service Bus-namnområdet. Ange den primära eller sekundära nyckeln här. |

    * Båda alternativen delar följande konfigurations alternativ:

       | Egenskap | Beskrivning |
       | --- | --- |
       | Konsumentgrupp för händelsehubb | Konsumentgruppen som läser händelser från event hub. Vi rekommenderar starkt att du använder en dedikerad konsumentgrupp för din händelsekälla. |
       | Händelseserialiseringsformat | JSON är för närvarande endast tillgängligt serialiseringsformatet. Händelse meddelanden måste ha det här formatet eller så går det inte att läsa data. |
       | Egenskapsnamnet för tidsstämpeln | För att fastställa det här värdet, måste du förstå meddelandeformat för meddelandedata som skickas till händelsehubben. Det här värdet är den **namn** för egenskapen specifik händelse i meddelandedata som du vill använda som tidsstämpel för händelsen. Värdet är skiftlägeskänsligt. Om det lämnas tomt används det **sätta händelsetid** i källan används som tidsstämpel för händelsen. |

1. Lägg till dedikerade Time Series Insights konsument gruppnamnet som du lade till din event hub.

1. Välj **Skapa**.

   När händelse källan har skapats börjar Time Series Insights automatiskt strömma data till din miljö.

## <a name="next-steps"></a>Nästa steg

* [Definiera dataåtkomstprinciper](time-series-insights-data-access.md) att skydda data.

* [Skicka händelser](time-series-insights-send-events.md) till händelsekällan.

* Få tillgång till den i den [Time Series Insights explorer](https://insights.timeseries.azure.com).
