---
title: Hur du lägger till en Händelsehubb händelsekälla Azure tid serien Insights | Microsoft Docs
description: Den här artikeln beskriver hur du lägger till en källa som är ansluten till en Händelsehubb i miljön tid serien insikter.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: edett
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/21/2017
ms.openlocfilehash: 780a7cb3035dbe19c45b5fe9c6dfae54fccafd03
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293656"
---
# <a name="how-to-add-an-event-hub-event-source-to-time-series-insights-environment"></a>Hur du lägger till en Händelsehubb händelsekälla tid serien insikter miljö

Den här artikeln beskriver hur du använder Azure-portalen för att lägga till en händelsekälla som läser data från en Händelsehubb i din miljö för tid serien insikter.

## <a name="prerequisites"></a>Förutsättningar
- Skapa en tid serien insikter-miljö. Mer information finns i [skapa en Azure tid serien Insights-miljö](time-series-insights-get-started.md) 
- Skapar en händelsehubb. Mer information om Händelsehubbar finns [skapa ett namnområde för Händelsehubbar och en händelsehubb med hjälp av Azure portal](../event-hubs/event-hubs-create.md)
- Händelsehubben måste ha aktiva meddelandet händelser skickas i. Mer information finns i [skicka händelser till Händelsehubbar i Azure med hjälp av .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Skapa en dedikerad konsumentgrupp i Event Hub för tid serien Insight miljön att använda från. Varje gång serien insikter händelsekälla måste ha sin egen dedikerad konsumentgrupp som inte delas med andra användare. Om flera läsare förbrukar händelser från samma konsumentgrupp, förväntas alla läsare finns fel. Observera att det finns också en gräns på 20 konsumentgrupper per Event Hub. Mer information finns i [Programmeringsguide för Event Hubs](../event-hubs/event-hubs-programming-guide.md).

## <a name="add-a-new-event-source"></a>Lägg till en ny händelsekälla
1. Logga in på [Azure Portal](https://portal.azure.com).

2. Leta upp din befintliga miljö tid serien insikter. Klicka på **alla resurser** på menyn till vänster i Azure-portalen. Välj Time Series Insights-miljö.

3. Under den **miljö topologi** rubrik, klickar du på **händelsekällor**.

   ![Händelsen datakällor + Lägg till](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)

4. Klicka på **+ Lägg till**.

5. Ange en **Händelsekällans namn** unika för den här gången serien insikter i miljön som **händelseströmmen**.

   ![Fyll i de tre första parametrarna i formuläret.](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)

6. Välj den **källa** som **Händelsehubb**.

7. Välj lämpliga **importera alternativet**. 
   - Välj **Använd Händelsehubb från tillgängliga prenumerationer** när du redan har en befintlig Händelsehubb på en av dina prenumerationer. Det här är den enklaste metoden.
   - Välj **ange Event Hub-inställningar manuellt** när Händelsehubben är externa för dina prenumerationer och du vill välja avancerade alternativ. 

8. Om du har valt den **Använd Händelsehubb från tillgängliga prenumerationer** alternativet i följande tabell beskrivs varje obligatorisk egenskap:

   ![Informationen om prenumeration och Event hub](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)

   | Egenskap  | Beskrivning |
   | --- | --- |
   | Prenumerations-ID | Välj den prenumeration som den här händelsehubben skapades.
   | Service Bus-namnrymd | Välj Service Bus-namnrymd som innehåller Händelsehubben.
   | Namnet på händelsehubben | Välj namnet på Händelsehubben.
   | Namnet på händelsehubben princip | Välj den princip för delad åtkomst som kan skapas på fliken Event Hub konfigurera. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. Princip för delad åtkomst för dina händelsekällan *måste* har **läsa** behörigheter.
   | Event hub principnyckel | Värdet för nyckeln kan fyllas i.
   | Event hub konsumentgrupp | Konsumentgrupp att läsa händelser från Event Hub. Vi rekommenderar starkt att använda en dedikerad konsumentgrupp för din händelsekälla. |
   | Händelseserialiseringsformat | JSON är för närvarande endast tillgängligt serialisering. Meddelanden om händelser måste vara i formatet eller inga data kan läsas. |
   | Tidsstämpel egenskapsnamn | För att fastställa detta värde, måste du förstå meddelandeformat för meddelandedata som skickas till Händelsehubben. Det här värdet är den **namn** för egenskapen specifika händelser i meddelandedata som du vill använda som tidsstämpel för händelse. Värdet är skiftlägeskänsligt. När värdet är tomt används den **sätta händelsetid** i händelsen källa används som tidsstämpel för händelse. |


9. Om du har valt den **ange Event Hub-inställningar manuellt** alternativet i följande tabell beskrivs varje obligatorisk egenskap:

   | Egenskap  | Beskrivning |
   | --- | --- |
   | Prenumerations-ID:t | Den prenumeration som den här händelsehubben skapades.
   | Resursgrupp | Resursgruppens namn som den här händelsehubben skapades.
   | Service Bus-namnrymd | En Service Bus-namnrymd är en behållare för en uppsättning meddelandeentiteter. När du har skapat en ny Händelsehubb skapade du även en Service Bus-namnrymd.
   | Namnet på händelsehubben | Namnet på din Event Hub. När du skapade din händelsehubb gav du den även ett specifikt namn.
   | Namnet på händelsehubben princip | Den princip för delad åtkomst som kan skapas på fliken Event Hub konfigurera. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. Princip för delad åtkomst för dina händelsekällan *måste* har **läsa** behörigheter.
   | Event hub principnyckel | Den delade åtkomstnyckeln som används för att autentisera åtkomst till Service Bus-namnrymd. Ange de primära och sekundära nycklarna här.
   | Event hub konsumentgrupp | Konsumentgrupp att läsa händelser från Event Hub. Vi rekommenderar starkt att använda en dedikerad konsumentgrupp för din händelsekälla.
   | Händelseserialiseringsformat | JSON är för närvarande endast tillgängligt serialisering. Meddelanden om händelser måste vara i formatet eller inga data kan läsas. |
   | Tidsstämpel egenskapsnamn | För att fastställa detta värde, måste du förstå meddelandeformat för meddelandedata som skickas till Händelsehubben. Det här värdet är den **namn** för egenskapen specifika händelser i meddelandedata som du vill använda som tidsstämpel för händelse. Värdet är skiftlägeskänsligt. När värdet är tomt används den **sätta händelsetid** i händelsen källa används som tidsstämpel för händelse. |


10. Välj **skapa** att lägga till ny händelsekälla.
   
   ![Klicka på Skapa](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)

   När du har skapat händelsekällan börjar Time Series Insights automatiskt att strömma data till din miljö.


### <a name="add-a-consumer-group-to-your-event-hub"></a>Lägg till en konsumentgrupp till din Event Hub
Konsumentgrupper används av program för att hämta data från Azure Event Hubs. Ange en dedikerad konsumentgrupp för användning av den här tiden serien insikter miljön, på ett tillförlitligt sätt läsa data från din Event Hub.

Följ dessa steg för att lägga till en ny konsumentgrupp i din Händelsehubb:
1. Leta upp och öppna din Händelsehubb i Azure-portalen.

2. Under den **entiteter** rubrik, Välj **konsumentgrupper**.

   ![Event Hub - Lägg till en konsumentgrupp](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)

3. Välj **+ konsumentgrupp** att lägga till en ny konsumentgrupp. 

4. På den **konsumentgrupper** anger du en ny unik **namn**.  Använd den här samma namn när du skapar en ny händelsekälla i miljön tid serien insikter.

5. Välj **skapa** att skapa nya konsumentgrupp.

## <a name="next-steps"></a>Nästa steg
- [Definiera principer för åtkomst av data](time-series-insights-data-access.md) att skydda data.
- [Skicka händelser](time-series-insights-send-events.md) käll-händelse.
- Åtkomst till din miljö i det [tid serien insikter explorer](https://insights.timeseries.azure.com).
