---
title: Transformera data med ett Azure Data Factory hanterade VNet-mappnings data flöde
description: Den här självstudien innehåller stegvisa instruktioner för hur du använder Azure Data Factory för att omvandla data med data flöde för mappning
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/19/2019
ms.openlocfilehash: 5515f6c4dfbc5d3c0e391373e763597d7fdc89ed
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532317"
---
# <a name="transform-data-securely-using-mapping-data-flows"></a>Transformera data på ett säkert sätt med hjälp av mappnings data flöden

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction).

I den här självstudien använder du gränssnittet i Azure Data Factory användar gränssnitt (UX) för att skapa en pipeline som kopierar och transformerar data **från en Azure Data Lake Storage-Gen2 (ADLS) till en ADLS Gen2 mottagare (båda tillåter endast åtkomst till valda nätverk)** med hjälp av data flödet i [Azure Data Factory hanterade Virtual Network](managed-virtual-network-private-endpoint.md). Konfigurations mönstret i den här självstudien kan utökas vid omvandling av data med hjälp av data flödet för mappning.

I den här självstudien gör du följande:

> [!div class="checklist"]
>
> * Skapa en datafabrik.
> * Skapa en pipeline med en data flödes aktivitet.
> * Skapa ett data flöde för mappning med fyra transformeringar.
> * Testkör pipelinen.
> * Övervaka en data flödes aktivitet

## <a name="prerequisites"></a>Förutsättningar
* **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage-konto**. Du använder ADLS-lagring som *käll* -och *mottagar* data lager. Om du inte har ett lagringskonto finns det anvisningar om hur du skapar ett i [Skapa ett Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal). **Se till att lagrings kontot endast tillåter åtkomst från valda nätverk.** 

