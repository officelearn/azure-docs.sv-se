---
title: Bearbeta data från Event Hubs Azure med Stream Analytics | Microsoft-dokument
description: Den här artikeln visar hur du bearbetar data från din Azure-händelsehubb med hjälp av ett Azure Stream Analytics-jobb.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: 531426656fe833752c9c4685688c00de3894895b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69991949"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Bearbeta data från händelsehubben med Azure Stream Analytics 
Azure Stream Analytics-tjänsten gör det enkelt att inta, bearbeta och analysera strömmande data från Azure Event Hubs, vilket möjliggör kraftfulla insikter för att driva realtidsåtgärder. Med den här integreringen kan du snabbt skapa en pipeline för analys av heta vägar. Du kan använda Azure-portalen för att visualisera inkommande data och skriva en Stream Analytics-fråga. När frågan är klar kan du flytta den till produktion med bara några få klick. 

## <a name="key-benefits"></a>Viktiga fördelar
Här är de viktigaste fördelarna med Azure Event Hubs och Azure Stream Analytics-integrering: 
- **Förhandsgranska data** – Du kan förhandsgranska inkommande data från en händelsehubb i Azure-portalen.
- **Testa din fråga** – Förbered en omvandlingsfråga och testa den direkt i Azure-portalen. Dokumentation för frågespråksyntaxen finns i Dokumentation [om Frågespråk för Strömma analytics.](/stream-analytics-query/built-in-functions-azure-stream-analytics)
- **Distribuera din fråga till produktion** – Du kan distribuera frågan till produktion genom att skapa och starta ett Azure Stream Analytics-jobb.

## <a name="end-to-end-flow"></a>Flöde från på sluten tid

1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Navigera till **namnområdet Event Hubs** och navigera sedan till **händelsehubben**, som har inkommande data. 
1. Välj **Processdata** på händelsehubbens sida.  

    ![Panelen Bearbeta data](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. Välj **Utforska** på panelen Aktivera insikter i realtid från evenemangspanelen. **Enable real-time insights from events** 

    ![Välj Stream Analytics](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. Du ser en frågesida med värden som redan angetts för följande fält:
    1. Din **händelsehubb** som en indata för frågan.
    1. Exempel på **SQL-fråga** med SELECT-uttryck. 
    1. Ett **utdataalias** som refererar till frågetestresultaten. 

        ![Frågeredigeraren](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  När du använder den här funktionen för första gången frågar den här sidan efter din tillåtelse att skapa en konsumentgrupp och en princip för att händelsehubben ska förhandsgranska inkommande data.
1. Välj **Skapa** i **förhandsgranskningsfönstret För indata** som visas i föregående bild. 
1. Du ser omedelbart en ögonblicksbild av de senaste inkommande data på den här fliken.
    - Serialiseringstypen i dina data identifieras automatiskt (JSON/CSV). Du kan manuellt ändra det också till JSON / CSV / AVRO.
    - Du kan förhandsgranska inkommande data i tabellformat eller råformat. 
    - Om dina data som visas inte är aktuella väljer du **Uppdatera** för att se de senaste händelserna. 

        Här är ett exempel på data ![i **tabellformatet**: Resultat i tabellformatet](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Här är ett exempel på data i **råformat:** 

        ![Resulterar i råformat](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Välj **Testfråga** om du vill visa ögonblicksbild av testresultaten för frågan på fliken **Testresultat.** Du kan också ladda ner resultaten.

    ![Testa frågeresultat](./media/process-data-azure-stream-analytics/test-results.png)
1. Skriv din egen fråga för att omvandla data. Se [Referens för frågespråk för Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference).
1. När du har testat frågan och vill flytta den till produktion väljer du **Distribuera fråga**. Om du vill distribuera frågan skapar du ett Azure Stream Analytics-jobb där du kan ange en utdata för jobbet och starta jobbet. Om du vill skapa ett Stream Analytics-jobb anger du ett namn för jobbet och väljer **Skapa**.

      ![Skapa ett Azure Stream Analytics-jobb](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  Vi rekommenderar att du skapar en konsumentgrupp och en princip för varje nytt Azure Stream Analytics-jobb som du skapar från sidan Event Hubs. Konsumentgrupper tillåter endast fem samtidiga läsare, så att tillhandahålla en särskild konsumentgrupp för varje jobb kommer att undvika eventuella fel som kan uppstå från att överskrida denna gräns. Med en dedikerad princip kan du rotera nyckeln eller återkalla behörigheter utan att påverka andra resurser. 
1. Ditt Stream Analytics-jobb skapas nu där frågan är densamma som du har testat, och indata är din händelsehubb. 

9.  Om du vill slutföra pipelinen anger du **utdata för** frågan och väljer **Start** för att starta jobbet.

    > [!NOTE]
    > Innan du påbörjar jobbet, glöm inte att ersätta outputalias med utdatanamnet som du skapade i Azure Stream Analytics.

      ![Ange utdata och starta jobbet](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Kända begränsningar
När du testar frågan tar testresultaten cirka 6 sekunder att läsa in. Vi arbetar på att förbättra testresultaten. Men när azure Stream Analytics distribueras i produktion har de underordnad svarstid.

## <a name="streaming-units"></a>Strömningsenheter
Ditt Azure Stream Analytics-jobb är standard tre strömningsenheter (SUs). Om du vill justera den här inställningen väljer du **Skala** på den vänstra menyn på **jobbsidan Stream Analytics** i Azure-portalen. Mer information om strömningsenheter finns i [Förstå och justera strömningsenheter](../stream-analytics/stream-analytics-streaming-unit-consumption.md).

![Skala strömningsenheter](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>Nästa steg
Mer information om Stream Analytics-frågor finns i [Fråga språk för Stream Analytics](/stream-analytics-query/built-in-functions-azure-stream-analytics)
