---
title: Bearbeta data från Event Hubs Azure med hjälp av Stream Analytics | Microsoft Docs
description: Den här artikeln visar hur du bearbetar data från Azure event hub med hjälp av Azure Stream Analytics-jobb.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: f179687b0983e145244e228a3d3b06b4eabead48
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723420"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Bearbeta data från din händelsehubb med hjälp av Azure Stream Analytics
Azure Stream Analytics-tjänsten gör det enkelt att mata in, bearbeta, och analysera strömmande data från Azure Event Hubs, att aktivera kraftfulla insikter och kör realtidsåtgärder. Den här integrationen kan du snabbt skapa en het sökvägsanalys pipeline. Du kan använda Azure-portalen för att visualisera inkommande data och skriva en Stream Analytics-fråga. När din fråga är klar kan flytta du den till produktion på bara några klick. 

## <a name="key-benefits"></a>Viktiga fördelar
Här är de främsta fördelarna med Azure Event Hubs och Azure Stream Analytics-integrering: 
- **Förhandsgranska data** – du kan förhandsgranska inkommande data från en händelsehubb i Azure-portalen.
- **Testa frågan** – förbereda en transformationsfråga och testa det direkt i Azure-portalen. Språk frågesyntaxen finns [Stream Analytics-frågespråket](/stream-analytics-query/built-in-functions-azure-stream-analytics) dokumentation.
- **Distribuera din fråga till produktion** – du kan distribuera frågan i produktionen genom att skapa och starta Azure Stream Analytics-jobb.

## <a name="end-to-end-flow"></a>Slutpunkt till slutpunkt-flöde

1. Logga in på [Azure Portal](https://portal.azure.com). 
1. Gå till din **Event Hubs-namnområdet** och gå sedan till den **händelsehubb**, som har inkommande data. 
1. Välj **processdata** på sidan event hub.  

    ![Bearbeta data panel](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. Välj **utforska** på den **möjliggöra realtidsinsikter från händelser** panelen. 

    ![Select Stream Analytics](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. Du ser en sida för frågan med värden som redan angetts för följande fält:
    1. Din **händelsehubb** som indata för frågan.
    1. Exemplet **SQL-fråga** med SELECT-instruktion. 
    1. En **utdata** alias att referera till testresultaten. 

        ![Frågeredigeraren](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  När du använder den här funktionen för första gången, frågar den här sidan för din tillåtelse för att skapa en konsumentgrupp och en princip för din händelsehubb för att förhandsgranska inkommande data.
1. Välj **skapa** i den **indata förhandsversion** fönstret som visas i föregående bild. 
1. En ögonblicksbild av den senaste inkommande data i den här fliken visas omedelbart.
    - Typen av serialisering i dina data är automatiskt identifierade (JSON/CSV). Du kan manuellt ändra samt till JSON/CSV/AVRO.
    - Du kan förhandsgranska inkommande data i tabellformat eller raw-format. 
    - Om dina data visas inte aktuella väljer **uppdatera** att se de senaste händelserna. 

        Här är ett exempel på data i den **tabellformat**:   ![Resultat i tabellformatet](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Här är ett exempel på data i den **obearbetat format**: 

        ![Resultat i raw-format](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Välj **Testovat dotaz** att se ögonblicksbilden av testresultaten av din fråga i den **testresultat** fliken. Du kan också hämta resultaten.

    ![Testa frågeresultat](./media/process-data-azure-stream-analytics/test-results.png)
1. Skriva en egen fråga för att transformera data. Se [frågespråksreferens för Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference).
1. När du har testat frågan och du vill flytta det produktion, väljer **distribuera fråga**. Skapa ett Azure Stream Analytics-jobb där du kan ange utdata för jobbet och starta jobbet för att distribuera frågan. Ange ett namn för jobbet för att skapa ett Stream Analytics-jobb, och välj **skapa**.

      ![Skapa ett Azure Stream Analytics-jobb](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  Vi rekommenderar att du skapar en konsumentgrupp och en princip för varje ny Azure Stream Analytics-jobb som du skapar från Event Hubs-sidan. Konsumentgrupper kan bara fem samtidiga läsare, så att tillhandahålla en dedikerad konsumentgrupp för varje jobb på så sätt undviker eventuella fel som kan uppstå överskrider denna gräns. En dedikerad princip kan du rotera din nyckel eller återkalla behörigheter utan att påverka andra resurser. 
1. Ditt Stream Analytics-jobb har skapats där din fråga är samma som du har testat och indata är din event hub. 

9.  För att slutföra pipelinen, ange den **utdata** av frågan och välj **starta** att starta jobbet.

    > [!NOTE]
    > Innan du startar jobbet Glöm inte att ersätta outputalias av utdatanamnet som du skapade i Azure Stream Analytics.

      ![Ställ in utdata och starta jobbet](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Kända begränsningar
När du testar din fråga kan ta testresultaten cirka 6 sekunder att läsa in. Vi arbetar på att förbättra prestandan för testning. Men när de distribueras i produktion, har subsecond svarstid i Azure Stream Analytics.

## <a name="streaming-units"></a>Strömningsenheter
Azure Stream Analytics-jobb standardvärdet är tre strömningsenheter (su). Om du vill ändra den här inställningen, Välj **skala** på den vänstra menyn i den **Stream Analytics-jobbet** sidan på Azure portal. Läs mer om enheter för strömning i [förstå och justera Direktuppspelningsenheter](../stream-analytics/stream-analytics-streaming-unit-consumption.md).

![Skala enheter för strömning](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>Nästa steg
Läs mer om Stream Analytics-frågor i [Stream Analytics-frågespråket](/stream-analytics-query/built-in-functions-azure-stream-analytics)