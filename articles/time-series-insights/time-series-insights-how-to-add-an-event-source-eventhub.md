---
title: Lägga till en händelsehubbarhändelsekälla – Azure Time Series Insights | Microsoft-dokument
description: Lär dig hur du lägger till en Azure Event Hubs-händelsekälla i din Time Series Insights-miljö.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/30/2020
ms.custom: seodec18
ms.openlocfilehash: c3b06289ba6ce98d4307a8255981ecdba069fdfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905410"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Lägga till en händelsehubbhändelsekälla i time series insights-miljön

I den här artikeln beskrivs hur du använder Azure-portalen för att lägga till en händelsekälla som läser data från Azure Event Hubs i din Azure Time Series Insights-miljö.

> [!NOTE]
> De steg som beskrivs i den här artikeln gäller både för förhandsversionerna för Time Series Insights GA och Time Series Insights.

## <a name="prerequisites"></a>Krav

- Skapa en Time Series Insights-miljö enligt beskrivningen i [Skapa en Azure Time Series Insights-miljö](./time-series-insights-update-create-environment.md).
- Skapa en händelsehubb. Läs [Skapa ett namnområde för händelsehubbar och en händelsehubb med hjälp av Azure-portalen](../event-hubs/event-hubs-create.md).
- Händelsehubben måste ha aktiva meddelandehändelser skickade till sig. Lär dig hur du [skickar händelser till Azure Event Hubs med hjälp av .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Skapa en dedikerad konsumentgrupp i händelsehubben som time series insights-miljön kan använda från. Varje Time Series Insights-händelsekälla måste ha en egen dedikerad konsumentgrupp som inte delas med någon annan konsument. Om flera läsare använder händelser från samma konsumentgrupp, är det troligt att alla läsare uppvisar fel. Det finns en gräns på 20 konsumentgrupper per händelsenav. Mer information finns i [programmeringsguiden för Event Hubs](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Lägga till en konsumentgrupp i händelsehubben

Program använder konsumentgrupper för att hämta data från Azure Event Hubs. Om du vill läsa data från händelsehubben på ett tillförlitligt sätt kan du tillhandahålla en dedikerad konsumentgrupp som endast används av den här Time Series Insights-miljön.

Så här lägger du till en ny konsumentgrupp i händelsehubben:

1. Leta reda på och öppna händelsehubbininstan i **fönstret Översikt** för namnområdet för händelsehubben i Azure-portalen. [Azure portal](https://portal.azure.com) Välj **entiteter > händelsehubbar** eller hitta din förekomst under **Namn**.

    [![Öppna namnområdet för händelsehubben](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. I händelsehubbinstan väljer du **Entiteter > Konsumentgrupper**. Välj sedan **+ Konsumentgrupp** om du vill lägga till en ny konsumentgrupp. 

   [![Händelsenav - Lägga till en konsumentgrupp](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   Annars väljer du en befintlig konsumentgrupp och går vidare till nästa avsnitt.

1. Ange ett nytt unikt värde för **Namn**på sidan **Konsumentgrupper** .  Använd samma namn när du skapar en ny händelsekälla i time series insights-miljön.

1. Välj **Skapa**.

## <a name="add-a-new-event-source"></a>Lägga till en ny händelsekälla

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Hitta din befintliga Time Series Insights-miljö. På den vänstra menyn väljer du **Alla resurser**och väljer sedan miljön Tidsseriestatistik.

1. Välj **Händelsekällor**och välj sedan **Lägg till**.

   [![Under Händelsekällor väljer du knappen Lägg till](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. Ange ett värde för **händelsekällans namn** som är unikt `Contoso-TSI-GA-Event-Hub-ES`för den här Time Series Insights-miljön, till exempel .

1. För **Källa**väljer du **Event Hub**.

1. Välj lämpliga värden för **importalternativet:**

   * Om du har en befintlig händelsehubb i en av dina prenumerationer väljer du **Använd händelsehubb från tillgängliga prenumerationer**. Det här alternativet är det enklaste tillvägagångssättet.

     [![Välj ett importalternativ för händelsekälla](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

    *  I följande tabell beskrivs de egenskaper som krävs för **alternativet Använd händelsehubb från tillgängliga prenumerationer:**

       [![Information om prenumeration och händelsehubb](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

       | Egenskap | Beskrivning |
       | --- | --- |
       | Prenumeration | Prenumerationen som den önskade händelsehubbinstansen och namnområdet tillhör. |
       | Namnområde för händelsehubb | Händelsehubbens namnområde som den önskade händelsenavet-förekomsten tillhör. |
       | Namn på händelsehubb | Namnet på den önskade händelsehubbinstansen. |
       | Principvärde för händelsehubben | Välj önskad princip för delad åtkomst. Du kan skapa principen för delad åtkomst på fliken **Konfigurera** händelsehubb. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomstnycklar. Principen för delad åtkomst för händelsekällan *måste* ha läsbehörighet. **read** |
       | Principnyckel för händelsehubben | Förifylld från det valda principvärdet För händelsehubben. |

    * Om händelsehubben är externt för dina prenumerationer eller om du vill välja avancerade alternativ väljer du **Ange inställningar för händelsehubb manuellt**.

       I följande tabell beskrivs de egenskaper som krävs för alternativet Ange inställningar för **händelsehubbar manuellt:**
 
       | Egenskap | Beskrivning |
       | --- | --- |
       | Prenumerations-ID:t | Prenumerationen som den önskade händelsehubbinstansen och namnområdet tillhör. |
       | Resursgrupp | Resursgruppen som den önskade händelsenavetinstansen och namnområdet tillhör. |
       | Namnområde för händelsehubb | Händelsehubbens namnområde som den önskade händelsenavet-förekomsten tillhör. |
       | Namn på händelsehubb | Namnet på den önskade händelsehubbinstansen. |
       | Principvärde för händelsehubben | Välj önskad princip för delad åtkomst. Du kan skapa principen för delad åtkomst på fliken **Konfigurera** händelsehubb. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomstnycklar. Principen för delad åtkomst för händelsekällan *måste* ha läsbehörighet. **read** |
       | Principnyckel för händelsehubben | Den delade åtkomstnyckeln som används för att autentisera åtkomsten till servicebussens namnområde. Ange den primära eller sekundära nyckeln här. |

    * Båda alternativen delar följande konfigurationsalternativ:

       | Egenskap | Beskrivning |
       | --- | --- |
       | Konsumentgrupp för händelsehubb | Konsumentgruppen som läser händelser från händelsehubben. Vi rekommenderar starkt att du använder en dedikerad konsumentgrupp för din händelsekälla. |
       | Händelseserialiseringsformat | För närvarande är JSON det enda tillgängliga serialiseringsformatet. Händelsemeddelanden måste vara i det här formatet eller så går det inte att läsa data. |
       | Egenskapsnamn för tidsstämpel | För att fastställa det här värdet måste du förstå meddelandeformatet för meddelandedata som skickas till händelsehubben. Det här värdet är **namnet** på den specifika händelseegenskapen i de meddelandedata som du vill använda som händelsetidsstämpel. Värdet är skiftlägeskänsligt. Om händelsen lämnas tom används **händelsens kötid** i händelsekällan som tidsstämpel för händelsen. |

1. Lägg till det dedikerade tidsseriestatistikkonsumentgruppsnamnet som du har lagt till i händelsehubben.

1. Välj **Skapa**.

   När händelsekällan har skapats börjar Time Series Insights automatiskt strömma data till din miljö.

## <a name="next-steps"></a>Nästa steg

* Definiera principer för [dataåtkomst](time-series-insights-data-access.md) för att skydda data.

* [Skicka händelser](time-series-insights-send-events.md) till händelsekällan.

* Få tillgång till din miljö i [Utforskaren för Time Series Insights](https://insights.timeseries.azure.com).
