---
title: "Självstudie för cykeldelning – förberedelse av avancerade data med Azure Machine Learning-arbetsstationen"
description: "Och dataförberedelse av självstudier för slutpunkt till slutpunkt med hjälp av Azure Machine Learning-arbetsstationen"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial, azure
ms.topic: article
ms.date: 09/21/2017
ms.openlocfilehash: e4bcf7ec2a18f6068554c2eb85b72ffc36dcc4fc
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="bike-share-tutorial-advanced-data-preparation-with-azure-machine-learning-workbench"></a>Självstudie för cykeldelning: Förberedelse av avancerade data med Azure Machine Learning-arbetsstationen
Azure Machine Learning (förhandsversionen) är en integrerad, avancerad lösning för datavetenskap och analys som datatekniker kan använda för att förbereda data, utveckla experiment och distribuera modeller i molnskala.

I den här självstudien använder du Azure Machine Learning (förhandsversion) till att lära dig att:
> [!div class="checklist"]
> * Förbereda data interaktivt med förberedelseverktyget för Azure Machine Learning Data
> * Importera, transformera och skapa en test-datauppsättning
> * Skapa ett paket för förberedelse av data
> * Kör paketet för förberedelse av data med Python
> * Generera en datauppsättning för träning genom att återanvända paketet för förberedelse av data för ytterligare inkommande filer
> * Köra skript i ett lokalt Azure CLI-fönster.
> * Köra skript i en Azure HDInsight-molnmiljö.


