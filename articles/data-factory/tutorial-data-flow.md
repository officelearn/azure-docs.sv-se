---
title: Omvandla data med hjälp av ett mappningsdataflöde
description: Den här självstudien innehåller steg-för-steg-instruktioner för hur du använder Azure Data Factory för att omvandla data med mappningsdataflöde
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/07/2019
ms.openlocfilehash: e6ca8007a96cc63b51b4f79b69029cbf0799e71c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979187"
---
# <a name="transform-data-using-mapping-data-flows"></a>Omvandla data med hjälp av mappningsdataflöden

Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).

I den här självstudien använder du Azure Data Factory-användargränssnittet (UX) för att skapa en pipeline som kopierar och omvandlar data från en Gen2-källa (Azure Data Lake Storage) till en ADLS Gen2-mottagare med hjälp av mappningsdataflöde. Konfigurationsmönstret i den här självstudien kan utökas när data omvandlas med hjälp av mappningsdataflöde

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa en pipeline med en dataflödesaktivitet.
> * Skapa ett mappningsdataflöde med fyra omvandlingar.
> * Testkör pipelinen.
> * Övervaka en dataflödesaktivitet

## <a name="prerequisites"></a>Krav
* **Azure-prenumeration**. Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure-lagringskonto**. Du använder ADLS-lagring som *källa* och sink-datalager. *sink* Om du inte har ett lagringskonto finns det anvisningar om hur du skapar ett i [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md).

