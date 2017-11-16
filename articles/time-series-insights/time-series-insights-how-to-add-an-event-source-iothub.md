---
title: "Hur du lägger till en IoT-hubb händelsekälla Azure tid serien Insights miljön | Microsoft Docs"
description: "Den här artikeln beskriver hur du lägger till en källa som är ansluten till en IoT-hubb i miljön tid serien insikter"
services: time-series-insights
ms.service: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: ed31a0e725d1e0863e9c4695d4eccb324f60678a
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="how-to-add-an-iot-hub-event-source-to-time-series-insights-environment"></a>Hur du lägger till en IoT-hubb händelsekälla tid serien insikter miljö
Den här artikeln beskriver hur du använder Azure-portalen för att lägga till en händelsekälla som läser data från en IoT-hubb i din miljö för tid serien insikter.

## <a name="prerequisites"></a>Krav
- Skapa en tid serien insikter-miljö. Mer information finns i [skapa en Azure tid serien Insights-miljö](time-series-insights-get-started.md) 
- Skapa en IoT-hubb. Mer information om IoT-hubbar finns [skapar en IoT-hubb med Azure-portalen](../iot-hub/iot-hub-create-through-portal.md)
- IoT-hubben måste ha aktiva meddelandet händelser skickas i.
- Skapa en dedikerad konsumentgrupp i IoT-hubb för tid serien Insight miljön att använda från. Varje gång serien insikter händelsekälla måste ha sin egen dedikerad konsumentgrupp som inte delas med andra användare. Om flera läsare förbrukar händelser från samma konsumentgrupp, förväntas alla läsare finns fel. Mer information finns i [IoT-hubb Utvecklarhandbok](../iot-hub/iot-hub-devguide.md).