## <a name="prerequisites"></a>Förutsättningar
* Azure Machine Learning-arbetsstationen måste vara installerad lokalt. Mer information finns i [snabbstart av installation](quickstart-installation.md).
* Om du inte har installerat Azure CLI, följ instruktionerna för att [installera den senaste versionen av Azure CLI]. (https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
* En [HDInsights Spark-kluster](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal) måste skapas i Azure.
* Ett Azure Storage-konto.
* Liknar processen med att skapa ett nytt projekt i arbetsstationen.
* Även om det inte krävs, är det bra att ha [Azure Lagringsutforskaren](https://azure.microsoft.com/features/storage-explorer/) installerat så att du kan ladda upp, hämta och visa blobbarna på ditt lagringskonto. 

## <a name="data-acquisition"></a>Datainsamling
Den här kursen använder data från [Boston Hubway-datauppsättning](https://s3.amazonaws.com/hubway-data/index.html) och Boston weather från [NOAA](http://www.noaa.gov/).

1. Hämta datafiler från följande länkar till din lokala utvecklingsmiljö. 
   * [Boston weather-data](https://azuremluxcdnprod001.blob.core.windows.net/docs/azureml/bikeshare/BostonWeather.csv). 
   * Hubway-resedata från Hubway-webbplatsen.

      - [201501-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201501-hubway-tripdata.zip)
      - [201504-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201504-hubway-tripdata.zip)
      - [201510-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201510-hubway-tripdata.zip)
      - [201601-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201601-hubway-tripdata.zip)
      - [201604-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201604-hubway-tripdata.zip)
      - [201610-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201610-hubway-tripdata.zip)
      - [201701-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201701-hubway-tripdata.zip)

2. Packa upp varje ZIP-fil efter nedladdning.

## <a name="upload-data-files-to-azure-blob-storage"></a>Överföra filer till Azure Blob storage
Du kan använda blob storage som värd för dina filer.

1. Använd samma Azure Storage-konto som används för HDInsight-klustret som du använder.

    ![hdinsightstorageaccount.png](media/tutorial-bikeshare-dataprep/hdinsightstorageaccount.png)

2. Skapa en ny behållare med namnet '**-datafiler**' att lagra BikeShare datafiler.

3. Ladda upp datafiler. Överför den `BostonWeather.csv` till en mapp med namnet `weather`, och datafiler för resa till en mapp med namnet `tripdata`.

    ![azurestoragedatafile.png](media/tutorial-bikeshare-dataprep/azurestoragedatafile.png)

> [!TIP]
> Du kan också använda **Azure Lagringsutforskaren** att överföra blobbar. Det här verktyget kan användas när du vill visa innehållet i någon av de filer som genereras under kursen samt.

## <a name="learn-about-the-datasets"></a>Lär dig mer om datauppsättningar
1. Filen __Boston weather__ innehåller följande väderrelaterade fält som rapporteras varje timme:
   * DATE
   * REPORTTPYE
   * HOURLYDRYBULBTEMPF
   * HOURLYRelativeHumidity
   * HOURLYWindSpeed

2. __Hubway__-data ordnas i filer per år och månad. Till exempel innehåller filen `201501-hubway-tripdata.zip` en CSV-fil som innehåller data för januari 2015. Data innehåller följande fält för varje rad som representerar en cykelresa:

   * Resans varaktighet (i sekunder)
   * Starttid och startdatum
   * Stopptid och stoppdatum
   * Startstationens namn och ID
   * Slutstationens namn och ID
   * Cykel-ID
   * Användartyp (Tillfällig = 24 timmars- eller 72-timmarsanvändare. Medlem = års- eller månadsmedlem)
   * Postnummer (om användaren är medlem)
   * Kön (självrapporterat av medlemmen)

## <a name="create-a-new-project"></a>Skapa ett nytt projekt
1. Starta **Azure Machine Learning-arbetsstationen** från startmenyn eller startprogrammet.

2. Skapa ett nytt Azure Machine Learning-projekt.  Klicka på knappen **+** på sidan **Projekt** eller **Fil** > **Ny**.
   - Använd den **cykeln filresurs** mall.
   - Namnge projektet **BikeShare**. 

## <a id="newdatasource"></a>Skapa en ny datakälla

1. Skapa en ny datakälla. Klicka på knappen **Data** (cylinderikonen) till vänster i verktygsfältet. Det här visar **Datavyn**.

   ![Bild av datavyfliken](media/tutorial-bikeshare-dataprep/navigatetodatatab.png)

2. Lägg till en datakälla. Välj ikonen **+** och välj sedan **Lägg till datakälla**.

   ![Bild av posten Lägg till datakälla](media/tutorial-bikeshare-dataprep/newdatasource.png)

## <a name="add-weather-data"></a>Lägg till väderdata

1. **Datalager**: Välj det datalager som innehåller data. Eftersom du använder filer, välj **Fil(er)/Katalog**. Välj **Nästa** för att fortsätta.

   ![Bild av posten för fil(er)/katalog](media/tutorial-bikeshare-dataprep/datasources.png)

2. **Filval**: Lägg till väderdata. Bläddra och välj den `BostonWeather.csv` -fil som du överfört till __Azure Blob Storage__ tidigare. Klicka på **Nästa**.

   ![Bild av filval med BostonWeater.csv](media/tutorial-bikeshare-dataprep/azureblobpickweatherdatafile.png)

3. **Filinformation**: Verifiera filschemat som upptäcks. Azure Machine Learning-arbetsstationen analyserar data i filen och härleder det schema som ska användas.

   ![Bild av filinformation](media/tutorial-bikeshare-dataprep/fileparameters.png)

   > [!IMPORTANT]
   > Arbetsstationen identifierar kanske inte korrekt schema i vissa fall. Du bör alltid kontrollera att parametrarna är korrekta för datauppsättningen. Kontrollera att de är inställda på följande värden för väderdata:
   >
   > * __Filtyp__: Avgränsad fil (csv, tvs, txt, etc.)
   > * __Avgränsare__: Kommatecken [,]
   > * __Kommentarradstecken__: Inget värde har angetts.
   > * __Läget Hoppa över rader__: Hoppa inte över
   > * __Filkodning__: utf-8
   > * __Uppgradera rubrikläge__: Använd rubriker från första filen

   Förhandsgranskning av data ska visa följande kolumner:
   * **Sökväg**
   * **DATE**
   * **REPORTTYPE**
   * **HOURLYDRYBULBTEMPF**
   * **HOURLYRelativeHumidity**
   * **HOURLYWindSpeed**

   Välj **Nästa** för att fortsätta.

4. **Datatyper**: Granska de datatyper som identifieras automatiskt. Azure Machine Learning-arbetsstationen analyserar data i filen och härleder de datatyper som ska användas.

   Ändra `DATA TYPE` för alla kolumner till `String` för dessa data.

   > [!NOTE]
   > `String` används för att markera funktionerna i arbetsstationen senare i den här kursen. 

   ![Bild av datatyperna](media/tutorial-bikeshare-dataprep/datatypedetection.png)

   Välj __Nästa__ för att fortsätta. 

5. **Provtagning**: Om du vill skapa ett schema för provtagning, Välj den **redigera** knappen. Välj den nya raden __Top 10000__ som har lagts till och markera sedan __Redigera__. Ange __Samplingsstrategi__ till **Fullständig fil**, och välj sedan **Tillämpa**.

   ![Bild för att lägga till en ny samplingsstrategi](media/tutorial-bikeshare-dataprep/weatherdatasamplingfullfile.png)

   För att använda strategin __Fullständig fil__, välj posten __Fullständig Fil__ och välj sedan __Ställ in som aktiv__. En stjärna visas bredvid __Fullständig fil__ som indikerar att detta är den aktiva strategin.

   ![Bild av Fullständig fil som aktiv strategi](media/tutorial-bikeshare-dataprep/fullfileactive.png)

   Välj **Nästa** för att fortsätta.

6. **Sökvägskolumn**: Med avsnittet __Sökvägskolumn__ kan du inkludera den fullständiga sökvägen som en kolumn i den importerade informationen. Välj __Inkludera inte i sökvägskolumnen__.

   > [!TIP]
   > Det är användbart att inkludera sökvägen som en kolumn om du importerar en mapp med många filer med olika namn. Det är också användbart om filnamnen innehåller information som du vill extrahera senare.

   ![Bild av inställd Sökvägskolumn som inte ska inkluderas](media/tutorial-bikeshare-dataprep/pathcolumn.png)

7. **Slutför**: För att slutföra skapandet av datakällan, välj knappen **Slutför**.

    En ny flik för datakälla med namnet __BostonWeather__ öppnas. Ett exempel på data visas i ett rutnät. Exemplet baseras på det aktiva samplingsschemat som angavs tidigare.

    Observera rutan **Steg** till höger på skärmen som visar de enskilda åtgärder som vidtas när du skapar den här datakällan.

   ![Bild som visar datakällan, exempel och steg](media/tutorial-bikeshare-dataprep/weatherdataloaded.png)

### <a name="view-data-source-metrics"></a>Visa mått för datakällan

Välj knappen __Mått__ längst upp till vänster på flikens rutnätsvy. Den här vyn visar fördelningen och annan samlad statistik över exempeldata.

![Bild av Mått visas](media/tutorial-bikeshare-dataprep/weathermetrics.png)

> [!NOTE]
> Om du vill konfigurera synligheten för statistik, använder du listrutan **Välj mått**. Markera och avmarkera mått om du vill ändra rutnätsvyn.

För att återgå till __Datavy__, välj __Data__ från det övre vänstra hörnet på sidan.

## <a name="add-data-source-to-data-preparation-package"></a>Lägg till datakälla till paket för förberedelse av data

1. Välj __Förbered__ för att börja förbereda informationen. 

2. När du uppmanas, anger du ett namn för förberedelsen av datapaketet, som t.ex. `BikeShare Data Prep`. 

3. Klicka på __OK__ för att fortsätta.

   ![Bild av dialogrutan Förbereda](media/tutorial-bikeshare-dataprep/dataprepdialog.png)

4. Ett nytt paket med namnet **BikeShare Data Prep** visas under avsnittet __Förberedelse av Data__ i fliken __Data__. 

   För att visa paketet, markera den här posten. 

5. Välj knappen **>>** för att expandera för att visa de __Dataflöden__ som ingår i paketet. I det här exemplet är __BostonWeather__ det enda dataflödet.

   > [!IMPORTANT]
   > Ett paket kan innehålla flera dataflöden.

   ![Bild av de dataflöden som ingår i paketet](media/tutorial-bikeshare-dataprep/weatherdataloadedingrid.png)

## <a name="filter-data-by-value"></a>Filtrera data efter värde
1. Filtrera data, högerklicka på en cell med ett visst värde och välj __Filter__, och sedan typ av filter.

2. För den här självstudien, markerar du en cell som innehåller värdet `FM-15` och sedan anger du filtret till ett filter för **Är lika med**.  Nu filtreras data till att endast returnera rader där __REPORTTYPE__ är `FM-15`.

   ![Bild av filterdialogrutan](media/tutorial-bikeshare-dataprep/weatherfilterinfm15.png)

   > [!NOTE]
   > FM-15 är en typ av METAR-rapport (Meteorological Terminal Aviation Routine Weather Report). FM-15:s rapporter har empiriskt observerats vara de mest fullständiga, med lite saknad data.

## <a name="remove-a-column"></a>Ta bort en kolumn

Du behöver inte längre kolumnen __REPORTTYPE__. Högerklicka på kolumnrubriken och välj **Ta bort kolumn**.

   ![Bild av alternativet Ta bort kolumn](media/tutorial-bikeshare-dataprep/weatherremovereporttype.png)

## <a name="change-datatypes-and-remove-errors"></a>Ändra datatyper och ta bort fel
1. När du trycker på __Ctrl (kommandot ⌘ på Mac)__ kan du medan du väljer kolumnrubriker markera flera kolumner på samma gång. Använd detta för att välja följande kolumnrubriker:
   * **HOURLYDRYBULBTEMPF**
   * **HOURLYRelativeHumidity**
   * **HOURLYWindSpeed**

2. **Högerklicka** på en av de valda kolumnrubrikerna och välj **Konvertera fälttyp till numerisk**. Detta omvandlar datatypen för kolumnerna till numerisk.

   ![Konvertera flera kolumner till numeriska](media/tutorial-bikeshare-dataprep/weatherconverttonumeric.png)

3. Filtrera ut felvärdena. Vissa kolumner har problem med konverteringen av datatyp. Det här problemet visas med röd färg i __Datakvalitetsstapeln__ för kolumnen.

   Om du vill ta bort de rader som innehåller fel, högerklicka på kolumnrubriken **HOURLYDRYBULBTEMPF**. Välj **Filterkolumn**. Använd standard **Jag vill att** som **Behåll rader**. Ändra listrutan **Villkor** och välj **är inte ett fel**. Välj **OK** för att tillämpa filtret.

    ![filtererrorvalues.png](media/tutorial-bikeshare-dataprep/filtererrorvalues.png)

4. För att minimera de återstående raderna med fel i de övriga kolumnerna måste du upprepa filterproceduren för kolumnerna **HOURLYRelativeHumidity** och **HOURLYWindSpeed**.

## <a name="use-by-example-transformations"></a>Använd omvandlingar som exempel

Om du vill använda data i en förutsägelse för tvåtimmarsblock, måste du beräkna de genomsnittliga väderleksvillkoren för tvåtimmarsperioder. Om du vill göra detta måste du använda följande åtgärder:

* Dela kolumnen **DATE** i separata kolumner för **Datum** och **Tid**. Se nästa avsnitt för detaljerade anvisningar.

* Härled en kolumn för **Hour_Range** från kolumnen **Tid**. Se följande avsnitt för detaljerade anvisningar.

* Härled en kolumn för **Datum\_Timintervall\_** från kolumnerna **DATE** och **Hour_Range**. Se följande avsnitt för detaljerade anvisningar.

### <a name="split-column-by-example"></a>Dela kolumner med exempel

1. Dela kolumnen **DATE** i separata kolumner för datum och tid. Högerklicka på kolumnrubriken **DATE** och välj **Dela kolumn efter exempel**.

   ![Bild av den delade kolumnen enligt exempelpost](media/tutorial-bikeshare-dataprep/weathersplitcolumnbyexample.png)

2. Azure Machine Learning-arbetsstationen identifierar automatiskt en meningsfull avgränsare och skapar två kolumner genom att dela data i datum- och tidsvärden. 

3. Välj __OK__ för att acceptera resultaten för delningen.

   ![Bild av de delade kolumnerna DATE_1 och DATE_2](media/tutorial-bikeshare-dataprep/weatherdatesplitted.png)

### <a name="derive-column-by-example"></a>Härled kolumner med exempel

1. För att härleda ett tvåtimmarsintervall, högerklicka på kolumnrubriken __DATE\_2__ och välj **Härled kolumn efter exempel**.

   ![Bild av den härledda kolumnen enligt exempelpost](media/tutorial-bikeshare-dataprep/weatherdate2range.png)

   En ny tom kolumn läggs med null-värden.

2. Klicka på den första tomma cellen i den nya kolumnen. Ge ett exempel på det tidsintervall som önskas genom att skriva `12AM-2AM` i den nya kolumnen och tryck sedan på retur.

   ![Bild av den nya kolumnen med ett värde av 12.00–02.00](media/tutorial-bikeshare-dataprep/weathertimerangeexample.png)

   > [!NOTE]
   > Azure ML-arbetsstationen syntetiserar program baserat på de exempel som tillhandahålls av dig och använder samma program på övriga rader. Alla andra rader fylls i automatiskt baserat på de exempel som du angav. Arbetsstationen analyserar också dina data och försöker identifiera gränsfall. 

   > [!IMPORTANT]
   > Identifiering av edge fall kanske inte fungerar på Mac i den aktuella versionen av arbetsstationen. Hoppa över den __steg 3__ och __steg 4__ nedan på Mac. Utan på __OK__ när alla rader hämta fylls med de härledda värdena.
   
3. Texten **Analysera data** ovanför rutnätet anger att arbetsstationen försöker identifiera gränsfall. När du är klar ändras statusen till **Granska nästa föreslagna rad** eller **Inga förslag**. I det här exemplet returneras **Granska nästa föreslagna rad**.

4. Om du vill granska de föreslagna ändringarna, välj **Granska nästa föreslagna rad**. Cellen som du bör granska och korrigera (vid behov) är markerad på skärmen.

   ![Bild av granskningsraden](media/tutorial-bikeshare-dataprep/weatherreviewnextsuggested.png)

    Välj __OK__ för att acceptera omvandlingen.
 
5. Du kommer tillbaka till rutnätsvyn för data för __BostonWeather__. Rutnätet innehåller nu tre kolumner som lagts till tidigare.

   ![Bild av rutnätsvy med tillagda rader](media/tutorial-bikeshare-dataprep/timerangecomputed.png)

   > [!TIP]
   >  Alla ändringar du har gjort sparas på panelen **Steg**. Gå till de steg som du skapade i panelen **Steg**, klicka på nedpilen och välj **Redigera**. Det avancerade fönstret för **Härled kolumn efter exempel** visas. Alla exempel bevaras här. Du kan också lägga till exempel manuellt genom att dubbelklicka på en rad i rutnätet nedan. Välj **Avbryt** för att återgå till det huvudsakliga rutnätet utan att tillämpa ändringarna. Du kan också komma åt den här vyn genom att välja **Avancerat läge** när du utför en omvandling med **Härled kolumn efter exempel**.

6. Om du vill byta namn på kolumnen, dubbelklickar du på kolumnrubriken och skriver **Timintervall**. Tryck på **Retur** att spara ändringen.

   ![Byta namn på kolumnen](media/tutorial-bikeshare-dataprep/weatherhourrangecolumnrename.png)

7. För att härleda datum- och tidsintervallet, väljer du både kolumnen **Datum\_1** och **Timintervall**, högerklickar och väljer sedan **Härled kolumn efter exempel**.

   ![Härleda kolumner med exempel](media/tutorial-bikeshare-dataprep/weatherderivedatehourrange.png)

   Ange `Jan 01, 2015 12AM-2AM` som exempel mot den första raden och tryck på **Retur**.

   Arbetsstationen anger omvandlingen som baseras på exemplet som du anger. I det här exemplet är resultatet att datumet format ändras och användas i sammanslagningen av fönstret två timmar.

   ![Bild av exemplet 01 januari 2015 12.00–02.00](media/tutorial-bikeshare-dataprep/wetherdatehourrangeexample.png)

   > [!IMPORTANT]
   > Följ anvisningarna nedan om du i stället för på Mac __steg 8__ nedan.
   >
   > * Gå till den första cellen som innehåller `Feb 01, 2015 12AM-2AM`. Det bör vara den __rad 15__. Korrigera värdet för `Jan 02, 2015 12AM-2AM`, och tryck på __RETUR__. 
   

8. Vänta på status för att ändra från **Analysera data** till **Granska nästa föreslagna rad**. Det kan ta flera sekunder. Välj statuslänken för att navigera till den föreslagna raden. 

   ![Bild av den föreslagna raden att granska](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguate.png)

   Raden har ett null-värde eftersom källdatavärdet kan vara antingen dd/mm/åååå eller mm/dd/åååå. Ange det korrekta värdet för `Jan 13, 2015 2AM-4AM` och tryck på **Retur**. Arbetsstationen använder de två exemplen för att förbättra härledning för återstående rader.

   ![Bild av korrekt formaterade data](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguated.png)

9. Välj **OK** för att acceptera omvandlingen.

   ![Bild av det slutförda omvandlingsrutnätet](media/tutorial-bikeshare-dataprep/weatherdatehourrangecomputed.png)

   > [!TIP]
   > Du kan använda avancerat läge av **Härled kolumn efter exempel** för det här steget genom att klicka på pilen i fönstret **Steg**. I datarutnätet finns det kryssrutor bredvid kolumnnamnen **DATE\_1** och **Timintervall**. Avmarkera kryssrutan bredvid kolumnen **Timintervall** för att se hur detta ändrar utdata. Om kolumnen **Timintervall** som indata saknas, behandlas **12.00–02.00** som en konstant och läggs till i de härledda värdena. Välj **Avbryt** för att återgå till det huvudsakliga rutnätet utan att tillämpa ändringarna.
   ![derivedcolumnadvancededitdeselectcolumn.png](media/tutorial-bikeshare-dataprep/derivedcolumnadvancededitdeselectcolumn.png)

10. Om du vill byta namn på kolumnen, dubbelklickar du på rubriken. Ändra namnet till **Datum Timintervall** och tryck sedan på **Retur**.

11. Markera kolumnerna **DATE**, **DATE\_1**, **DATE\_2**, och **Timintervall**. Högerklicka och välj **Ta bort kolumn**.

## <a name="summarize-data-mean"></a>Sammanfatta data (medel)

Nästa steg är att sammanfatta vädret genom att ta medelvärdet av värdena, grupperat efter timintervall.

1. Välj kolumnen **Datum Timintervall** och välj sedan **Sammanfatta** från menyn **Transformeringar**.

    ![Transformeringsmenyn](media/tutorial-bikeshare-dataprep/weathersummarizemenu.png)

2. För att sammanfatta data drar du kolumner från rutnätet längst ned på sidan till vänster och höger fönster längst upp. Den vänstra panelen innehåller texten **Dra kolumner hit om du vill gruppera data**. Det högra fönstret innehåller texten **Dra kolumner hit om du vill sammanfatta data**. 

    Dra kolumnen **Datum Timintervall** från rutnätet längst ned i det vänstra fönstret. Dra **HOURLYDRYBULBTEMPF**, **HOURLYRelativeHumidity** och **HOURLYWindSpeed** till det högra fönstret. 

    I det högra fönstret väljer du **Medel** som måttet **Samlat** för varje kolumn. Klicka på **OK** för att slutföra sammanfattningen.

   ![Bild av sammanfattad dataskärm](media/tutorial-bikeshare-dataprep/weathersummarize.png)

## <a name="transform-dataflow-using-script"></a>Transformera dataflöde med hjälp av skript

Genom att ändra data i de numeriska kolumnerna till ett intervall på 0–1 kan vissa modeller konvergera snabbt. Det finns för närvarande ingen inbyggd transformation för att göra den här transformationen, men Python-skriptet kan användas för att utföra den här åtgärden.

1. Från den **transformera** väljer du **transformera dataflöde (skript)**.

2. Ange följande kod i textrutan som visas. Om du har använt kolumnnamnen bör koden fungera utan modifiering. Du skriver en enkel normaliseringslogik för min–max i Python.

    > [!WARNING]
    > Skriptet förväntar de kolumnnamn som använts tidigare i den här självstudien. Om du har andra kolumnnamn måste du ändra namn i skriptet.

   ```python
   maxVal = max(df["HOURLYDRYBULBTEMPF_Mean"])
   minVal = min(df["HOURLYDRYBULBTEMPF_Mean"])
   df["HOURLYDRYBULBTEMPF_Mean"] = (df["HOURLYDRYBULBTEMPF_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYDRYBULBTEMPF_Mean":"N_DryBulbTemp"},inplace=True)

   maxVal = max(df["HOURLYRelativeHumidity_Mean"])
   minVal = min(df["HOURLYRelativeHumidity_Mean"])
   df["HOURLYRelativeHumidity_Mean"] = (df["HOURLYRelativeHumidity_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYRelativeHumidity_Mean":"N_RelativeHumidity"},inplace=True)

   maxVal = max(df["HOURLYWindSpeed_Mean"])
   minVal = min(df["HOURLYWindSpeed_Mean"])
   df["HOURLYWindSpeed_Mean"] = (df["HOURLYWindSpeed_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYWindSpeed_Mean":"N_WindSpeed"},inplace=True)

   df
   ```

    > [!TIP]
    > Python-skriptet måste returnera `df` i slutet. Det här värdet används för att fylla i rutnätet.
    
    ![Skriptdialog för att transformera dataflödet](media/tutorial-bikeshare-dataprep/transformdataflowscript.png)

3. Välj __OK__ för att använda skriptet. Numeriska kolumner i rutnätet innehåller nu värden i intervallet 0–1.

    ![Rutnät som innehåller värden mellan 0 och 1](media/tutorial-bikeshare-dataprep/datagridwithdecimals.png)

Förberedelsen av väderdata är klar. Förbered nu resedata.

## <a name="load-trip-data"></a>Läs in resedata

1. Så här importerar du filen `201701-hubway-tripdata.csv`, använd stegen i avsnittet [Skapa en ny datakälla](#newdatasource). Använd följande alternativ under importen:

    * __Filen markeringen__: Välj **Azure Blob** när du bläddrar för att välja filen.
    * __Samplingsschemat__: **Fullständig fil** samplingsschema, aktivera exemplet och 
    * __Datatyp__: Acceptera standardvärdena.

2. När du har importerat data, väljer du knappen __Förbered__ om du vill börja förbereda data. Välj det befintliga paketet **BikeShare Data Prep.dprep** och välj sedan __OK__.

    Den här processen lägger till ett **Dataflöde** i den befintliga filen **Förberedelse av data** i stället för att skapa en ny.

    ![Bild av val av det befintliga paketet](media/tutorial-bikeshare-dataprep/addjandatatodprep.png)

3. När rutnätet har lästs in, expandera __DATAFLOWS__. Det finns nu två dataflöden: **BostonWeather** och **201701-hubway-tripdata**. Välj posten **201701-hubway-tripdata**.

    ![Bild av posten 201701-hubway-tripdata](media/tutorial-bikeshare-dataprep/twodfsindprep.png)

## <a name="use-map-inspector"></a>Använd kartkontroll

För förberedelse av data finns det ett antal användbara visualiseringar som kallas **Kontroller** för sträng-, numeriska och geografiska data för att bättre förstå data och för att identifiera avvikare. Använd följande steg för att använda kartkontrollen:

1. Välj både kolumnen **latitud för startstation** och **longitud för startstation**. Högerklicka på en av kolumnerna och välj sedan **Karta**.

    > [!TIP]
    > Om du vill aktivera flerval, håll ned __Ctrl (kommandot ⌘ på Mac)__ och välj rubriken för varje kolumn.

    ![Bild av kartvisualiseringen](media/tutorial-bikeshare-dataprep/launchMapInspector.png)

2. För att maximera kartvisualiseringen, välj ikonen **Maximera**. För att passa in kartan till fönstret väljer du ikonen **E** på den översta vänstra sidan av visualiseringen.

    ![Maximerad bild](media/tutorial-bikeshare-dataprep/maximizedmap.png)

3. Klicka på knappen **Minimera** för att gå tillbaka till rutnätsvyn.

## <a name="use-column-statistics-inspector"></a>Använd kolumnstatistikkontroll

Om du vill använda kolumnstatistikkontroll, högerklicka på kolumnen __tripduration__ och markera __Kolumnstatistik__.

![Kolumnstatistikpost](media/tutorial-bikeshare-dataprep/tripdurationcolstats.png)

Den här processen lägger till en ny visualisering med titeln __tripduration Statistics__ i fönstret __INSPECTORS__.

![Bild av kontroll för tripduration Statistics](media/tutorial-bikeshare-dataprep/tripdurationcolstatsinwell.png)

> [!IMPORTANT]
> Det högsta värdet för resans varaktighet är **961 814 minuter**, vilket är ungefär två år. Det verkar det finns vissa avvikare i datauppsättningen.

## <a name="use-histogram-inspector"></a>Använd histogramkontroll

Om du vill försöka identifiera avvikare högerklickar du på kolumnen __tripduration__ markerar __Histogram__.

![Histogramkontroll](media/tutorial-bikeshare-dataprep/tripdurationhistogram.png)

Histogrammet är inte användbart, eftersom avvikare snedställer diagrammet.

## <a name="add-column-using-script"></a>Lägg till kolumn med hjälp av skript

1. Högerklicka på kolumnen **tripduration** och markera **Lägg till kolumn (skript)**.

    ![Bild av menyn Lägg till kolumn (skript)](media/tutorial-bikeshare-dataprep/computecolscript.png)

2. I dialogrutan __Lägg till kolumn (skript)__, använder du följande värden:

    * __Nya kolumnnamn__: logtripduration
    * __Infoga den här nya kolumn efter__: tripduration
    * __Ny kolumnkod__: `math.log(row.tripduration)`
    * __Kodblocktyp__: Uttryck

   ![Bild av dialogen Lägg till kolumn (skript)](media/tutorial-bikeshare-dataprep/computecolscriptdialog.png)

3. Välj __OK__ för att lägga till kolumnen **logtripduration**.

4. Högerklicka på kolumnen och välj **Histogram**.

    ![Histogram för kolumnen logtripduration](media/tutorial-bikeshare-dataprep/logtriphistogram.png)

  Det här histogrammet verkar visuellt som en normalfördelning med en onormal ände.

## <a name="use-advanced-filter"></a>Använd avancerat filter

Genom att använda ett filter på data uppdateras kontrollerna med den nya distributionen. Högerklicka på kolumnen **logtripduration** och markera **Filtrera kolumn**. I dialogrutan __Redigera__, använder du följande värden:

* __Filtrera den här talkolumnen__: logtripduration
* __Jag vill__: Behålla rader
* __När__: Något av villkoren nedan är Sant (logiskt ELLER)
* __Om den här kolumnen__: är mindre än
* __Värdet__: 9

![Filtrera alternativ](media/tutorial-bikeshare-dataprep/loftripfilter.png)

Välj __OK__ för att tillämpa filtret.

![Uppdaterade histogram efter filter används](media/tutorial-bikeshare-dataprep/loftripfilteredinspector.png)

### <a name="the-halo-effect"></a>Halo-effekt

1. Maximera histogrammet **logtripduration**. Det finns ett blått histogram ovanpå ett grått histogram. Den här vyn kallas **Halo-effekt**:

    * Det **grå histogrammet** representerar distributionen innan åtgärden (i det här fallet filtreringen).
    * Det **blå histogrammet** representerar histogrammet efter åtgärden. 

   Halo-effekten hjälper att visualisera effekten av en åtgärd på data.

   ![Bild på halo-effekten](media/tutorial-bikeshare-dataprep/loftripfilteredinspectormaximized.png)

    > [!NOTE]
    > Observera att det blå histogrammet visas kortare jämfört med föregående. Detta beror på automatisk bucket av data igen i det nya området.

2. Om du vill ta bort halon, välj __Redigera__ och avmarkerar __Visa halo__.

    ![Alternativ för histogrammet](media/tutorial-bikeshare-dataprep/uncheckhalo.png)

3. Välj **OK** för att inaktivera halo-effekten och minimera histogrammet.

### <a name="remove-columns"></a>Ta bort kolumner

I resedata representerar varje rad en cykelhämtningshändelse. I den här självstudien behöver du bara kolumnen **starttime** och **starta station id**. Ta bort de andra kolumnerna genom att välja dessa två kolumner samtidigt, högerklicka på kolumnrubriken och välj sedan **Behåll kolumn**. Andra kolumner tas bort.

![Bild av alternativet Behåll kolumn](media/tutorial-bikeshare-dataprep/tripdatakeepcolumn.png)

### <a name="summarize-data-count"></a>Sammanfatta data (antal)

Använd härledda kolumner för att sammanfatta cykelefterfrågan för en 2-timmarsperiod.

1. Högerklicka på kolumnen **starttime** och markera **Härled kolumn efter exempel**

    ![Bild av alternativet härled efter exempel](media/tutorial-bikeshare-dataprep/tripdataderivebyexample.png)

2. För det här exemplet anger du värdet `Jan 01, 2017 12AM-2AM` för den första raden.

    > [!IMPORTANT]
    > I det förra exemplet för att härleda kolumner används flera steg för att härleda en kolumn som innehåller datum- och tidsperioden. I det här exemplet ser du att den här åtgärden kan utföras som ett enda steg genom att tillhandahålla ett exempel på det slutgiltiga resultatet.

    > [!NOTE]
    > Du kan ge ett exempel mot någon av raderna. I det här exemplet är värdet för `Jan 01, 2017 12AM-2AM` giltigt för den första raden med data.

    ![Bild av exempeldata](media/tutorial-bikeshare-dataprep/tripdataderivebyexamplefirstexample.png)

   > [!IMPORTANT]
   > Följ anvisningarna nedan om du i stället för på Mac __steg 3__ nedan.
   >
   > * Gå till den första cellen som innehåller `Jan 01, 2017 1AM-2AM`. Det bör vara den __rad 14__. Korrigera värdet för `Jan 01, 2017 12AM-2AM`, och tryck på __RETUR__. 

3. Vänta tills programmet beräknar värden mot alla rader. Det kan ta flera sekunder. När analysen är klar kan du använda länken __Granska nästa föreslagna rad__ för att granska data.

   ![Bild av slutförd analys med granskningslänk](media/tutorial-bikeshare-dataprep/tripdatabyexanalysiscomplete.png)

    Se till att de beräknade värdena är korrekta. Om inte, uppdatera värdet med det förväntade värdet och tryck på retur. Vänta sedan på att analysen ska slutföras. Slutför processen **Granska nästa föreslagna rad** tills du ser **Inga förslag**. När du ser **Inga förslag**, har programmet tittat på gränsfall och är nöjd med det syntetiserade programmet. Det är bäst att utföra en granskning av transformerade data innan du godkänner transformationen. 

4. Välj **OK** för att acceptera omvandlingen. Byt namn på den nyligen skapade kolumnen till **Datum Timintervall**.

    ![Bild av den omdöpta kolumnen](media/tutorial-bikeshare-dataprep/tripdatasummarize.png)

5. Högerklicka på kolumnrubriken **starttime** och välj **Ta bort kolumn**.

6. Om du vill sammanfatta data, välj __Sammanfatta__ från menyn __Transformera__. Utför följande åtgärder för att skapa transformeringen:

    * Dra __Datum Timintervall__ och __startstation-id__ till vänster fönster (gruppera enligt).
    * Dra __startstation-id__ till höger fönster (sammanfatta data).

   ![En bild av sammanfattningsalternativen](media/tutorial-bikeshare-dataprep/tripdatacount.png)

7. Välj **OK** för att acceptera sammanfattningsresultaten.

## <a name="join-dataflows"></a>Slå ihop dataflöden

Använd följande steg för att slå ihop väderdata med resedata:

1. Välj __Koppla__ från menyn __Transformerar__.

2. __Tabeller__: Välj **BostonWeather** som det vänstra dataflödet och **201701-hubway-tripdata** som det högra dataflödet. Välj **Nästa** för att fortsätta.

    ![Bild av tabellvalen](media/tutorial-bikeshare-dataprep/jointableselection.png)

3. __Nyckelkolumner__: Välj kolumnen **Datum Timintervall** i båda tabellerna och välj sedan __Nästa__.

    ![Bild av sammanslagningen av nyckelkolumner](media/tutorial-bikeshare-dataprep/joinkeyselection.png)

4. __Kopplingstyp__: Välj __Matchande rader__ som kopplingstyp och välj sedan __Slutför__.

    ![Matchande rader som kopplingstyp](media/tutorial-bikeshare-dataprep/joinscreen.png)

    Den här processen skapar ett nytt dataflöde med namnet __Kopplingsresultat__.

## <a name="create-additional-features"></a>Skapa ytterligare funktioner

1. Om du vill skapa en kolumn som innehåller veckodagen, högerklicka på kolumnen **Datum Timintervall** och markera **Härled kolumn efter exempel**. Använd värdet __Sun__ för ett datum som inträffat på söndag. Till exempel **01 januari 2017 12.00–02.00**. Tryck på **Retur** och välj sedan **OK**. Byt namn på den här kolumnen till __Veckodag__.

    ![Bild av hur du skapar en ny kolumn som innehåller veckodagen](media/tutorial-bikeshare-dataprep/featureweekday.png)

2. Om du vill skapa en kolumn som innehåller tidsperioden för raden, högerklicka på kolumnen **Datum Timintervall** och markera **Härled kolumn efter exempel**. Använd värdet **12.00–02.00** för den rad som innehåller **01 januari 2017 12.00–02.00**. Tryck på **Retur** och välj sedan **OK**. Byt namn på den här kolumnen till **Period**.

    ![Bild av periodkolumnen](media/tutorial-bikeshare-dataprep/featurehourrange.png)

3. Ta bort kolumnerna **Datum Timintervall** och **rDatum Timintervall**, tryck på **Ctrl (kommandot ⌘ på Mac)** och markera varje kolumnrubrik. Högerklicka och välj **Ta bort kolumn**.

## <a name="read-data-from-python"></a>Läs data från Python

Du kan köra ett paket för förberedelse av data från Python eller PySpark och hämta resultatet som en **Dataram**.

Om du vill generera ett Python-skriptexempel, högerklicka på __BikeShare Data Prep__ och välj __Generera kodfil för dataåtkomst__. Python-exempelfilen skapas i din **Projektmapp**, och läses även in på en flik i arbetsstationen. Följande skript i Python är ett exempel på den kod som genererades:

```python
# Use the Azure Machine Learning data preparation package
from azureml.dataprep import package

# Use the Azure Machine Learning data collector to log various metrics
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# This call will load the referenced package and return a DataFrame.
# If run in a PySpark environment, this call returns a
# Spark DataFrame. If not, it will return a Pandas DataFrame.
df = package.run('BikeShare Data Prep.dprep', dataflow_idx=0)

# Remove this line and add code that uses the DataFrame
df.head(10)
```

För den här självstudien är namnet på filen `BikeShare Data Prep.py`. Den här filen används senare under självstudien.

## <a name="save-test-data-as-a-csv-file"></a>Spara testdata som en CSV-fil

För att spara dataflöde för **Koppla resultat** till en. CSV-fil måste du ändra skriptet `BikeShare Data Prep.py`. 

1. Öppna projektet för redigering i VSCode.

    ![openprojectinvscode.png](media/tutorial-bikeshare-dataprep/openprojectinvscode.png)

2. Uppdatera Python-skriptet i den `BikeShare Data Prep.py` filen med följande kod:

    ```python
    import pyspark

    from azureml.dataprep.package import run
    from pyspark.sql.functions import *

    # start Spark session
    spark = pyspark.sql.SparkSession.builder.appName('BikeShare').getOrCreate()

    # dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
    df = run('BikeShare Data Prep.dprep', dataflow_idx=2)
    df.show(n=10)
    row_count_first = df.count()

    # Example file name: 'wasb://data-files@bikesharestorage.blob.core.windows.net/testata'
    # 'wasb://<your container name>@<your azure storage name>.blob.core.windows.net/<csv folder name>
    blobfolder = 'Your Azure Storage blob path'

    df.write.csv(blobfolder, mode='overwrite') 

    # retrieve csv file parts into one data frame
    csvfiles = "<Your Azure Storage blob path>/*.csv"
    df = spark.read.option("header", "false").csv(csvfiles)
    row_count_result = df.count()
    print(row_count_result)
    if (row_count_first == row_count_result):
        print('counts match')
    else:
        print('counts do not match')
    print('done')
    ```

3. Ersätt `Your Azure Storage blob path` med sökväg till utdatafilen som ska skapas. Ersätt för både den `blobfolder` och `csvfiles` variabler.

## <a name="create-hdinsight-run-configuration"></a>Skapa HDInsight kör konfiguration

1. Öppna kommandoradsfönstret i Azure Machine Learning Workbench, välj menyn **Arkiv** och välj **Öppna kommandotolken**. Kommandotolken startar i projektmappen med prompten `C:\Projects\BikeShare>`.

 ![opencommandprompt.png](media/tutorial-bikeshare-dataprep/opencommandprompt.png)

   >[!IMPORTANT]
   >Du måste använda kommandotolken (som öppnas från Workbench) för att utföra följande steg.

2. Använd kommandotolken för att logga in på Azure. 

   Workbench-appen och CLI använder oberoende cacheminnen för inloggningsuppgifter vid autentisering mot Azure-resurser. Du behöver bara göra det här en gång, tills token i cacheminnet upphör att gälla. Den `az account list` kommando returnerar listan över tillgängliga prenumerationer för din inloggning. Om det finns fler än en använder du ID-värdet från prenumerationen som du vill använda. Ange den prenumeration som standardkonto för användning med den `az account set -s` kommando och ange prenumerations-ID-värde. Bekräfta inställningen med kontot `show` kommando.

   ```azurecli
   REM login by using the aka.ms/devicelogin site
   az login
   
   REM lists all Azure subscriptions you have access to 
   az account list -o table
   
   REM sets the current Azure subscription to the one you want to use
   az account set -s <subscriptionId>
   
   REM verifies that your current subscription is set correctly
   az account show
   ```

3. Skapa HDInsight kör config. Du måste namnet på klustret och sshuser lösenord.
    ```azurecli
    az ml computetarget attach --name hdinsight --address <yourclustername>.azurehdinsight.net --username sshuser --password <your password> --type cluster
    az ml experiment prepare -c hdinsight
    ```
> [!NOTE]
> När du har skapat ett tomt projekt standardkonfigurationerna kör är **lokala** och **docker**. Det här steget skapar en ny konfiguration för Kör som är tillgängliga i den **Azure Machine Learning arbetsstationen** när du kör skripten. 

## <a name="run-in-hdinsight-cluster"></a>Kör i HDInsight-kluster

Gå tillbaka till den **Azure Machine Learning arbetsstationen** program att köra skriptet i HDInsight-klustret.

1. Gå tillbaka till startsidan i ditt projekt genom att klicka på den **hem** ikonen till vänster.

2. Välj **hdinsight** i listrutan för att köra skriptet i HDInsight-klustret.

3. Välj **Kör** högst upp på skärmen. Skriptet har skickats in som en **jobbet**. När jobbet har statusen ändras till __slutförd__, filen har skrivits till den angivna platsen i din **Azure Storage-behållare**.

    ![hdinsightrunscript.png](media/tutorial-bikeshare-dataprep/hdinsightrunscript.png)


## <a name="substitute-data-sources"></a>Ersätt datakällor

I föregående steg, använde du datakällorna `201701-hubway-tripdata.csv` och `BostonWeather.csv` för att förbereda testdata. Om du vill använda paketet med andra resedatafiler, använder du följande steg:

1. Skapa en ny **Datakälla** med hjälp av anvisningarna som gavs tidigare, med följande ändringar i processen:

    * __Filval__: När du väljer en fil, välj de sex återstående tripdata. CSV-filerna för resan samtidigt.

        ![Läs in de sex återstående filerna](media/tutorial-bikeshare-dataprep/browseazurestoragefortripdatafiles.png)

        > [!NOTE]
        > Posten __+ 5__ indikerar att det finns fem ytterligare filer utöver den som visas.

    * __Filinformation__: Ange __Befordra rubrikläge__ till **Alla filer har samma rubriker**. Det här värdet anger att var och en av filerna innehåller samma rubrik.

        ![Val av filinformation](media/tutorial-bikeshare-dataprep/headerfromeachfile.png) 

   Spara namnet på den här datakällan eftersom den används i senare steg.

2. Välj mappikonen för att visa filerna i projektet. Expandera katalogen __aml\_config__ och välj sedan filen `hdinsight.runconfig`.

    ![Bild av platsen för hdinsight.runconfig](media/tutorial-bikeshare-dataprep/hdinsightsubstitutedatasources.png) 

3. Klicka på knappen Redigera om du vill öppna filen i VSCode.

4. Lägg till följande rader i slutet av filen `hdinsight.runconfig` och klicka på diskikonen för att spara filen.

    ```yaml
    DataSourceSubstitutions:
      201701-hubway-tripdata.dsource: 201501-hubway-tripdata.dsource
    ```

    Den här ändringen ersätter den ursprungliga datakällan med den som innehåller de sex resedatafilerna.

## <a name="save-training-data-as-a-csv-file"></a>Spara träningsdata som en CSV-fil

Navigera till filen Python `BikeShare Data Prep.py` som du redigerade tidigare och ange en annan sökväg till filen för att spara träningsdata.

```python
import pyspark

from azureml.dataprep.package import run
from pyspark.sql.functions import *

# start Spark session
spark = pyspark.sql.SparkSession.builder.appName('BikeShare').getOrCreate()

# dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
df = run('BikeShare Data Prep.dprep', dataflow_idx=2)
df.show(n=10)
row_count_first = df.count()

# Example file name: 'wasb://data-files@bikesharestorage.blob.core.windows.net/traindata'
# 'wasb://<your container name>@<your azure storage name>.blob.core.windows.net/<csv folder name>
blobfolder = 'Your Azure Storage blob path'

df.write.csv(blobfolder, mode='overwrite') 

# retrieve csv file parts into one data frame
csvfiles = "<Your Azure Storage blob path>/*.csv"
df = spark.read.option("header", "false").csv(csvfiles)
row_count_result = df.count()
print(row_count_result)
if (row_count_first == row_count_result):
    print('counts match')
else:
    print('counts do not match')
print('done')
```

1. Använd mappnamnet `traindata` för utbildning data utdata.

2. Om du vill skicka ett nytt jobb, använd ikonen **Kör** längst upp på sidan. Kontrollera att **hdinsight** är markerad. Ett **Jobb** skickas in med den nya konfigurationen. Utdata för jobbet är träningsdata. Dessa data skapas med hjälp av samma steg för förberedelse av data som du skapade tidigare. Det kan ta några minuter att slutföra jobbet.


## <a name="next-steps"></a>Nästa steg
Du har slutfört självstudierna för förberedelse av data för cykeldelning. I den här självstudien använde du Azure Machine Learning (förhandsversion) till att lära dig att:
> [!div class="checklist"]
> * Förbereda data interaktivt med förberedelseverktyget för Azure Machine Learning Data
> * Importera, transformera och skapa en test-datauppsättning
> * Skapa ett paket för förberedelse av data
> * Kör paketet för förberedelse av data med Python
> * Generera en datauppsättning för träning genom att återanvända paketet för förberedelse av data för ytterligare inkommande filer

Härnäst, lär dig mer om förberedelse av data:
> [!div class="nextstepaction"]
> [Användarhandbok för förberedelse av data](data-prep-user-guide.md)
