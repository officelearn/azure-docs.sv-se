---
title: Power BI-instrumentpanelen på Azure Stream Analytics | Microsoft Docs
description: Använda en realtid strömmande Power BI-instrumentpanel för att samla in business intelligence och analysera stora volymer data från en Stream Analytics-jobbet.
keywords: instrumentpanelen, realtid instrumentpanelen
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: fe8db732-4397-4e58-9313-fec9537aa2ad
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/27/2017
ms.author: jeanb
ms.openlocfilehash: e1f1d960c312362e0e0cd6d2f83599c28c8c3f05
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Strömma analyser och Power BI: en analys i realtid instrumentpanel för strömmande data
Azure Stream Analytics gör att du kan dra nytta av en av de inledande verktyg för business intelligence [Microsoft Power BI](https://powerbi.com/). I den här artikeln får du lära dig hur skapa business intelligence-verktyg med hjälp av Power BI som utdata för Azure Stream Analytics-jobb. Du också lära dig hur du skapar och använder en realtid instrumentpanel.

Den här artikeln fortsätter från Stream Analytics [att upptäcka bedrägerier realtid](stream-analytics-real-time-fraud-detection.md) kursen. Den bygger på arbetsflödet som skapats i denna självstudiekurs och lägger till en Power BI utdata så att du kan visualisera bedrägliga telefonsamtal som identifieras av en Streaming Analytics-jobbet. 

Du kan titta på [en video](https://www.youtube.com/watch?v=SGUpT-a99MA) som visar det här scenariot.


## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du kontrollera att du har följande:

* Ett Azure-konto.
* Ett konto för Power BI. Du kan använda ett arbetskonto eller ett skolkonto.
* En fullständig version av den [att upptäcka bedrägerier realtid](stream-analytics-real-time-fraud-detection.md) kursen. Vägledningen innehåller en app som genererar fiktiva telefonsamtal metadata. I självstudiekursen skapar en händelsehubb och skicka informationen strömmande telefonsamtal till händelsehubben. Du kan skriva en fråga som identifierar bedrägliga anrop (anrop från samma nummer samtidigt på olika platser). 


## <a name="add-power-bi-output"></a>Lägga till Power BI-utdata
I realtid bedrägeri identifiering självstudierna skickas utdata till Azure Blob storage. I det här avsnittet kan du lägga till utdata som skickar information till Power BI.

1. Öppna Streaming Analytics-jobbet som du skapade tidigare i Azure-portalen. Om du har använt namnet som föreslås jobbet heter `sa_frauddetection_job_demo`.

2. Välj den **utdata** rutan i mitten på instrumentpanelen för projektet och välj sedan **+ Lägg till**.

3. För **Utdataalias**, ange `CallStream-PowerBI`. Du kan använda ett annat namn. Om du vill anteckna, eftersom du måste ha namnet senare. 

4. Under **Sink**väljer **Power BI**.

   ![Skapa ett utgående för Power BI](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut.png)

5. Klicka på **auktorisera**.

    En öppnas där du kan ange dina Azure-autentiseringsuppgifter för ett konto för arbetet eller skolan. 

    ![Ange autentiseringsuppgifter för åtkomst till Power BI](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

6. Ange dina autentiseringsuppgifter. Tänk sedan när du anger dina autentiseringsuppgifter också ger behörighet till Streaming Analytics-jobbet för åtkomst till Power BI-område.

7. När du kommer tillbaka till den **nya utdata** bladet anger du följande information:

    * **Gruppera arbetsytan**: Välj en arbetsyta i Power BI-klienten där du vill skapa datauppsättningen.
    * **DataSet-namnet**: Ange `sa-dataset`. Du kan använda ett annat namn. Om du vill anteckna den för senare.
    * **Tabellnamn**: Ange `fraudulent-calls`. Power BI-utdata från Stream Analytics-jobb kan för närvarande har endast en tabell i en dataset.

    ![PBI-arbetsytan](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut-with-dataset-table.png)

    > [!WARNING]
    > Om Power BI har en datauppsättning och en tabell som har samma namn som de som du anger i Stream Analytics-jobbet, skrivs befintliga över.
    > Vi rekommenderar att du inte uttryckligen skapar den här datauppsättningen och tabellen i Power BI-konto. De skapas automatiskt när du startar Stream Analytics-jobbet och jobbet startar pumpande utdata till Power BI. Om jobbet frågan inte returnerar något resultat, skapas inte i datamängden och tabell.
    >

8. Klicka på **Skapa**.

Datauppsättningen har skapats med följande inställningar:

* **defaultRetentionPolicy: BasicFIFO**: Data är FIFO, med högst 200 000 rader.
* **defaultMode: pushStreaming**: dataset stöder både strömmande paneler och traditionella rapport-baserade visuell information (kallas även push).

För närvarande kan skapa du inte datauppsättningar med andra flaggor.

Läs mer om Power BI-datauppsättningar den [Power BI REST API](https://msdn.microsoft.com/library/mt203562.aspx) referens.


## <a name="write-the-query"></a>Skriva frågan

1. Stäng den **utdata** bladet och gå sedan tillbaka till bladet för jobbet.

2. Klicka på den **frågan** rutan. 

3. Ange följande fråga. Den här frågan liknar självkoppling frågan som du skapade i kursen upptäckt av bedrägerier. Skillnaden är att den här frågan skickar resultaten till den nya utdata som du skapade (`CallStream-PowerBI`). 

    >[!NOTE]
    >Om du inte name indata `CallStream` i självstudierna upptäckt av bedrägerier ersätta ditt namn för `CallStream` i den **från** och **ansluta** satser i fråga.

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

4. Klicka på **Spara**.


## <a name="test-the-query"></a>Testa frågan
Det här avsnittet är valfritt men rekommenderas. 

1. Om appen TelcoStreaming inte körs startar du den genom att följa dessa steg:

    * Öppna ett kommandofönster.
    * Gå till mappen där telcogenerator.exe och ändrade telcodatagen.exe.config filer är.
    * Kör följande kommando:

            telcodatagen.exe 1000 .2 2

2. I den **frågan** bladet, klickar du på punkter bredvid den `CallStream` indata och välj sedan **exempeldata från indata**.

3. Du vill att tre minuter kan du se data och klicka på **OK**. Vänta tills du meddelas att data har samlats in.

4. Klicka på **Test** och se till att du får resultat.


## <a name="run-the-job"></a>Kör jobbet

1. Se till att TelcoStreaming appen körs.

2. Stäng den **frågan** bladet.

3. I jobb-bladet klickar du på **starta**.

    ![Starta Stream Analytics-jobbet](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

Streaming Analytics-jobbet startar söker efter bedrägliga anrop i den inkommande dataströmmen. Jobbet också dataset och tabell skapas i Power BI och börjar skicka data om falska anrop till dessa.


## <a name="create-the-dashboard-in-power-bi"></a>Skapa instrumentpanelen i Power BI

1. Gå till [Powerbi.com](https://powerbi.com) och logga in med ditt arbets- eller skolkonto. Om frågan Stream Analytics-jobbet överför resultaten, ser du att dina dataset redan har skapats:

    ![Strömmande datauppsättning i Power BI](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

2. I arbetsytan, klickar du på  **+ &nbsp;skapa**.

    ![Knappen Skapa i Power BI-arbetsyta](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Skapa en ny instrumentpanel och ger den namnet `Fraudulent Calls`.

    ![Skapa en instrumentpanel och ge det ett namn i Power BI-arbetsyta](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. Överst i fönstret klickar du på **Lägg till panelen**väljer **anpassade STRÖMMANDE DATA**, och klicka sedan på **nästa**.

    ![Anpassad strömning dataset](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. Under **din DATSETS**, Välj din datauppsättning och sedan på **nästa**.

    ![Din strömmande datauppsättning](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. Under **visualiseringen typen**väljer **kort**, och klicka sedan på den **fält** väljer **fraudulentcalls**.

    ![Visualiseringen information för den nya panelen](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

7. Klicka på **Nästa**.

8. Fyll i panelen detaljer som en rubrik och underrubrik.

    ![Rubrik och underrubrik för den nya panelen](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Klicka på **Använd**.

    Nu har du en bedrägeri räknare!

    ![Bedrägeri räknare](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

8. Följ anvisningarna om du vill lägga till en panel (startar med steg 4). Den här tiden kan göra följande:

    * När du kommer till **visualiseringen typen**väljer **linjediagram**. 
    * Lägg till en axel och välj **windowend**. 
    * Lägg till ett värde och välj **fraudulentcalls**.
    * För **tidsfönstret att visa**, Välj de senaste 10 minuterna.

    ![Skapa panelen för linjediagram](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Klicka på **nästa**, lägga till en rubrik och underrubrik och på **tillämpa**.

    Power BI-instrumentpanelen nu får du två vyer av data om falska samtal som identifierats i strömmande data.

    ![Färdig Power BI-instrumentpanelen visar två paneler för bedrägliga anrop](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>Ta reda på med om Power BI

Den här kursen visar hur du skapar bara några typer av visualiseringar för en dataset. Med hjälp av Powerbi kan du skapa andra kunden business intelligence-verktyg för din organisation. Fler idéer finns i följande resurser:

* Ett annat exempel på en Power BI-instrumentpanel titta på [komma igång med Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) video.
* Mer information om hur du konfigurerar Streaming Analytics-jobbet utdata till Power BI och använda Power BI-grupper, granska den [Power BI](stream-analytics-define-outputs.md#power-bi) avsnitt i den [Stream Analytics matar ut](stream-analytics-define-outputs.md) artikel. 
* Information om hur du använder Power BI vanligtvis finns [instrumentpaneler i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).


## <a name="learn-about-limitations-and-best-practices"></a>Lär dig mer om metodtips och begränsningar
Power BI kan för närvarande anropas ungefär en gång per sekund. Strömmande visuell information stöder paket på 15 KB. Strömmande visuell information misslyckas utöver det kan (men push fortsätter att fungera). På grund av dessa begränsningar lämpar Power BI sig mest naturligt för fall där Azure Stream Analytics kan minska belastningen viktiga data. Vi rekommenderar en rullande fönster eller Hopping fönster för att säkerställa att data-push är som mest en push per sekund och att din fråga hamnar inom kraven genomflöde.

Du kan använda följande formel för att beräkna ett värde för att ge ditt fönster i sekunder:

![Equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Exempel:

* Du har 1 000 enheter som skickar data med en sekunds intervall.
* Du använder Power BI Pro SKU: N som har stöd för 1 000 000 rader per timme.
* Du vill publicera den genomsnittliga datamängd per enhet till Power BI.

Därför blir formeln:

![Equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

Du kan med den här konfigurationen för att ändra den ursprungliga frågan till följande:

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


### <a name="renew-authorization"></a>Förnya auktorisationen
Om lösenordet har ändrats sedan jobbet skapades eller senast autentiserad, måste du autentiseras Power BI-konto. Om Azure Multi-Factor Authentication har konfigurerats på din Azure Active Directory (Azure AD)-klient, måste du också förnya Power BI-auktorisering varannan vecka. Om du inte förnyar kan du se problem, till exempel brist på jobbutdata eller en `Authenticate user error` i loggarna för åtgärden.

Om ett jobb startar efter att token har upphört att gälla, uppstår ett fel och jobbet misslyckas. Stoppa jobb som körs för att lösa problemet och gå till Power BI-utdata. Om du vill undvika dataförlust, Välj den **förnya auktorisering** länka och starta sedan om jobbet från den **stoppats senast**.

När tillståndet har uppdaterats med Power BI, visas en grön avisering i auktorisering för att återspegla att problemet har lösts.

## <a name="get-help"></a>Få hjälp
För ytterligare hjälp försök vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics query language-referens](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API-referens](https://msdn.microsoft.com/library/azure/dn835031.aspx)
