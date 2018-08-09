---
title: Lägga till en IoT Hub-händelsekälla till Azure Time Series Insights | Microsoft Docs
description: Den här artikeln beskrivs hur du lägger till en händelsekälla som är ansluten till en IoT-hubb i miljön för Time Series Insights
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/21/2017
ms.openlocfilehash: b6beecbf64cee925f62ac4c82919926fcb79940a
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627380"
---
# <a name="how-to-add-an-iot-hub-event-source-to-time-series-insights-environment"></a>Hur du lägger till en IoT Hub-händelsekälla till Time Series Insights-miljö
Den här artikeln beskriver hur du använder Azure-portalen för att lägga till en händelsekälla som läser data från en IoT-hubb i din Time Series Insights-miljö.

## <a name="prerequisites"></a>Förutsättningar
- Skapa en Time Series Insights-miljö. Mer information finns i [skapa en Azure Time Series Insights-miljö](time-series-insights-get-started.md) 
- Skapa en IoT Hub. Mer information om IoT-hubbar finns [skapar en IoT Hub med Azure portal](../iot-hub/iot-hub-create-through-portal.md)
- IoT-hubben måste ha aktiva meddelandehändelser som skickas.
- Skapa en dedikerad konsumentgrupp i IoT Hub för Time Series Insight-miljö för att använda. Varje Time Series Insights-händelsekälla måste ha sin egen dedikerad konsumentgrupp som inte delas med andra användare. Om flera läsare konsumera händelser från samma konsumentgruppen, förmodligen alla läsare ser fel. Mer information finns i [utvecklarhandboken för IoT Hub](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Lägg till en konsumentgrupp till din IoT Hub
Konsumentgrupper används av program för att hämta data från Azure IoT-hubbar. Tillhandahåller en dedikerad konsumentgrupp för användning av den här Time Series Insights-miljön, att på ett tillförlitligt sätt läsa data från IoT Hub.

Följ dessa steg om du vill lägga till en ny konsumentgrupp i din IoT-hubb:
1. Leta upp och öppna din IoT-hubb i Azure-portalen.

2. Under den **Messaging** väljer **slutpunkter**. 

   ![Lägg till en konsumentgrupp](media/time-series-insights-how-to-add-an-event-source-iothub/5-add-consumer-group.png)

3. Välj den **händelser** slutpunkt, och **egenskaper** öppnas.

4. Under den **konsumentgrupper** rubrik, ange ett nytt unikt namn för konsumentgruppen. Använd det här namnet i Time Series Insights-miljö när du skapar en ny händelsekälla.

5. Välj **spara** att spara den nya konsumentgruppen.

## <a name="add-a-new-event-source"></a>Lägg till en ny händelsekälla
1. Logga in på [Azure Portal](https://portal.azure.com).

2. Leta upp din befintliga Time Series Insights-miljö. Klicka på **alla resurser** på menyn på vänster sida av Azure-portalen. Välj Time Series Insights-miljö.

3. Under den **miljöns topologi** klickar **händelsekällor**.
   ![Händelsen datakällor + Lägg till](media/time-series-insights-how-to-add-an-event-source-iothub/1-event-sources.png)

4. Klicka på **+ Lägg till**.

5. Ange en **Händelsekällans namn för** unika för den här Time Series Insights-miljön som **händelseströmmen**.

   ![Fyll i de första tre parametrarna i formuläret.](media/time-series-insights-how-to-add-an-event-source-iothub/2-import-option.png)

6. Välj den **källa** som **IoT-hubb**.

7. Välj lämplig **importalternativ**. 
   - Välj **Använd IoT Hub från tillgängliga prenumerationer** när du redan har en befintlig IoT-hubb på en av dina prenumerationer. Detta är den enklaste metoden.
   - Välj **ger IoT Hub-inställningar manuellt** när IoT-hubben som är externa för dina prenumerationer eller du vill välja avancerade alternativ. 

8. Om du har valt den **Använd IoT Hub från tillgängliga prenumerationer** alternativet i följande tabell beskrivs varje obligatorisk egenskap:

   ![Prenumeration och Event hub-information](media/time-series-insights-how-to-add-an-event-source-iothub/3-new-event-source.png)

   | Egenskap  | Beskrivning |
   | --- | --- |
   | Prenumerations-ID | Välj den prenumeration som den här IoT-hubben har skapats.
   | IoT Hub-namn | Välj namnet på IoT-hubben.
   | Principnamn för IoT Hub | Välj den princip för delad åtkomst som finns på inställningsfliken för IoT Hub. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. Princip för delad åtkomst för din händelsekälla *måste* har **tjänsten ansluta** behörigheter.
   | Principnyckel för IoT Hub | Nyckeln innehåller redan.
   | IoT Hub-konsumentgrupp | Konsumentgrupp att läsa händelser från IoT Hub. Vi rekommenderar starkt att använda en dedikerad konsumentgrupp för din händelsekälla.
   | Händelseserialiseringsformat | JSON är för närvarande endast tillgängligt serialisering. Händelsemeddelanden måste vara i formatet eller inga data kan läsas. |
   | Egenskapsnamnet för tidsstämpeln | För att fastställa det här värdet, måste du förstå meddelandeformat för meddelandedata som skickas till IoT Hub. Det här värdet är den **namn** för egenskapen specifik händelse i meddelandedata som du vill använda som tidsstämpel för händelsen. Värdet är skiftlägeskänsligt. När lämnas tomt används det **sätta händelsetid** i händelsen används källa som tidsstämpel för händelsen. |

9. Om du har valt den **ger IoT Hub-inställningar manuellt** alternativet i följande tabell beskrivs varje obligatorisk egenskap:

   | Egenskap  | Beskrivning |
   | --- | --- |
   | Prenumerations-ID | Den prenumeration som den här IoT-hubben har skapats.
   | Resursgrupp | Resursgruppens namn som den här IoT-hubben har skapats.
   | IoT Hub-namn | Namnet på din IoT-hubb. När du har skapat din IoT-hubb, gav du den även ett specifikt namn.
   | Principnamn för IoT Hub | Den princip för delad åtkomst som kan skapas på inställningsfliken för IoT Hub. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. Princip för delad åtkomst för din händelsekälla *måste* har **tjänsten ansluta** behörigheter.
   | Principnyckel för IoT Hub | Den delade åtkomstnyckeln som används för att autentisera åtkomst till Service Bus-namnområdet. Ange den primära eller sekundära nyckeln här.
   | IoT Hub-konsumentgrupp | Konsumentgrupp att läsa händelser från IoT Hub. Vi rekommenderar starkt att använda en dedikerad konsumentgrupp för din händelsekälla.
   | Händelseserialiseringsformat | JSON är för närvarande endast tillgängligt serialisering. Händelsemeddelanden måste vara i formatet eller inga data kan läsas. |
   | Egenskapsnamnet för tidsstämpeln | För att fastställa det här värdet, måste du förstå meddelandeformat för meddelandedata som skickas till IoT Hub. Det här värdet är den **namn** för egenskapen specifik händelse i meddelandedata som du vill använda som tidsstämpel för händelsen. Värdet är skiftlägeskänsligt. När lämnas tomt används det **sätta händelsetid** i händelsen används källa som tidsstämpel för händelsen. |

10. Lägg till dedikerade TSI konsument gruppnamnet som du lade till din IoT-hubb.

11. Välj **skapa** att lägga till nya händelsekällan.

   ![Klicka på Skapa](media/time-series-insights-how-to-add-an-event-source-iothub/4-create-button.png)

   När du har skapat händelsekällan börjar Time Series Insights automatiskt att strömma data till din miljö.

## <a name="next-steps"></a>Nästa steg
- [Definiera dataåtkomstprinciper](time-series-insights-data-access.md) att skydda data.
- [Skicka händelser](time-series-insights-send-events.md) till händelsekällan.
- Få tillgång till den i den [Time Series Insights explorer](https://insights.timeseries.azure.com).
