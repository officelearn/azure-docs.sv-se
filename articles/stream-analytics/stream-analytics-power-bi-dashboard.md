---
title: Power BI-instrumentpanelsintegrering med Azure Stream Analytics
description: I den här artikeln beskrivs hur du använder en Power BI-instrumentpanel i realtid för att visualisera data från ett Azure Stream Analytics-jobb.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2019
ms.openlocfilehash: 8466fbcb4325dc244551a3b84fc20581366b7071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851147"
---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics och Power BI: En instrumentpanel för analys i realtid för direktuppspelning av data

Med Azure Stream Analytics kan du dra nytta av ett av de ledande business intelligence-verktygen, [Microsoft Power BI](https://powerbi.com/). I den här artikeln får du lära dig hur du skapar business intelligence-verktyg med power bi som utdata för dina Azure Stream Analytics-jobb. Du lär dig också hur du skapar och använder en instrumentpanel i realtid.

Den här artikeln fortsätter från granskningsstudien för [bedrägeriidentifiering](stream-analytics-real-time-fraud-detection.md) i realtid. Den bygger på arbetsflödet som skapas i den självstudien och lägger till en Power BI-utdata så att du kan visualisera bedrägliga telefonsamtal som identifieras av ett Streaming Analytics-jobb. 

Du kan titta på [en video](https://www.youtube.com/watch?v=SGUpT-a99MA) som illustrerar det här scenariot.


## <a name="prerequisites"></a>Krav

Se till att du har följande innan du börjar:

* Ett Azure-konto.
* Ett konto för Power BI Pro. Du kan använda ett arbetskonto eller ett skolkonto.
* En slutförd version av realtid [bedrägeri upptäckt](stream-analytics-real-time-fraud-detection.md) handledning. Självstudien innehåller en app som genererar fiktiva telefonsamtalmetadata. I självstudien skapar du en händelsehubb och skickar strömmande telefonsamtalsdata till händelsehubben. Du skriver en fråga som identifierar bedrägliga samtal (samtal från samma nummer samtidigt på olika platser). 


## <a name="add-power-bi-output"></a>Lägg till utmatning av utmatning av utmatning
I självstudien för bedrägeriidentifiering i realtid skickas utdata till Azure Blob-lagring. I det här avsnittet lägger du till ett utdata som skickar information till Power BI.

1. Öppna jobbet Stream Analytics som du skapade tidigare i Azure-portalen. Om du använde det föreslagna namnet `sa_frauddetection_job_demo`heter jobbet .

2. På den vänstra menyn väljer du **Utdata** under **Jobbtopologi**. Välj sedan **+ Lägg till** och välj Power **BI** på rullgardinsmenyn.

3. Välj **+ Lägg till** > **Power BI**. Fyll sedan i formuläret med följande information och välj **Auktorisera**:

   |**Inställning**  |**Föreslaget värde**  |
   |---------|---------|
   |Utdataalias  |  CallStream-PowerBI  |
   |Namn på datauppsättning  |   sa-datauppsättning  |
   |Tabellnamn |  bedrägliga samtal  |

   ![Konfigurera Stream Analytics-utdata](media/stream-analytics-power-bi-dashboard/configure-stream-analytics-output.png)

   > [!WARNING]
   > Om Power BI har en datauppsättning och tabell som har samma namn som de som du anger i Stream Analytics-jobbet skrivs de befintliga över.
   > Vi rekommenderar att du inte uttryckligen skapar den här datauppsättningen och tabellen i ditt Power BI-konto. De skapas automatiskt när du startar stream analytics-jobbet och jobbet börjar pumpa utdata till Power BI. Om jobbfrågan inte returnerar några resultat skapas inte datauppsättningen och tabellen.
   >

4. När du har valt **Auktorisera** visas ett popup-fönster och du ombeds ange autentiseringsuppgifter för att autentisera ditt Power BI-konto. När auktoriseringen är klar ska du **Spara** inställningarna.

8. Klicka på **Skapa**.

Datauppsättningen skapas med följande inställningar:

* **defaultRetentionPolicy: BasicFIFO** - Data är FIFO, med högst 200 000 rader.
* **defaultMode: pushStreaming** - Datauppsättningen stöder både strömmande paneler och traditionella rapportbaserade visuella objekt (kallas även push).

För närvarande kan du inte skapa datauppsättningar med andra flaggor.

Mer information om Power BI-datauppsättningar finns i Power BI REST API-referensen. [Power BI REST API](https://msdn.microsoft.com/library/mt203562.aspx)


## <a name="write-the-query"></a>Skriv frågan

1. Stäng **utgångsbladet** och återgå till jobbbladet.

2. Klicka på rutan **Fråga.** 

3. Ange följande fråga. Den här frågan liknar den självkopplingsfråga som du skapade i självstudiekursen för bedrägeriidentifiering. Skillnaden är att den här frågan skickar resultat`CallStream-PowerBI`till de nya utdata som du skapade ( ). 

    >[!NOTE]
    >Om du inte namngav `CallStream` indata i självstudien för `CallStream` bedrägeriidentifiering ersätter du ditt namn i **FRÅN-** och **JOIN-satserna** i frågan.

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

Det här avsnittet är valfritt, men rekommenderas. 

1. Om TelcoStreaming-appen inte körs för tillfället startar du den med följande steg:

    * Öppna Kommandotolken.
    * Gå till mappen där filen telcogenerator.exe och de ändrade telcodatagen.exe.config-filerna finns.
    * Kör följande kommando:

       `telcodatagen.exe 1000 .2 2`

2. På sidan **Fråga** för ditt Stream Analytics-jobb klickar `CallStream` du på punkterna bredvid indata och väljer sedan **Exempeldata från indata**.

3. Ange att du vill ha tre minuters data och klicka på **OK**. Vänta tills du får ett meddelande om att data har samplats.

4. Klicka på **Testa** och granska resultaten.

## <a name="run-the-job"></a>Kör jobbet

1. Kontrollera att TelcoStreaming-appen körs.

2. Navigera till **sidan Översikt** för ditt Stream Analytics-jobb och välj **Start**.

    ![Starta Stream Analytics-jobbet](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

Ditt Streaming Analytics-jobb börjar leta efter bedrägliga samtal i den inkommande strömmen. Jobbet skapar också datauppsättningen och tabellen i Power BI och börjar skicka data om bedrägliga samtal till dem.


## <a name="create-the-dashboard-in-power-bi"></a>Skapa instrumentpanelen i Power BI

1. Gå till [Powerbi.com](https://powerbi.com) och logga in med ditt arbets- eller skolkonto. Om jobbfrågan för Stream Analytics ger resultat visas att datauppsättningen redan har skapats:

    ![Plats för direktuppspelning av datauppsättning i Power BI](./media/stream-analytics-power-bi-dashboard/stream-analytics-streaming-dataset.png)

2. Klicka på ** + &nbsp;Skapa**på arbetsytan.

    ![Knappen Skapa i Power BI-arbetsytan](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Skapa en ny instrumentpanel och namnge den `Fraudulent Calls`.

    ![Skapa en instrumentpanel och ge den ett namn på Power BI-arbetsytan](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. Högst upp i fönstret klickar du på **Lägg till panel,** väljer **ANPASSADE STRÖMMANDE DATA**och klickar sedan på **Nästa**.

    ![Anpassad panel för direktuppspelning av datauppsättning i Power BI](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. Under **DIN DATSETS**väljer du din datauppsättning och klickar sedan på **Nästa**.

    ![Din strömmande datauppsättning i Power BI](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. Under **Visualiseringstyp**väljer du **Kort**och väljer sedan **bedrägliga samtal**i listan **Fält** .

    ![Visualiseringsinformation för ny panel](./media/stream-analytics-power-bi-dashboard/add-fraudulent-calls-tile.png)

7. Klicka på **Nästa**.

8. Fyll i paneldetaljer som en titel och underrubrik.

    ![Titel och underrubrik för ny panel](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Klicka på **Använd**.

    Nu har du en bedrägeriräknare!

    ![Bedrägeriräknare i Power BI-instrumentpanelen](./media/stream-analytics-power-bi-dashboard/power-bi-fraud-counter-tile.png)

8. Följ stegen igen för att lägga till en panel (börjar med steg 4). Gör följande den här gången:

    * När du kommer till **Visualiseringstyp**väljer du **Linjediagram**. 
    * Lägg till en axel och välj **windowend**. 
    * Lägg till ett värde och välj **fraudulentcalls**.
    * För **Tidsfönster att visa** väljer du de senaste 10 minuterna.

      ![Skapa panel för linjediagram i Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Klicka på **Nästa,** lägg till en rubrik och underrubrik och klicka på **Använd**.

     Power BI-instrumentpanelen ger dig nu två vyer av data om bedrägliga samtal som upptäcks i strömmande data.

     ![Power BI-instrumentpanel som visar två paneler för bedrägliga samtal](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>Läs mer om Power BI

Den här självstudien visar hur du skapar bara några typer av visualiseringar för en datauppsättning. Power BI kan hjälpa dig att skapa andra verktyg för business intelligence för din organisation. Fler idéer finns i följande resurser:

* Ett annat exempel på en Power BI-instrumentpanel kan du titta på [komma igång med Power BI-video.](https://youtu.be/L-Z_6P56aas?t=1m58s)
* Mer information om hur du konfigurerar streaminganalysjobbutdata till Power BI och använder Power BI-grupper finns i [Power BI-avsnittet](stream-analytics-define-outputs.md#power-bi) i avsnittet [Stream Analytics-utdata.](stream-analytics-define-outputs.md) 
* Information om hur du använder Power BI finns i allmänhet [instrumentpaneler i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).


## <a name="learn-about-limitations-and-best-practices"></a>Läs mer om begränsningar och metodtips
För närvarande kan Power BI anropas ungefär en gång per sekund. Strömmande visuella objekt stöder paket på 15 kB. Utöver detta misslyckas strömmande visuella objekt (men push fortsätter att fungera). På grund av dessa begränsningar lämpar sig Power BI naturligt för fall där Azure Stream Analytics gör en betydande minskning av databelastningen. Vi rekommenderar att du använder ett Tumbling-fönster eller hoppningsfönster för att säkerställa att datafush är högst en push per sekund och att frågan hamnar inom dataflödeskraven.

Du kan använda följande ekvation för att beräkna värdet för att ge fönstret på några sekunder:

![Ekvation för att beräkna värde för att ge fönster i sekunder](./media/stream-analytics-power-bi-dashboard/compute-window-seconds-equation.png)  

Ett exempel:

* Du har 1 000 enheter som skickar data med en sekunds mellanrum.
* Du använder Power BI Pro SKU som stöder 1 000 000 rader per timme.
* Du vill publicera mängden genomsnittlig data per enhet till Power BI.

Som ett resultat blir ekvationen:

![Ekvation baserad på exempelvillkor](./media/stream-analytics-power-bi-dashboard/power-bi-example-equation.png)  

Med tanke på den här konfigurationen kan du ändra den ursprungliga frågan till följande:

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

### <a name="renew-authorization"></a>Förnya auktorisering
Om lösenordet har ändrats sedan jobbet skapades eller senast autentiserades måste du autentisera om ditt Power BI-konto. Om Azure Multi Factor Authentication är konfigurerat på din Azure Active Directory (Azure AD) -klient måste du också förnya Power BI-auktorisering varannan vecka. Om du inte förnyar kan du se symptom som brist `Authenticate user error` på jobbutdata eller en i åtgärdsloggarna.

På samma sätt, om ett jobb startar efter att token har upphört att gälla, uppstår ett fel och jobbet misslyckas. Lös problemet genom att stoppa jobbet som körs och gå till din Power BI-utdata. Om du vill undvika dataförlust väljer du länken **Förnya auktorisering** och startar sedan om jobbet från **den senaste stoppade tiden**.

När auktoriseringen har uppdaterats med Power BI visas en grön avisering i auktoriseringsområdet för att återspegla att problemet har lösts.

## <a name="get-help"></a>Få hjälp
Om du vill ha mer hjälp kan du prova vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Språkreferens för Azure Stream Analytics-frågor](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [AZURE Stream Analytics Management REST API-referens](https://msdn.microsoft.com/library/azure/dn835031.aspx)
