---
title: "Hur du skriver frågor i Stream Analytics | Microsoft Docs"
description: "Skriva frågor i Stream Analytics och fråga efter data | Learning sökvägssegment."
keywords: "hur du skriver frågor, fråga efter data, skriva en fråga, skriva frågor"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 0e9cdadd-0ee0-4bee-b65b-4a06fb863c95
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 215b774c20d80a67b1cefa2634131bd44860c692
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-write-queries-in-stream-analytics"></a>Hur du skriver frågor i Stream Analytics
Skriva frågor för bearbetning av logiken i Azure Stream Analytics implementeras som en ”stående-fråga” som har definierats innan ett jobb startar och köras på data eftersom den når jobbet. Dataomvandling uttryckt i ett SQL-liknande frågespråk som i stort sett en delmängd av T-SQL med några läggs-tillägg som [fönsterhantering](https://msdn.microsoft.com/library/azure/dn835019.aspx) användas för att uttrycka temporal semantik.

## <a name="writing-queries"></a>Skriva frågor:
1. I din Stream Analytics-jobbet i Azure-portalen, klickar du på **frågan**.
   
    ![Välj fråga](./media/stream-analytics-write-queries/1-stream-analytics-write-queries.png)  
   
    I Azure-portalen klickar du på **frågan**.
   
    ![Välj Query Preview](./media/stream-analytics-write-queries/query-preview-portal.png)  
2. Nya jobb har en fråga mall för att komma igång. Frågemallen utför en ”” direktfråga som projekt alla fält från inkommande händelser i utdata.  
   
   * Om du har definierat minst ett indata och utdata för jobbet, kan du ersätta platshållaren ”[YourOutputAlias]” och ”[YourInputAlias]” fält med alias för indata och utdata som du vill använda först. Dessutom kan du fortfarande skapa och testa din fråga i den klassiska Azure-portalen utan att definiera indata och utdata för jobbet.
   * Om du vill utföra mer bearbetning än en enkel direkt kan du redigera frågedefinitionen. Om du vill komma igång med redigering av frågan, ta en titt på vissa vanlig fråga mönster fångas [här](stream-analytics-stream-analytics-query-patterns.md).  
   
   ![Fråga efter data fönster](./media/stream-analytics-write-queries/2-stream-analytics-write-queries.png)  

## <a name="to-validate-query-data-is-working"></a>Om du vill validera frågedata fungerar:
Du kan testa att frågan fungerar som förväntat genom att köra det i webbläsaren över en eller flera lokala JSON-filer som innehåller testdata. Det här inte starta jobbet eller ha alla fakturering konsekvenser.

> [!NOTE]
> För närvarande stöds webbläsarbaserad frågetestning inte i Azure-portalen.  
> 
> 

1. Kontrollera att det inte finns några fel i frågan (annars knappen Testa inaktiveras) och klicka sedan på knappen Testa.  
   
   ![Fråga efter data Test](./media/stream-analytics-write-queries/3-stream-analytics-write-queries.png)  
2. Du uppmanas att ange filer för varje indata som refereras i frågan. I det här exemplet lämnas mallen frågan som-är så att dialogrutan uppmanar för indata med namnet ”yourinputalias”.
   
   ![Testa fråga](./media/stream-analytics-write-queries/4-stream-analytics-write-queries.png)  
3. Bläddra till en testfil. Flera exempelfiler är tillgängliga på [github](https://github.com/Azure/azure-stream-analytics/tree/master/Sample Data) och du kan också hämta exempeldata från din egen dataströmmen indata via funktionen exempeldata på fliken indata.
   
   ![Frågan indata](./media/stream-analytics-write-queries/5-stream-analytics-write-queries.png)  
4. Frågan ska köras via testdata och resultaten längst ned på sidan frågan visas när du har stängt dialogrutan.
   
   ![Frågesammanfattning av](./media/stream-analytics-write-queries/6-stream-analytics-write-queries.png)  

## <a name="get-help"></a>Få hjälp
Om du behöver mer hjälp kan du besöka vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

