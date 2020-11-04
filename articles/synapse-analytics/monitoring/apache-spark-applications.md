---
title: Övervaka Apache Spark program med Synapse Studio
description: Använd Synapse Studio för att övervaka dina Apache Spark-program.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 9f55d65b215c03769caa7c812dcda138f8e83595
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322465"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Använd Synapse Studio för att övervaka dina Apache Spark-program

Med Azure Synapse Analytics kan du använda Apache Spark för att köra antecknings böcker, jobb och andra typer av program på dina Apache Spark-pooler på din arbets yta.

I den här artikeln beskrivs hur du övervakar Apache Spark program, så att du kan hålla koll på den senaste statusen, problemen och förloppet.

Den här självstudien omfattar följande uppgifter:

* Övervakare som kör Apache Spark program
* Visa slutförda Apache Spark program
* Visa avbrutna Apache Spark program
* Det gick inte att felsöka Apache Spark programmet

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar med den här självstudien måste du uppfylla följande krav:

- En Synapse Studio-arbetsyta. Instruktioner finns i [skapa en Synapse Studio-arbetsyta](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace#create-a-workspace).

- En server lös Apache Spark pool.

## <a name="monitor-running-apache-spark-application"></a>Övervakare som kör Apache Spark program

Öppna **övervakaren** och välj **Apache Spark program**. Om du vill visa information om de Apache Spark-program som körs väljer du sändnings Apache Spark programmet och visar informationen. Om Apache Spark programmet fortfarande körs kan du övervaka förloppet.

  ![Välj pågående jobb](./media/how-to-monitor-spark-applications/select-running-job.png)

1. Kontrol lera **slutförda aktiviteter** , **status** och **total varaktighet**.

2. Avbryt Apache Spark programmet.

3. Uppdatera logg fråga.

4. Visa grafen.

5. Kontrol lera **sammanfattnings** informationen.

6. Kontrol lera **loggarna**. Logg informationen är tom vid körning.

    ![Visa jobb som körs](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-completed-apache-spark-application"></a>Visa slutförda Apache Spark program

Öppna **övervakaren** och välj **Apache Spark program**. Om du vill visa information om de slutförda Apache Spark-programmen väljer du Apache Spark programmet och visar informationen.

  ![Välj slutfört jobb](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. Kontrol lera **slutförda aktiviteter** , **status** och **total varaktighet**.

2. Uppdatera logg fråga.

3. Öppna Apache Spark historik Server länk genom att klicka på **Spark historik Server**.

4. Kontrol lera **sammanfattnings** informationen genom att klicka på ikonen i diagrammet.

5. Kontrol lera **loggarna**. Du kan välja olika typer av loggar i list rutan och du kan hämta logg informationen genom att klicka på **Hämta loggar**.

6. Du kan se en översikt över jobbet i diagrammet genererat jobb. Som standard visar diagrammet alla jobb. Du kan filtrera den här vyn efter **jobb-ID**.

7. Som standard är visnings **förloppet** markerat. Du kan kontrol lera data flödet genom att välja **läsa** eller **skriva** i list rutan **Visa** .

8. Om du vill spela upp jobbet väljer du **uppspelning**. Du kan välja **stoppa** när som helst för att stoppa.

9. Använd musen för att zooma in och ut i jobb diagrammet, eller Välj **Zooma för** att anpassas för att få plats på skärmen.

10. I noden jobb diagram visas följande information om varje steg:

    * Identitet.

    * Namn eller beskrivning.

    * Totalt aktivitets nummer.

    * Lästa data: summan av storleken på indata och den blandade Läs storleken.

    * Data skrivning: summan av storlek och blandnings storlek för utdata.

    * Körnings tid: tiden mellan start tiden för det första försöket och slut för ande tiden för det senaste försöket.

    * Radantal: summan av inmatnings poster, utgående poster, blandar Läs poster och blanda Skriv poster.

    * Pågår.

     ![Visa slutfört jobb](./media/how-to-monitor-spark-applications/view-completed-job.png)
    
11. Klicka på diagrammet så visas detaljerna för scenen.

   ![information för steg](./media/how-to-monitor-spark-applications/details-for-stage.png)

## <a name="view-canceled-apache-spark-application"></a>Visa avbrutna Apache Spark program

Öppna **övervakaren** och välj **Apache Spark program**. Om du vill visa information om de avbrutna Apache Spark-programmen väljer du Apache Spark programmet och visar informationen.

 ![Välj avbrutet jobb](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. Kontrol lera **slutförda aktiviteter** , **status** och **total varaktighet**.

2. Uppdatera logg frågan.

3. Öppna Apache historik Server länk genom att klicka på **Spark historik Server**.

4. Visa grafen.

5. Kontrol lera **sammanfattnings** informationen.

6. Kontrol lera **loggarna**. Du kan välja olika typer av loggar i list rutan och du kan hämta logg informationen genom att klicka på **Hämta loggar**.

   ![Visa avbrutna jobb](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>Det gick inte att felsöka Apache Spark programmet

Öppna **övervakaren** och välj **Apache Spark program**. Om du vill visa information om misslyckade Apache Spark-program väljer du Apache Spark programmet och visar informationen.

![Välj misslyckat jobb](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. Kontrol lera **slutförda aktiviteter** , **status** och **total varaktighet**.

2. Uppdatera logg fråga.

3. Öppna Apache Spark historik Server länk genom att klicka på **Spark historik Server**.

4. Visa grafen.

5. Kontrol lera **sammanfattnings** informationen.

6. Kontrol lera fel informationen.

   ![jobb information misslyckades](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="next-steps"></a>Nästa steg

Mer information om övervakning av pipeline-körningar finns i artikeln [övervaka pipeline som körs med hjälp av Synapse Studio](how-to-monitor-pipeline-runs.md) .  