## <a name="add-a-new-event-source"></a>Lägg till en ny händelsekälla
1. Logga in på [Azure Portal](https://portal.azure.com).

2. Leta upp din befintliga miljö tid serien insikter. Klicka på **alla resurser** på menyn till vänster i Azure-portalen. Välj Time Series Insights-miljö.

3. Under den **miljö topologi** rubrik, klickar du på **händelsekällor**.
   ![Händelsen datakällor + Lägg till](media/time-series-insights-how-to-add-an-event-source-iothub/1-event-sources.png)

4. Klicka på **+ Lägg till**.

5. Ange en **Händelsekällans namn** unika för den här gången serien insikter i miljön som **händelseströmmen**.

   ![Fyll i de tre första parametrarna i formuläret.](media/time-series-insights-how-to-add-an-event-source-iothub/2-import-option.png)

6. Välj den **källa** som **IoT-hubb**.

7. Välj lämpliga **importera alternativet**. 
   - Välj **Använd IoT-hubb från tillgängliga prenumerationer** när du redan har en befintlig IoT-hubb på en av dina prenumerationer. Det här är den enklaste metoden.
   - Välj **ange IoT-hubb inställningar manuellt** när IoT-hubben är externa för dina prenumerationer och du vill välja avancerade alternativ. 

8. Om du har valt den **Använd IoT-hubb från tillgängliga prenumerationer** alternativet i följande tabell beskrivs varje obligatorisk egenskap:

   ![Informationen om prenumeration och Event hub](media/time-series-insights-how-to-add-an-event-source-iothub/3-new-event-source.png)

   | Egenskap | Beskrivning |
   | --- | --- |
   | Prenumerations-ID:t | Välj den prenumeration som den här IoT-hubben har skapats.
   | IoT-hubbnamnet | Välj namnet på IoT-hubben.
   | Principnamn för IoT-hubb | Välj den princip för delad åtkomst som finns på inställningsfliken för IoT-hubb. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. Princip för delad åtkomst för dina händelsekällan *måste* har **tjänsten ansluta** behörigheter.
   | IoT-hubb principnyckel | Nyckeln är förinställd.
   | Konsumentgrupp för IoT-hubb | Konsumentgrupp som händelser ska läsas från IoT-hubben. Vi rekommenderar starkt att använda en dedikerad konsumentgrupp för din händelsekälla.
   | Händelsen serialiseringsformat | JSON är för närvarande endast tillgängligt serialisering. Meddelanden om händelser måste vara i formatet eller inga data kan läsas. |
   | Tidsstämpel egenskapsnamn | För att fastställa detta värde, måste du förstå meddelandeformat för meddelandedata som skickas till IoT-hubb. Det här värdet är den **namn** för egenskapen specifika händelser i meddelandedata som du vill använda som tidsstämpel för händelse. Värdet är skiftlägeskänsligt. När värdet är tomt används den **sätta händelsetid** i händelsen källa används som tidsstämpel för händelse. |

9. Om du har valt den **ange IoT-hubb inställningar manuellt** alternativet i följande tabell beskrivs varje obligatorisk egenskap:

   | Egenskap | Beskrivning |
   | --- | --- |
   | Prenumerations-ID:t | Den prenumeration som den här IoT-hubben har skapats.
   | Resursgrupp | Resursgruppens namn som den här IoT-hubben har skapats.
   | IoT-hubbnamnet | Namnet på din IoT-hubb. När du skapade din IoT-hubb gav du den även ett specifikt namn.
   | Principnamn för IoT-hubb | Den princip för delad åtkomst som kan skapas på inställningsfliken för IoT-hubb. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. Princip för delad åtkomst för dina händelsekällan *måste* har **tjänsten ansluta** behörigheter.
   | IoT-hubb principnyckel | Den delade åtkomstnyckeln som används för att autentisera åtkomst till Service Bus-namnrymd. Ange de primära och sekundära nycklarna här.
   | Konsumentgrupp för IoT-hubb | Konsumentgrupp som händelser ska läsas från IoT-hubben. Vi rekommenderar starkt att använda en dedikerad konsumentgrupp för din händelsekälla.
   | Händelsen serialiseringsformat | JSON är för närvarande endast tillgängligt serialisering. Meddelanden om händelser måste vara i formatet eller inga data kan läsas. |
   | Tidsstämpel egenskapsnamn | För att fastställa detta värde, måste du förstå meddelandeformat för meddelandedata som skickas till IoT-hubb. Det här värdet är den **namn** för egenskapen specifika händelser i meddelandedata som du vill använda som tidsstämpel för händelse. Värdet är skiftlägeskänsligt. När värdet är tomt används den **sätta händelsetid** i händelsen källa används som tidsstämpel för händelse. |

10. Välj **skapa** att lägga till ny händelsekälla.

   ![Klicka på Skapa](media/time-series-insights-how-to-add-an-event-source-iothub/4-create-button.png)

   När du har skapat händelsekällan börjar Time Series Insights automatiskt att strömma data till din miljö.

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Lägg till en konsumentgrupp i din IoT-hubb
Konsumentgrupper används av program för att hämta data från Azure IoT-hubbar. Ange en dedikerad konsumentgrupp för användning av den här gången serien insikter miljön endast på ett tillförlitligt sätt läsa data från IoT-hubb.

Följ dessa steg om du vill lägga till en ny konsumentgrupp i din IoT-hubb:
1. Leta upp och öppna din IoT-hubb i Azure-portalen.

2. Under den **Messaging** rubrik, Välj **slutpunkter**. 

   ![Lägg till en konsumentgrupp](media/time-series-insights-how-to-add-an-event-source-iothub/5-add-consumer-group.png)

3. Välj den **händelser** slutpunkt, och **egenskaper** öppnas.

4. Under den **konsumentgrupper** rubrik, ange ett nytt unikt namn för konsumentgruppen. Använd den här samma namn i tid serien insikter miljö när du skapar en ny händelsekälla.

5. Välj **spara** att spara den nya konsumentgruppen.

## <a name="next-steps"></a>Nästa steg
- [Definiera principer för åtkomst av data](time-series-insights-data-access.md) att skydda data.
- [Skicka händelser](time-series-insights-send-events.md) käll-händelse.
- Åtkomst till din miljö i det [tid serien insikter explorer](https://insights.timeseries.azure.com).