Filen som vi transformerar i den här självstudien är MoviesDB.csv, som du hittar [här](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Om du vill hämta filen från GitHub kopierar du innehållet till en text redigerare som du väljer att spara lokalt som en CSV-fil. Om du vill överföra filen till ditt lagrings konto, se [Ladda upp blobar med Azure Portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Exemplen refererar till en behållare med namnet "Sample-data".

## <a name="create-a-data-factory"></a>Skapa en datafabrik

I det här steget skapar du en data fabrik och öppnar Data Factory UX för att skapa en pipeline i data fabriken.

1. Öppna **Microsoft Edge** eller **Google Chrome**. Data Factory-gränssnittet stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
2. På den vänstra menyn väljer du **skapa en resurs**  >  **analys**  >  **Data Factory**.
3. I fönstret **Ny datafabrik**, under **Namn** anger du **ADFTutorialDataFactory**.

   Namnet på Azure Data Factory måste vara *globalt unikt*. Ange ett annat namn för datafabriken om du får ett felmeddelande om namnvärdet. (till exempel Dittnamnadftutorialdatafactory). Se artikeln [Namnregler för Data Factory](naming-rules.md) för namnregler för Data Factory-artefakter.

4. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.
5. Gör något av följande för **Resursgrupp**:

    a. Välj **Använd befintlig**och välj en befintlig resurs grupp i den nedrullningsbara listan.

    b. Välj **Skapa ny**och ange namnet på en resurs grupp. 
         
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md). 
6. Under **Version** väljer du **V2**.
7. Under **Plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Data lager (till exempel Azure Storage och SQL Database) och beräkningarna (till exempel Azure HDInsight) som används av data fabriken kan finnas i andra regioner.

8. Välj **Skapa**.

9. När du har skapat meddelandet visas meddelandet i Notifications Center. Välj **gå till resurs** för att navigera till sidan data fabrik.
10. Klicka på **Författare och övervakare** för att starta användargränssnittet för datafabriken på en separat flik.

## <a name="create-an-azure-integration-runtime-in-adf-managed-virtual-network"></a>Skapa en Azure Integration Runtime i ADF-hanterade Virtual Network
I det här steget skapar du en Azure Integration Runtime och aktiverar hanterade Virtual Network.

1. I ADF-portalen går du till **Hantera hubb** och klickar på **nytt** för att skapa en ny Azure integration Runtime.
   ![Skapa nytt Azure Integration Runtime](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
2. Välj att skapa en **Azure** -integration Runtime.
   ![Ny Azure Integration Runtime](./media/tutorial-copy-data-portal-private/azure-ir.png)
3. Aktivera **Virtual Network**.
   ![Ny Azure Integration Runtime](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
4. Välj **Skapa**.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Skapa en pipeline med en data flödes aktivitet

I det här steget ska du skapa en pipeline som innehåller en data flödes aktivitet.

1. På sidan **Nu sätter vi igång** väljer du **Skapa pipeline**.

   ![Skapa pipeline](./media/doc-common-process/get-started-page.png)

1. I rutan **Egenskaper** för pipelinen anger du **TransformMovies** som **namn** på pipelinen.
1. I det fabriksinstallerade övre fältet drar du skjutreglaget för **data flödes fel sökning** på. Fel söknings läge möjliggör interaktiv testning av omvandlings logik mot ett aktivt Spark-kluster. Data flödes kluster tar 5-7 minuter att värma upp och användare rekommenderas att aktivera fel sökning först om de planerar att utföra data flödes utveckling. Mer information finns i [fel söknings läge](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-debug-mode).

    ![Fel sökning av data flöde](media/tutorial-data-flow-private/dataflow-debug.png)
1. I fönstret **aktiviteter** expanderar du **förflyttnings-och omvandlings** draget. Dra och släpp **data flödes** aktiviteten från fönstret till pipeline-arbetsytan.

1. I popup-fönstret för **att lägga till data flöde** väljer du **Skapa nytt data flöde** och väljer sedan **mappa data flöde**. Klicka på **OK** när du är färdig.

    ![Mappa dataflöden](media/tutorial-data-flow-private/mapping-dataflow.png)

1. Namnge ditt data flöde **TransformMovies** i rutan Egenskaper.

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Bygg omvandlings logik i data flödets arbets yta

När du har skapat ditt data flöde skickas det automatiskt till data flödets arbets yta. I det här steget ska du bygga ett data flöde som tar moviesDB.csv i ADLS-lagring och sammanställer genomsnitts betyget för Comedies från 1910 till 2000. Sedan skriver du filen tillbaka till ADLS-lagringen.

### <a name="add-the-source-transformation"></a>Lägg till käll omvandlingen

I det här steget konfigurerar du Azure Data Lake Storage Gen2 som källa.

1. I data flödets arbets yta lägger du till en källa genom att klicka på rutan **Lägg till källa** .

1. Namnge din käll **MoviesDB**. Klicka på **ny** för att skapa en ny käll data uppsättning.

1. Välj **Azure Data Lake Storage Gen2**. Klicka på Fortsätt.

1. Välj **DelimitedText**. Klicka på Fortsätt.

1. Namnge din data uppsättnings **MoviesDB**. I list rutan länkad tjänst väljer du **nytt**.

1. På skärmen Skapa länkad tjänst namnger du ADLS Gen2 länkade tjänst **ADLSGen2** och anger autentiseringsmetoden. Ange sedan dina autentiseringsuppgifter för anslutningen. I den här självstudien använder vi konto nyckeln för att ansluta till vårt lagrings konto. 

1. Se till att aktivera **interaktiv redigering**. Det kan ta cirka 1 minut att aktive ras.

    ![Interaktiv redigering](./media/tutorial-data-flow-private/interactive-authoring.png)

1. Välj **test anslutning**. det fungerar inte eftersom lagrings kontot inte ger åtkomst till det utan att skapa och godkänna en privat slut punkt. I fel meddelandet bör du se en länk för att skapa en **privat slut punkt** som du kan följa för att skapa en hanterad privat slut punkt. *Ett alternativ är att gå direkt till fliken Hantera och följa instruktionerna i [det här avsnittet](#create-a-managed-private-endpoint) för att skapa en hanterad privat slut punkt*

1. Håll dialog rutan öppen och gå sedan till ditt lagrings konto som valts ovan.

1. Följ anvisningarna i [det här avsnittet](#approval-of-a-private-link-in-storage-account) om du vill godkänna den privata länken.

1. Gå tillbaka till dialog rutan. **Testa anslutningen** igen och välj **skapa** för att distribuera den länkade tjänsten.

1. När du är tillbaka på skärmen skapa data uppsättning anger du var filen finns under fältet **fil Sök väg** . I den här självstudien finns filen moviesDB.csv i container exempel-data. När filen har rubriker kontrollerar du **första raden som rubrik**. Välj **från anslutning/Arkiv** om du vill importera huvud schemat direkt från filen i lagrings utrymmet. Klicka på OK när du är färdig.

    ![Källsökväg](media/tutorial-data-flow-private/source-file-path.png)

1. Om ditt fel söknings kluster har startats går du till fliken **data förhands granskning** i käll omvandlingen och klickar på **Uppdatera** för att hämta en ögonblicks bild av data. Du kan använda för hands versionen av data för att kontrol lera att din omvandling är korrekt konfigurerad.

    ![Förhandsgranskning](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>Skapa en hanterad privat slut punkt

Om du inte klickade på hyperlänken när du testar anslutningen ovan följer du följande sökväg. Nu måste du skapa en hanterad privat slut punkt som du ska ansluta till den länkade tjänsten som skapats ovan.

1. Gå till fliken Hantera.
> [!NOTE]
> Fliken Hantera är kanske inte tillgänglig för alla data Factory-instanser. Om du inte ser det kan du fortfarande komma åt privata slut punkter via fliken**Author**-->**Connections**-->**privata slut punkt**
1. Gå till avsnittet hanterade privata slut punkter.
1. Välj **+ ny** under hanterade privata slut punkter.

    ![Ny hanterad privat slut punkt](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. Välj panelen Azure Data Lake Storage Gen2 i listan och välj **Fortsätt**.
1. Ange namnet på det lagrings konto som du skapade ovan.
1. Välj **Skapa**.
1. Du bör se efter att ha väntat några sekunder på att den privata länken som skapas behöver ett godkännande.
1. Välj den privata slut punkt som du skapade ovan. Du kan se en hyperlänk som kommer att leda till att du godkänner den privata slut punkten på lagrings konto nivån.

    ![Hantera privat slut punkt](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-storage-account"></a>Godkännande av en privat länk i lagrings kontot

1. I lagrings kontot går du till **privata slut punkts anslutningar** under avsnittet Inställningar.

1. Kryssa för den privata slut punkt som du skapade ovan och välj **Godkänn**.

    ![Godkänn privat slut punkt](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. Lägg till en beskrivning och klicka på **Ja**.
1. Gå tillbaka till avsnittet **hanterade privata slut punkter** på fliken **Hantera** i Azure Data Factory.
1. Det bör ta cirka 1 minut att få godkännandet återspeglas för din privata slut punkt.

### <a name="add-the-filter-transformation"></a>Lägg till filter omvandlingen

1. Klicka på plus ikonen bredvid käll-noden på data flödets arbets yta för att lägga till en ny omvandling. Den första omvandlingen som du lägger till är ett **filter**.

    ![Lägg till filter](media/tutorial-data-flow-private/add-filter.png)
1. Namnge filter omvandlingen **FilterYears**. Klicka på uttrycks rutan bredvid **filtrera på** för att öppna uttrycks verktyget. Här anger du filtrerings villkoret.

    ![Filtrera år](media/tutorial-data-flow-private/filter-years.png)
1. Med uttrycks verktyget Data Flow kan du interaktivt skapa uttryck som ska användas i olika transformationer. Uttryck kan innehålla inbyggda funktioner, kolumner från schemat för indata och användardefinierade parametrar. Mer information om hur du skapar uttryck finns i [uttrycks verktyg för data flöde](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder).

    * I den här självstudien vill du filtrera filmer för Genre-komedi som kommer ut mellan åren 1910 och 2000. Om ett år är för närvarande en sträng måste du konvertera det till ett heltal med hjälp av ```toInteger()``` funktionen. Använd den större än eller lika med (>=) och mindre än eller lika med (<=) operatörer för att jämföra med de exakta värdena 1910 och 200-. Union dessa uttryck tillsammans med operatorn och (&&). Uttrycket visas som:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * Om du vill ta reda på vilka filmer som är Comedies kan du använda ```rlike()``` funktionen för att hitta mönstret "komedi" i kolumn genrerna. Union rlike-uttrycket med jämförelse året för att hämta:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * Om du har ett fel söknings kluster aktivt kan du verifiera din logik genom att klicka på **Uppdatera** för att se uttryckets utdata jämfört med de indata som används. Det finns mer än ett rätt svar på hur du kan utföra den här logiken med hjälp av Expression-språket för data flödet.

        ![Filter uttryck](media/tutorial-data-flow-private/filter-expression.png)

    * Klicka på **Spara och slutför** när du är klar med ditt uttryck.

1. Hämta en **data förhands granskning** för att kontrol lera att filtret fungerar korrekt.

    ![Filtrera data för hands version](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>Lägg till aggregerad transformering

1. Nästa omvandling du lägger till är en **sammanställd** omvandling under **schema modifieraren**.

    ![Lägg till mängd](media/tutorial-data-flow-private/add-aggregate.png)
1. Namnge den sammanställda transformeringen **AggregateComedyRatings**. På fliken **Gruppera efter** väljer du **år** i list rutan för att gruppera agg regeringar efter året som filmen kommer ut.

    ![Samlings grupp](media/tutorial-data-flow-private/group-by-year.png)
1. Gå till fliken **agg regeringar** . I den vänstra text rutan namnger du den sammanställda kolumnen **AverageComedyRating**. Klicka i rutan till höger uttryck för att ange det sammanställda uttrycket via uttrycks verktyget.

    ![Sammanställd kolumn namn](media/tutorial-data-flow-private/name-column.png)
1. Om du vill få medelvärdet för kolumn **klassificeringen**använder du ```avg()``` mängd funktionen. Eftersom **klassificering** är en sträng och ```avg()``` tar med numeriska värden måste vi konvertera värdet till ett tal via ```toInteger()``` funktionen. Detta är ett uttryck som ser ut så här:

    ```avg(toInteger(Rating))```

    Klicka på **Spara och slutför** när du är klar.

    ![Spara mängd](media/tutorial-data-flow-private/save-aggregate.png)
1. Gå till fliken **data förhands granskning** för att Visa transformationens utdata. Observera att endast två kolumner finns där, **år** och **AverageComedyRating**.

### <a name="add-the-sink-transformation"></a>Lägg till omvandlingen av mottagare

1. Sedan vill du lägga till en **Sink** -omvandling under **mål**.

    ![Lägg till mottagare](media/tutorial-data-flow-private/add-sink.png)
1. Namnge din Sink- **mottagare**. Klicka på **ny** för att skapa din data uppsättning för mottagare.

    ![Skapa mottagare](media/tutorial-data-flow-private/create-sink.png)
1. På sidan ny data uppsättning väljer du **Azure Data Lake Storage Gen2**. Klicka på Fortsätt.

1. På sidan Välj format väljer du **DelimitedText**. Klicka på Fortsätt.

1. Namnge din data uppsättning **MoviesSink**. För länkad tjänst väljer du samma ADLSGen2-länkade tjänst som du skapade för käll omvandling. Ange en mapp för utdata för att skriva dina data till. I den här självstudien ska vi skriva till mappen "output" i container ' Sample-data '. Mappen behöver inte finnas i förväg och kan skapas dynamiskt. Ange **första raden som rubrik** som sann och välj **ingen** för **import schema**. Klicka på OK.

    ![Mottagar Sök väg](media/tutorial-data-flow-private/sink-file-path.png)

Nu har du slutfört skapandet av ditt data flöde. Du är redo att köra den i din pipeline.

## <a name="running-and-monitoring-the-data-flow"></a>Köra och övervaka data flödet

Du kan felsöka en pipeline innan du publicerar den. I det här steget ska du utlösa en fel söknings körning av data flödets pipeline. Medan data förhands granskning skriver inte data, skriver en fel söknings körning data till mottagar målet.

1. Gå till pipeline-arbetsytan. Klicka på **Felsök** för att utlösa en debug-körning.

1. Fel sökning av data flödes aktiviteter i pipeline använder det aktiva fel söknings klustret men tar fortfarande minst en minut att initiera. Du kan följa förloppet via fliken **utdata** . När körningen är klar klickar du på glasögon-ikonen för att köra information.

1. På sidan information kan du se antalet rader och tid som lagts till i varje omformnings steg.

    ![Övervaknings körning](media/tutorial-data-flow-private/run-details.png)
1. Klicka på en omvandling för att få detaljerad information om kolumnerna och partitionering av data.

Om du har följt den här självstudien korrekt bör du ha skrivit 83 rader och 2 kolumner i mottagar mappen. Du kan kontrol lera att informationen är korrekt genom att kontrol lera Blob Storage.

## <a name="summary"></a>Sammanfattning

I den här självstudien använder du gränssnittet i Azure Data Factory användar gränssnitt (UX) för att skapa en pipeline som kopierar och transformerar data **från en Azure Data Lake Storage-Gen2 (ADLS) till en ADLS Gen2 mottagare (båda tillåter endast åtkomst till valda nätverk)** med hjälp av data flödet i [Azure Data Factory hanterade Virtual Network](managed-virtual-network-private-endpoint.md).
