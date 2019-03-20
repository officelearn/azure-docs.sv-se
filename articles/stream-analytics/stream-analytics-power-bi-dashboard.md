---
title: Power BI-instrumentpanel – integrering med Azure Stream Analytics
description: Den här artikeln beskriver hur du använder en realtidsinstrumentpanel i Power BI för att visualisera data från Azure Stream Analytics-jobb.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 487c142400dc2bfa6f44e17963535051af017196
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58116189"
---
# <a name="tutorial-stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Självstudier: Stream Analytics och Power BI: En instrumentpanel för analys i realtid för strömmande data
Azure Stream Analytics kan du dra nytta av en av de ledande business intelligence-verktyg, [Microsoft Power BI](https://powerbi.com/). I den här artikeln får du lära dig hur skapa business intelligence-verktyg med Power BI som utdata för dina Azure Stream Analytics-jobb. Du också lära dig hur du skapar och använder en realtidsinstrumentpanel.

Den här artikeln fortsätter från Stream Analytics [bedrägerier i realtid](stream-analytics-real-time-fraud-detection.md) självstudien. Den bygger på det arbetsflöde som skapas i självstudien och lägger till en Power BI som utdata så att du kan visualisera bedrägliga samtal som identifieras av ett Streaming Analytics-jobb. 

Du kan titta på [en video](https://www.youtube.com/watch?v=SGUpT-a99MA) som illustrerar det här scenariot.


## <a name="prerequisites"></a>Förutsättningar

Se till att du har följande innan du börjar:

* Ett Azure-konto.
* Ett konto för Power BI. Du kan använda ett arbetskonto eller ett skolkonto.
* En fullständig version av den [bedrägerier i realtid](stream-analytics-real-time-fraud-detection.md) självstudien. Självstudien innehåller en app som genererar fiktiva telefonsamtal metadata. I självstudien du skapar en event hub och skicka strömmande data för telefonsamtal till event hub. Du kan skriva en fråga som identifierar bedrägliga samtal (anrop från samma antal samtidigt på olika platser). 


## <a name="add-power-bi-output"></a>Lägg till Power BI-utdata
I självstudierna identifiering av bedrägerier i realtid skickas utdata till Azure Blob storage. I det här avsnittet ska du lägga till en utdata som skickar information till Power BI.

1. Öppna Streaming Analytics-jobb som du skapade tidigare i Azure-portalen. Om du har använt det föreslagna namnet jobbet med namnet `sa_frauddetection_job_demo`.

2. Välj den **utdata** rutan mitt jobb instrumentpanelen och välj sedan **+ Lägg till**.

3. För **Utdataaliaset**, ange `CallStream-PowerBI`. Du kan använda ett annat namn. Om du gör notera av det, eftersom du behöver namnet senare. 

4. Under **mottagare**väljer **Power BI**.

   ![Skapa utdata för Power BI](./media/stream-analytics-power-bi-dashboard/create-power-bi-ouptut.png)

5. Klicka på **auktorisera**.

    Ett fönster öppnas där du kan ange dina autentiseringsuppgifter för Azure för ett arbets- eller skolkonto konto. 

    ![Ange autentiseringsuppgifter för åtkomst till Power BI](./media/stream-analytics-power-bi-dashboard/power-bi-authorization-credentials.png)

6. Ange dina autentiseringsuppgifter. Var medveten om och sedan när du anger dina autentiseringsuppgifter kan du också håller behörighet för Streaming Analytics-jobbet för att komma åt ditt Power BI-område.

7. När du är tillbaka till den **nya utdata** bladet anger du följande information:

   * **Gruppen arbetsyta**: Välj en arbetsyta i Power BI-klient där du vill skapa datauppsättningen.
   * **Namn på datauppsättning**:  Ange `sa-dataset`. Du kan använda ett annat namn. Om du gör, notera den till senare.
   * **Tabellnamn**: Ange `fraudulent-calls`. Power BI-utdata från Stream Analytics-jobb kan för närvarande kan ha endast en tabell i en datauppsättning.

     ![Power BI-arbetsytan datauppsättning och tabell](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut-with-dataset-table.png)

     > [!WARNING]
     > Om Power BI har en datauppsättning och en tabell som har samma namn som de som du anger i Stream Analytics-jobb, skrivs befintliga.
     > Vi rekommenderar att du inte uttryckligen skapar datauppsättningen och tabellen i Power BI-kontot. De skapas automatiskt när du startar ditt Stream Analytics-jobb och jobbet startar pumpande utdata till Power BI. Om jobbet frågan inte returnerar några resultat, skapas inte datauppsättningen och tabellen.
     >

8. Klicka på **Skapa**.

Datauppsättningen skapas med följande inställningar:

* **defaultRetentionPolicy: BasicFIFO**: Data är FIFO, med högst 200 000 rader.
* **defaultMode: pushStreaming**: Datauppsättningen stöder både strömmande paneler och traditionella rapport-baserade visuella objekt (alias) push).

För närvarande kan skapa du inte datauppsättningar med andra flaggor.

Läs mer om Power BI-datauppsättningar, den [Power BI REST API](https://msdn.microsoft.com/library/mt203562.aspx) referens.


## <a name="write-the-query"></a>Skriva frågan

1. Stäng den **utdata** bladet och återgå till bladet jobb.

2. Klicka på den **fråga** box. 

3. Ange följande fråga. Den här frågan liknar självkoppling frågan som du skapade i självstudiekursen för identifiering av bedrägerier. Skillnaden är att den här frågan skickar resultaten till den nya utdata som du skapade (`CallStream-PowerBI`). 

    >[!NOTE]
    >Om du inte indata `CallStream` i självstudien identifiering av bedrägerier, ersätter du ditt namn för `CallStream` i den **FROM** och **ansluta** satser i fråga.

        ```SQL
        /* Our criteria for fraud:
        Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

        SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
        INTO "CallStream-PowerBI"
        FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
        JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime

        /* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
        ON CS1.CallingIMSI = CS2.CallingIMSI

        /* ...and date between CS1 and CS2 is between one and five seconds */
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

        /* Where the switch location is different */
        WHERE CS1.SwitchNum != CS2.SwitchNum
        GROUP BY TumblingWindow(Duration(second, 1))
        ```

4. Klicka på **Spara**.


## <a name="test-the-query"></a>Testa frågan
Det här avsnittet är valfritt men rekommenderas. 

1. Om appen TelcoStreaming inte körs, startar du den genom att följa dessa steg:

    * Öppna ett kommandofönster.
    * Gå till mappen där telcogenerator.exe och ändrade telcodatagen.exe.config filer är.
    * Kör följande kommando:

       `telcodatagen.exe 1000 .2 2`

2. I den **fråga** bladet klickar du på punkterna bredvid den `CallStream` indata och väljer sedan **exempeldata från indata**.

3. Ange att du vill att tre minuter tillhandahåller data och klicka på **OK**. Vänta tills du får ett meddelande om att data har samplats.

4. Klicka på **Test** och kontrollera att du får resultat.


## <a name="run-the-job"></a>Kör jobbet

1. Se till att appen TelcoStreaming körs.

2. Stäng den **fråga** bladet.

3. Jobb-bladet klickar du på **starta**.

    ![Starta Stream Analytics-jobbet](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

Streaming Analytics-jobbet startar söker efter bedrägliga samtal i den inkommande dataströmmen. Jobbet också skapar datauppsättningen och tabellen i Power BI och startar skickar data om bedrägliga anrop till dessa.


## <a name="create-the-dashboard-in-power-bi"></a>Skapa instrumentpanelen i Power BI

1. Gå till [Powerbi.com](https://powerbi.com) och logga in med ditt arbets- eller skolkonto. Om den Stream Analytics-jobbfrågan resultat, se du att din datauppsättning redan har skapat:

    ![Plats för strömmande datauppsättning i Power BI](./media/stream-analytics-power-bi-dashboard/stream-analytics-streaming-dataset.png)

2. På arbetsytan och klicka på  **+ &nbsp;skapa**.

    ![Knappen Skapa i Power BI-arbetsyta](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Skapa en ny instrumentpanel och ge den namnet `Fraudulent Calls`.

    ![Skapa en instrumentpanel och ge den ett namn i Power BI-arbetsyta](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. Längst upp i fönstret klickar du på **Lägg till panel**väljer **anpassade STRÖMMANDE DATA**, och klicka sedan på **nästa**.

    ![Anpassade strömmande datauppsättning panel i Power BI](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. Under **YOUR DATSETS**, Välj din datauppsättning och sedan på **nästa**.

    ![Din strömmande datauppsättning i Power BI](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. Under **Visualiseringstyp**väljer **kort**, och sedan i den **fält** väljer **fraudulentcalls**.

    ![Visualisering information för den nya panelen](./media/stream-analytics-power-bi-dashboard/add-fraudulent-calls-tile.png)

7. Klicka på **Nästa**.

8. Fyll i panelinformation som en rubrik och underrubrik.

    ![Rubrik och underrubrik för den nya panelen](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Klicka på **Verkställ**.

    Nu har du en räknare för bedrägeri!

    ![Bedrägeri räknare i Power BI-instrumentpanel](./media/stream-analytics-power-bi-dashboard/power-bi-fraud-counter-tile.png)

8. Följ stegen om du vill lägga till en panel (från och med steg 4). Den här tiden kan göra följande:

    * När du kommer till **Visualiseringstyp**väljer **linjediagram**. 
    * Lägg till en axel och välj **windowend**. 
    * Lägg till ett värde och välj **fraudulentcalls**.
    * För **Tidsfönster att visa** väljer du de senaste 10 minuterna.

      ![Skapa panelen för linjediagram i Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Klicka på **nästa**, lägga till en rubrik och underrubrik och på **tillämpa**.

     Power BI-instrumentpanel nu ger två vyer av data om bedrägliga samtal som identifierats i strömmande data.

     ![Klar med Power BI-instrumentpanel som visar två paneler efter bedrägliga samtal](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>Ta reda på med om Power BI

Den här självstudien visar hur du skapar bara några typer av visualiseringar för en datauppsättning. Med hjälp av Powerbi kan du skapa andra kunden business intelligence-verktyg för din organisation. Fler idéer finns i följande resurser:

* Titta på ett annat exempel på en Power BI-instrumentpanel i [komma igång med Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) video.
* Mer information om hur du konfigurerar Streaming Analytics-jobbutdata till Power BI och använder Power BI-grupper, granska de [Power BI](stream-analytics-define-outputs.md#power-bi) delen av den [Stream Analytics matar ut](stream-analytics-define-outputs.md) artikeln. 
* Information om hur du använder Power BI vanligtvis finns i [instrumentpaneler i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).


## <a name="learn-about-limitations-and-best-practices"></a>Lär dig mer om begränsningar och bästa praxis
Power BI kan för närvarande kan anropas ungefär en gång per sekund. Strömmande visuella information har stöd för paket på 15 KB. Utöver dessa, strömmande visuella information misslyckas (men push fortsätter att fungera). På grund av dessa begränsningar lämpar Power BI sig mest naturligt för fall där Azure Stream Analytics har en omfattande data belastning av. Vi rekommenderar en rullande fönster eller Hopping fönstret så att data-push är högst en push per sekund och att din fråga hamnar inom behov för dataflöde.

Du kan använda följande formel för att beräkna värdet för att ge fönstret i sekunder:

![Formel för att beräkna värdet för att ge fönster i sekunder](./media/stream-analytics-power-bi-dashboard/compute-window-seconds-equation.png)  

Exempel:

* Du har 1 000 enheter som skickar data med en sekund intervall.
* Du använder Power BI Pro SKU som stöder 1 000 000 rader per timme.
* Du vill publicera den genomsnittliga datamängd per enhet till Power BI.

Därför blir ekvationen:

![Formel som baseras på exempel kriterier](./media/stream-analytics-power-bi-dashboard/power-bi-example-equation.png)  

Med den här konfigurationen kan du ändra den ursprungliga frågan så här:

```SQL
    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO "CallStream-PowerBI"
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl
```

### <a name="renew-authorization"></a>Förnya auktorisationen
Om lösenordet har ändrats sedan jobbet skapades eller senast autentiserade, måste du autentiseras på nytt Power BI-kontot. Om Azure Multi-Factor Authentication har konfigurerats på din Azure Active Directory (Azure AD)-klient, måste du också förnya auktoriseringen för Power BI varannan vecka. Om du inte förnyar kan du få se symptom, till exempel brist på jobbutdata eller en `Authenticate user error` i loggarna för åtgärden.

Om ett jobb startar när token har upphört att gälla, uppstår ett fel och jobbet misslyckas. Stoppa jobb som körs för att lösa problemet och gå till Power BI-utdata. För att undvika dataförlust, Välj den **förnya auktoriseringen** länka och sedan starta om jobbet från den **senast stoppad**.

När auktoriseringen har uppdaterats med Power BI, visas en grön avisering i området auktorisering för att återspegla att problemet har lösts.

## <a name="get-help"></a>Få hjälp
För mer hjälp kan du prova vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Frågespråksreferens för Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API-referens](https://msdn.microsoft.com/library/azure/dn835031.aspx)