Filen som vi omvandlar i den här guiden är MoviesDB.csv, som finns [här](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Om du vill hämta filen från GitHub kopierar du innehållet till en textredigerare som du väljer för att spara lokalt som en CSV-fil. Information om hur du överför filen till ditt lagringskonto finns i [Ladda upp blobbar med Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Exemplen kommer att referera till en behållare med namnet "exempeldata".

## <a name="create-a-data-factory"></a>Skapa en datafabrik

I det här steget skapar du en datafabrik och öppnar Data Factory UX för att skapa en pipeline i datafabriken.

1. Öppna **Microsoft Edge** eller **Google Chrome**. För närvarande stöds datafabrikens användargränssnitt endast i microsoft edge- och Google Chrome-webbläsarna.
2. På den vänstra menyn väljer du **Skapa en resurs** > **Analytics** > **Data Factory:**

   ![Valet Data Factory i fönstret Nytt](./media/doc-common-process/new-azure-data-factory-menu.png)

3. I fönstret **Ny datafabrik**, under **Namn** anger du **ADFTutorialDataFactory**.

   Namnet på Azure-datafabriken måste vara *globalt unikt*. Ange ett annat namn för datafabriken om du får ett felmeddelande om namnvärdet. (till exempel ditt namnADFTutorialDataFactory). Se artikeln [Namnregler för Data Factory](naming-rules.md) för namnregler för Data Factory-artefakter.

     ![Ny datafabrik](./media/doc-common-process/name-not-available-error.png)
4. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.
5. Gör något av följande för **Resursgrupp**:

    a. Välj **Använd befintlig**och välj en befintlig resursgrupp i listrutan.

    b. Välj **Skapa ny**och ange namnet på en resursgrupp. 
         
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md). 
6. Under **Version** väljer du **V2**.
7. Under **Plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Datalager (till exempel Azure Storage och SQL Database) och beräkningar (till exempel Azure HDInsight) som används av datafabriken kan vara i andra regioner.
8. Välj **Skapa**.
9. När skapandet är klart visas meddelandet i Meddelandecenter. Välj **Gå till resurs** för att navigera till sidan Datafabrik.
10. Klicka på **Författare och övervakare** för att starta användargränssnittet för datafabriken på en separat flik.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Skapa en pipeline med en dataflödesaktivitet

I det här steget ska du skapa en pipeline som innehåller en dataflödesaktivitet.

1. På sidan **Nu sätter vi igång** väljer du **Skapa pipeline**.

   ![Skapa pipeline](./media/doc-common-process/get-started-page.png)

1. På fliken **Allmänt** för pipelinen anger du **TransformMovies** för **pipelinens namn.**
1. Skjut reglaget **Dataflödesfelsök** i fabriksfältet. Felsökningsläge möjliggör interaktiv testning av omvandlingslogik mot ett live Spark-kluster. Dataflödeskluster tar 5-7 minuter att värma upp och användare rekommenderas att aktivera felsökning först om de planerar att göra dataflödesutveckling. Mer information finns i [Felsökningsläge](concepts-data-flow-debug-mode.md).

    ![Aktivitet för dataflöde](media/tutorial-data-flow/dataflow1.png)
1. Expandera dragspelet Flytta **och omforma** i fönstret **Aktiviteter.** Dra och släpp **aktiviteten Dataflöde** från fönstret till pipeline-arbetsytan.

    ![Aktivitet för dataflöde](media/tutorial-data-flow/activity1.png)
1. I popup-programmet **Lägga till dataflöde** väljer du **Skapa nytt dataflöde** och namnger sedan dataflödet **TransformMovies**. Klicka på Slutför när du är klar.

    ![Aktivitet för dataflöde](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Skapa omvandlingslogik på dataflödesarbetsytan

När du har skapat dataflödet skickas du automatiskt till dataflödesarbetsytan. I det här steget ska du skapa ett dataflöde som tar moviesDB.csv i ADLS-lagring och sammanställer den genomsnittliga klassificeringen av komedier från 1910 till 2000. Du ska sedan skriva tillbaka den här filen till ADLS-lagringen.

1. Lägg till en källa på dataflödesarbetsytan genom att klicka på rutan **Lägg till källa.**

    ![Arbetsyta för dataflöde](media/tutorial-data-flow/dataflow2.png)
1. Namnge din källa **MoviesDB**. Klicka på **Nytt** för att skapa en ny källdatauppsättning.

    ![Arbetsyta för dataflöde](media/tutorial-data-flow/dataflow3.png)
1. Välj **Azure Data Lake Storage Gen2**. Klicka på Fortsätt.

    ![Datauppsättning](media/tutorial-data-flow/dataset1.png)
1. Välj **AvgränsadText**. Klicka på Fortsätt.

    ![Datauppsättning](media/tutorial-data-flow/dataset2.png)
1. Namnge din datauppsättning **MoviesDB**. Välj **Nytt**i listrutan för länkad tjänst .

    ![Datauppsättning](media/tutorial-data-flow/dataset3.png)
1. På skärmen för att skapa länkade tjänster namnger du adls gen2-länkade tjänsten **ADLSGen2** och anger din autentiseringsmetod. Ange sedan dina anslutningsuppgifter. I den här självstudien använder vi kontonyckel för att ansluta till vårt lagringskonto. Du kan klicka på **Testa anslutning** för att kontrollera att dina autentiseringsuppgifter har angetts korrekt. Klicka på Skapa när du är klar.

    ![Länkad tjänst](media/tutorial-data-flow/ls1.png)
1. När du är tillbaka på skärmen för att skapa datauppsättning anger du var filen finns under fältet **Filsökväg.** I den här självstudien finns filen moviesDB.csv i exempeldata för behållaren. När filen har rubriker markerar du **Första raden som rubrik**. Välj **Från anslutning/butik** om du vill importera rubrikschemat direkt från filen i lagring. Klicka på OK när du är klar.

    ![Datauppsättningar](media/tutorial-data-flow/dataset4.png)
1. Om felsökningsklustret har startat går du till fliken **Förhandsgranskning** av data i källomvandlingen och klickar på **Uppdatera** för att hämta en ögonblicksbild av data. Du kan använda förhandsgranskning av data för att kontrollera att omvandlingen är korrekt konfigurerad.

    ![Arbetsyta för dataflöde](media/tutorial-data-flow/dataflow4.png)
1. Bredvid källnoden på dataflödesarbetsytan klickar du på plusikonen för att lägga till en ny omvandling. Den första omvandlingen du lägger till är ett **filter**.

    ![Arbetsyta för dataflöde](media/tutorial-data-flow/dataflow5.png)
1. Namnge filteromvandlingen **FilterYears**. Klicka på uttrycksrutan **bredvid Filtrera på** för att öppna uttrycksverktyget. Här anger du filtreringsvillkoret.

    ![Filter](media/tutorial-data-flow/filter1.png)
1. Med dataflödesuttrycksverktyget kan du interaktivt skapa uttryck som du kan använda i olika omformningar. Uttryck kan innehålla inbyggda funktioner, kolumner från indataschemat och användardefinierade parametrar. Mer information om hur du skapar uttryck finns i [Data Flow expression builder](concepts-data-flow-expression-builder.md).

    I den här guiden vill du filtrera filmer av genre komedi som kom ut mellan åren 1910 och 2000. Eftersom år är för närvarande en sträng måste du ```toInteger()``` konvertera den till ett heltal med hjälp av funktionen. Använd operatorerna större än eller lika med (>=) och mindre än eller lika med (<=) för att jämföra med bokstavliga årsvärden 1910 och 200-. dessa uttryck tillsammans med aktören och (&&). Uttrycket kommer ut som:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    För att hitta vilka filmer som är ```rlike()``` komedier, kan du använda funktionen för att hitta mönster "Comedy" i kolumnen genrer. Union rlike uttryck med året jämförelse för att få:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    Om du har ett felsökningskluster aktivt kan du verifiera logiken genom att klicka på **Uppdatera** för att se uttrycksutdata jämfört med de indata som används. Det finns mer än ett rätt svar på hur du kan utföra den här logiken med hjälp av dataflödesuttrycksspråket.

    ![Filter](media/tutorial-data-flow/filter2.png)

    Klicka på **Spara och slutför** när du är klar med uttrycket.

1. Hämta en **förhandsgranskning av data** för att kontrollera att filtret fungerar som det ska.

    ![Filter](media/tutorial-data-flow/filter3.png)
1. Nästa omvandling som du lägger till är en **aggregerad** omvandling under **Schemamodifierare**.

    ![Aggregera](media/tutorial-data-flow/agg1.png)
1. Namnge din sammanlagda omvandling **AggregateComedyRatings**. På fliken **Gruppera efter** väljer du **år** i listrutan för att gruppera aggregeringarna efter det år då filmen kom ut.

    ![Aggregera](media/tutorial-data-flow/agg2.png)
1. Gå till fliken **Aggregat.** I den vänstra textrutan namnger du den sammanlagda kolumnen **AverageComedyRating**. Klicka på rutan för rätt uttryck för att ange det sammanlagda uttrycket via uttrycksverktyget.

    ![Aggregera](media/tutorial-data-flow/agg3.png)
1. Om du vill hämta medelvärdet ```avg()``` för **kolumnklassificering**använder du den mängdba funktionen. Eftersom **Betyg** är ```avg()``` en sträng och tar in en numerisk ingång ```toInteger()``` måste vi konvertera värdet till ett tal via funktionen. Det här är uttrycket ser ut:

    ```avg(toInteger(Rating))```

    Klicka på **Spara och slutför** när du är klar.

    ![Aggregera](media/tutorial-data-flow/agg4.png)
1. Gå till fliken **Förhandsgranskning av data** om du vill visa omvandlingsutdata. Observera att endast två kolumner finns där, **år** och **AverageComedyRating**.

    ![Aggregera](media/tutorial-data-flow/agg3.png)
1. Därefter vill du lägga till en **sink-omvandling** under **Mål**.

    ![Kanalmottagare](media/tutorial-data-flow/sink1.png)
1. Namnge din **diskbänk Sink**. Klicka på **Ny** om du vill skapa sink-datauppsättningen.

    ![Kanalmottagare](media/tutorial-data-flow/sink2.png)
1. Välj **Azure Data Lake Storage Gen2**. Klicka på Fortsätt.

    ![Datauppsättning](media/tutorial-data-flow/dataset1.png)
1. Välj **AvgränsadText**. Klicka på Fortsätt.

    ![Datauppsättning](media/tutorial-data-flow/dataset2.png)
1. Namnge din sink-datauppsättning **MoviesSink**. För länkad tjänst väljer du den ADLS gen2-länkade tjänst som du skapade i steg 6. Ange en utdatamapp som du vill skriva data till. I den här självstudien skriver vi till mappen "output" i behållaren "sample-data". Mappen behöver inte finnas i förväg och kan skapas dynamiskt. Ange **första raden som rubrik** som true och välj **Ingen** för **importschema**. Klicka på Slutför.

    ![Kanalmottagare](media/tutorial-data-flow/sink3.png)

Nu har du byggt klart ditt dataflöde. Du är redo att köra den i din pipeline.

## <a name="running-and-monitoring-the-data-flow"></a>Köra och övervaka dataflödet

Du kan felsöka en pipeline innan du publicerar den. I det här steget ska du utlösa en felsökningskörning av dataflödespipelinen. Dataförhandsgranskningen skriver inte data, men en felsökningskörning skriver data till diskbänksmålet.

1. Gå till pipeline-arbetsytan. Klicka på **Felsök** för att utlösa en felsökningskörning.

    ![Pipeline](media/tutorial-data-flow/pipeline1.png)
1. Pipeline-felsökning av dataflödesaktiviteter använder det aktiva felsökningsklustret men tar ändå minst en minut att initiera. Du kan spåra förloppet via fliken **Utdata.** När körningen har lyckats klickar du på glasögonikonen för att öppna övervakningsfönstret.

    ![Pipeline](media/tutorial-data-flow/pipeline2.png)
1. I övervakningsfönstret kan du se antalet rader och tid som tillbringats i varje omvandlingssteg.

    ![Övervakning](media/tutorial-data-flow/pipeline3.png)
1. Klicka på en omvandling för att få detaljerad information om kolumnerna och partitionering av data.

    ![Övervakning](media/tutorial-data-flow/pipeline4.png)

Om du följde den här självstudien korrekt, bör du ha skrivit 83 rader och 2 kolumner i din diskbänk mapp. Du kan kontrollera att data är korrekta genom att kontrollera din blob-lagring.

## <a name="next-steps"></a>Nästa steg

Pipelinen i den här självstudien kör ett dataflöde som sammanställer den genomsnittliga klassificeringen av komedier från 1910 till 2000 och skriver data till ADLS. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa en pipeline med en dataflödesaktivitet.
> * Skapa ett mappningsdataflöde med fyra omvandlingar.
> * Testkör pipelinen.
> * Övervaka en dataflödesaktivitet

Läs mer om [dataflödesuttrycksspråket](data-flow-expression-functions.md).
