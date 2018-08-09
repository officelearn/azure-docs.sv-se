---
title: Självstudie för cykeldelning – förberedelse av avancerade data med Azure Machine Learning-arbetsstationen
description: I den här självstudien utför du en dataförberedelse för slutpunkt till slutpunkt med hjälp av Azure Machine Learning Workbench
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/21/2017
ms.openlocfilehash: 2a50350b9ba49d82a20b92804ffb92ec6906186d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439908"
---
# <a name="tutorial-use-azure-machine-learning-workbench-for-advanced-data-preparation-bike-share-data"></a>Självstudie: Använd Azure Machine Learning Workbench för avancerad dataförberedelse (BikeShare-data)
Azure Machine Learning (förhandsversion) är en integrerad analyslösning från slutpunkt till slutpunkt som dataforskare kan använda för att förbereda data, utveckla experiment och distribuera modeller i molnskala.

I den här självstudien använder du Machine Learning (förhandsversion) till att lära dig att:
> [!div class="checklist"]
> * Förbereda data interaktivt med förberedelseverktyget för Machine Learning-data.
> * Importera, transformera och skapa en testdatamängd.
> * Skapa ett paket för förberedelse av data.
> * Köra paketet för förberedelse av data med Python.
> * Generera en datamängd för träning genom att återanvända paketet för förberedelse av data för ytterligare inkommande filer.
> * Köra skript i ett lokalt Azure CLI-fönster.
> * Köra skript i en Azure HDInsight-molnmiljö.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En lokal installation av Azure Machine Learning Workbench. Mer information finns i [Snabbstart av installation](../service/quickstart-installation.md).
* Om du inte har installerat Azure CLI följer du anvisningarna för att [installera den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Ett [HDInsights Apache Spark-kluster](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal) som skapats i Azure.
* Ett Azure-lagringskonto.
* Liknar processen med att skapa ett nytt projekt i Workbench.
* Även om det inte krävs är det bra att ha [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) installerad, så att du kan ladda upp, ladda ned och visa blobbarna på ditt lagringskonto.

## <a name="data-acquisition"></a>Datainsamling
Den här självstudien använder [Boston Hubway-datamängd](https://s3.amazonaws.com/hubway-data/index.html) och Boston weather-data från [NOAA](http://www.noaa.gov/).

1. Hämta datafiler från följande länkar till din lokala utvecklingsmiljö:

   * [Boston weather-data](https://azuremluxcdnprod001.blob.core.windows.net/docs/azureml/bikeshare/BostonWeather.csv)

   * Hubway-resedata från Hubway-webbplatsen:

      - [201501-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201501-hubway-tripdata.zip)
      - [201504-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201504-hubway-tripdata.zip)
      - [201510-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201510-hubway-tripdata.zip)
      - [201601-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201601-hubway-tripdata.zip)
      - [201604-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201604-hubway-tripdata.zip)
      - [201610-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201610-hubway-tripdata.zip)
      - [201701-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201701-hubway-tripdata.zip)

1. Packa upp varje ZIP-fil efter nedladdning.

## <a name="upload-data-files-to-azure-blob-storage"></a>Ladda upp filer till Azure Blob Storage
Du kan använda Azure Blob Storage som värd för dina datafiler.

1. Använd samma lagringskonto som används för ditt HDInsight-kluster.

    ![Lagringskonto för HDInsight-kluster](media/tutorial-bikeshare-dataprep/hdinsightstorageaccount.png)

1. Skapa en ny container med namnet **datafiler** där du ska lagra **BikeShare**-datafiler.

1. Ladda upp datafilerna. Ladda upp `BostonWeather.csv` till en mapp med namnet `weather`. Ladda upp resedatafilerna till en mapp med namnet `tripdata`.

    ![Ladda upp datafiler](media/tutorial-bikeshare-dataprep/azurestoragedatafile.png)

> [!TIP]
> Du kan också använda Storage Explorer när du överför blobbar. Du kan också använda det här verktyget när du vill se innehållet i de filer som har genererats i självstudien.

## <a name="learn-about-the-datasets"></a>Lär dig mer om datauppsättningar
1. Filen __Boston weather__ innehåller följande väderrelaterade fält som rapporteras varje timme:

   * **DATE**

   * **REPORTTPYE**

   * **HOURLYDRYBULBTEMPF**

   * **HOURLYRelativeHumidity**

   * **HOURLYWindSpeed**

1. __Hubway__-data ordnas i filer per år och månad. Till exempel innehåller filen `201501-hubway-tripdata.zip` en .csv-fil med data för januari 2015. Datan innehåller följande fält, där varje rad motsvarar en cykelresa:

   * **Resans varaktighet (i sekunder)**

   * **Starttid och startdatum**

   * **Stopptid och stoppdatum**

   * **Startstationens namn och ID**

   * **Slutstationens namn och ID**

   * **Cykel-ID**

   * **Användartyp (Tillfällig = 24 timmars- eller 72-timmarsanvändare. Medlem = års- eller månadsmedlem)**

   * **Postnummer (om användaren är medlem)**

   * **Kön (självrapporterat av medlemmen)**

## <a name="create-a-new-project"></a>Skapa ett nytt projekt
1. Starta **Machine Learning Workbench** från startmenyn eller startprogrammet.

1. Skapa ett nytt Machine Learning-projekt. Klicka på knappen **+** på sidan **Projekt** eller välj **Arkiv** > **Ny**.

   * Använd mallen **BikeShare**.

   * Namnge projektet **BikeShare**. 

## <a id="newdatasource"></a>Skapa en ny datakälla

1. Skapa en ny datakälla. Klicka på knappen **Data** (cylinderikonen) till vänster i verktygsfältet för att visa vyn **Data**.

   ![Fliken Datavy](media/tutorial-bikeshare-dataprep/navigatetodatatab.png)

1. Lägg till en datakälla. Välj ikonen **+** och välj sedan **Lägg till datakälla**.

   ![Alternativet Lägg till datakälla](media/tutorial-bikeshare-dataprep/newdatasource.png)

## <a name="add-weather-data"></a>Lägg till väderdata

1. **Datalager**: Välj det datalager som innehåller data. Eftersom du använder filer väljer du **Fil(er)/katalog**. Välj **Nästa** för att fortsätta.

   ![Posten Fil(er)/katalog](media/tutorial-bikeshare-dataprep/datasources.png)

1. **Filval**: Lägg till väderdata. Bläddra och välj den `BostonWeather.csv`-fil som du tidigare laddade upp till Blob Storage. Välj **Nästa**.

   ![Filmarkering med BostonWeather.csv valt](media/tutorial-bikeshare-dataprep/azureblobpickweatherdatafile.png)

1. **Filinformation**: Verifiera filschemat som upptäcks. Machine Learning Workbench analyserar data i filen och härleder det schema som ska användas.

   ![Kontrollera filinformationen](media/tutorial-bikeshare-dataprep/fileparameters.png)

   > [!IMPORTANT]
   > Ibland kanske inte Workbench kan identifiera rätt schema. Du bör alltid kontrollera att parametrarna stämmer med datamängden. Kontrollera att de är inställda på följande värden för väderdata:
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

1. **Datatyper**: Granska de datatyper som identifieras automatiskt. Machine Learning Workbench analyserar data i filen och härleder de datatyper som ska användas.

   a. För dessa data ändrar du **DATATYP** för alla kolumner till **Sträng**.

   > [!NOTE]
   > Strängen används för att lyfta fram funktionerna i Workbench senare i den här självstudien. 

   ![Granska datatyper](media/tutorial-bikeshare-dataprep/datatypedetection.png)

   b. Välj __Nästa__ för att fortsätta. 

1. **Sampling**: Om du vill skapa ett samplingsschema väljer du **Redigera**. Välj den nya raden __Top 10000__ som har lagts till och markera sedan __Redigera__. Ange __Samplingsstrategi__ till **Fullständig fil**, och välj sedan **Tillämpa**.

   ![Lägga till en ny samplingsstrategi](media/tutorial-bikeshare-dataprep/weatherdatasamplingfullfile.png)

   Om du vill använda strategin __Fullständig fil__ väljer du posten __Fullständig fil__ och sedan __Ange som aktiv__. En stjärna visas bredvid __Fullständig fil__, vilket visar att detta är den aktiva strategin.

   ![Ange Fullständig fil som aktiv strategi](media/tutorial-bikeshare-dataprep/fullfileactive.png)

   Välj **Nästa** för att fortsätta.

1. **Sökvägskolumn**: Använd avsnittet __Sökvägskolumn__ för att inkludera den fullständiga sökvägen som en kolumn i den importerade informationen. Välj __Inkludera inte i sökvägskolumnen__.

   > [!TIP]
   > Det är användbart att inkludera sökvägen som en kolumn om du importerar en mapp med många filer med olika namn. Det är också användbart om filnamnen innehåller information som du vill extrahera senare.

   ![Den konfigurerade sökvägskolumnen inkluderar inte](media/tutorial-bikeshare-dataprep/pathcolumn.png)

1. **Slutför**: För att slutföra skapandet av datakällan väljer du **Slutför**.

    En ny flik för datakälla med namnet __BostonWeather__ öppnas. Ett exempel på data visas i ett rutnät. Exemplet baseras på det aktiva samplingsschemat som angavs tidigare.

    Observera rutan **Steg** till höger på skärmen som visar de enskilda åtgärder som vidtas när du skapar den här datakällan.

   ![Visa datakällan, exempel och steg](media/tutorial-bikeshare-dataprep/weatherdataloaded.png)

### <a name="view-data-source-metrics"></a>Visa mått för datakällan

Välj __Mått__ längst upp till vänster på fliken rutnätsvy. Den här vyn visar fördelningen och annan samlad statistik över exempeldata.

![Måttvisning](media/tutorial-bikeshare-dataprep/weathermetrics.png)

> [!NOTE]
> Om du vill konfigurera synligheten för statistik, använder du listrutan **Välj mått**. Markera och avmarkera mått om du vill ändra rutnätsvyn.

För att återgå till vyn __Data__ väljer du __Data__ i det övre vänstra hörnet på sidan.

## <a name="add-a-data-source-to-the-data-preparation-package"></a>Lägga till en datakälla till paketet med dataförberedelse

1. Välj __Förbered__ för att börja förbereda informationen. 

1. När du uppmanas till det anger du ett namn för paketet med dataförberedelse, som t.ex. **BikeShare Data Prep**. 

1. Klicka på __OK__ för att fortsätta.

   ![Dialogrutan Förbered](media/tutorial-bikeshare-dataprep/dataprepdialog.png)

1. Ett nytt paket med namnet **BikeShare Data Prep** visas under avsnittet __Förberedelse av data__ på fliken __Data__. 

   För att visa paketet, markera den här posten. 

1. Välj knappen **>>** för att expandera __Dataflöden__ och visa de dataflöden som ingår i paketet. I det här exemplet är __BostonWeather__ det enda dataflödet.

   > [!IMPORTANT]
   > Ett paket kan innehålla flera dataflöden.

   ![Dataflöden i paketet](media/tutorial-bikeshare-dataprep/weatherdataloadedingrid.png)

## <a name="filter-data-by-value"></a>Filtrera data efter värde
1. Filtrera data genom att högerklicka på en cell med ett visst värde. Välj sedan __Filter__. Välj sedan typ av filter.

1. I den här självstudien markerar du en cell som innehåller värdet `FM-15`. Anger sedan filtret till **är lika med**.  Nu filtreras datan till att endast returnera rader där __REPORTTYPE__ är `FM-15`.

   ![Dialogrutan Filter](media/tutorial-bikeshare-dataprep/weatherfilterinfm15.png)

   > [!NOTE]
   > FM-15 är en typ av METAR-väderrapport (Meteorological Terminal Aviation Routine). FM-15:s rapporter har empiriskt observerats vara de mest fullständiga, med lite saknad data.

## <a name="remove-a-column"></a>Ta bort en kolumn

Du behöver inte längre kolumnen __REPORTTYPE__. Högerklicka på kolumnrubriken och välj **Ta bort kolumn**.

   ![Alternativet Ta bort kolumn](media/tutorial-bikeshare-dataprep/weatherremovereporttype.png)

## <a name="change-datatypes-and-remove-errors"></a>Ändra datatyper och ta bort fel
1. Välj Ctrl (Cmd ⌘ på Mac) när du väljer kolumnrubriker om du vill välja flera kolumner på samma gång. Använd denna metod när du väljer följande kolumnrubriker:

   * **HOURLYDRYBULBTEMPF**

   * **HOURLYRelativeHumidity**

   * **HOURLYWindSpeed**

1. Högerklicka på en av de valda kolumnrubrikerna och välj **Konvertera fälttyp till numerisk**. Detta omvandlar datatypen för kolumnerna till numerisk.

   ![Konvertera flera kolumner till numeriska](media/tutorial-bikeshare-dataprep/weatherconverttonumeric.png)

1. Filtrera ut felvärdena. Vissa kolumner har problem med konverteringen av datatyp. Det här problemet visas med röd färg i __Datakvalitetsstapeln__ för kolumnen.

   Om du vill ta bort de rader som innehåller fel, högerklickar du på kolumnrubriken **HOURLYDRYBULBTEMPF**. Välj **Filterkolumn**. Använd standard **Jag vill att** som **Behåll rader**. Ändra listrutan **Villkor** till **är inte ett fel**. Välj **OK** för att tillämpa filtret.

   ![Filtrera felvärden](media/tutorial-bikeshare-dataprep/filtererrorvalues.png)

1. För att minimera de återstående raderna med fel i de övriga kolumnerna måste du upprepa filterproceduren för kolumnerna **HOURLYRelativeHumidity** och **HOURLYWindSpeed**.

## <a name="use-by-example-transformations"></a>Använd omvandlingar som exempel

Om du vill använda data i en förutsägelse för tvåtimmarsblock, måste du beräkna de genomsnittliga väderleksvillkoren för tvåtimmarsperioder. Vidta följande åtgärder:

* Dela kolumnen **DATE** i separata kolumner för **Datum** och **Tid**. Se följande avsnitt för detaljerade anvisningar.

* Härled en kolumn för **Hour_Range** från kolumnen **Tid**. Se följande avsnitt för detaljerade anvisningar.

* Härled en kolumn för **Datum\_Timintervall\_** från kolumnerna **DATE** och **Hour_Range**. Se följande avsnitt för detaljerade anvisningar.

### <a name="split-column-by-example"></a>Dela kolumner med exempel

1. Dela kolumnen **DATE** i separata kolumner för **Datum** och **Tid**. Högerklicka på kolumnrubriken **DATE** och välj **Dela kolumner med exempel**.

   ![Posten Dela kolumner med exempel](media/tutorial-bikeshare-dataprep/weathersplitcolumnbyexample.png)

1. Machine Learning Workbench identifierar automatiskt en meningsfull avgränsare och skapar två kolumner genom att dela data i datum- och tidsvärden. 

1. Välj __OK__ för att acceptera resultaten för delningen.

   ![De delade kolumnerna DATE_1 och DATE_2](media/tutorial-bikeshare-dataprep/weatherdatesplitted.png)

### <a name="derive-column-by-example"></a>Härled kolumner med exempel

1. För att härleda ett tvåtimmarsintervall högerklickar du på kolumnrubriken __DATE\_2__ och väljer **Härled kolumner med exempel**.

   ![Posten Härled kolumner med exempel](media/tutorial-bikeshare-dataprep/weatherdate2range.png)

   En ny tom kolumn läggs med null-värden.

1. Välj den första tomma cellen i den nya kolumnen. Ge ett exempel på det tidsintervall som önskas genom att skriva **12AM-2AM** i den nya kolumnen och tryck sedan på retur.

   ![Ny kolumn med värdet 12AM–2AM](media/tutorial-bikeshare-dataprep/weathertimerangeexample.png)

   > [!NOTE]
   > Machine Learning Workbench syntetiserar program baserat på de exempel som tillhandahålls av dig och använder samma program på övriga rader. Alla andra rader fylls i automatiskt baserat på de exempel som du angav. Arbetsstationen analyserar också dina data och försöker identifiera gränsfall. 

   > [!IMPORTANT]
   > Identifiering av gränsfall kanske inte fungerar på Mac i den aktuella versionen av Workbench. Hoppa över följande steg 3 och steg 4 på Mac. Välj i stället __OK__ när alla rader har fyllts med de härledda värdena.
   
1. Texten **Analysera data** ovanför rutnätet anger att arbetsstationen försöker identifiera gränsfall. När du är klar ändras statusen till **Granska nästa föreslagna rad** eller **Inga förslag**. I det här exemplet returneras **Granska nästa föreslagna rad**.

1. Om du vill granska de föreslagna ändringarna, välj **Granska nästa föreslagna rad**. Cellen som du bör granska och korrigera (vid behov) är markerad på skärmen.

   ![Granska nästa föreslagna rad](media/tutorial-bikeshare-dataprep/weatherreviewnextsuggested.png)

    Välj __OK__ för att acceptera omvandlingen.
 
1. Du kommer tillbaka till rutnätsvyn för data för __BostonWeather__. Rutnätet innehåller nu tre kolumner som lagts till tidigare.

   ![Rutnätsvy med tillagda rader](media/tutorial-bikeshare-dataprep/timerangecomputed.png)

   > [!TIP]
   > Alla ändringar du har gjort sparas i fönstret **Steg**. Gå till det steg som du skapade i fönstret **Steg**, klicka på nedåtpilen och välj **Redigera**. Det avancerade fönstret för **Härled kolumn efter exempel** visas. Alla exempel bevaras här. Du kan också lägga till exempel manuellt genom att dubbelklicka på en rad i rutnätet nedan. Välj **Avbryt** för att återgå till det huvudsakliga rutnätet utan att tillämpa ändringarna. Du kan också komma åt den här vyn genom att välja **Avancerat läge** när du utför en omvandling med **Härled kolumner med exempel**.

1. Om du vill byta namn på kolumnen dubbelklickar du på kolumnrubriken och skriver **Timintervall**. Tryck på Retur för att spara ändringen.

   ![Byta namn på kolumnen](media/tutorial-bikeshare-dataprep/weatherhourrangecolumnrename.png)

1. För att härleda datum- och tidsintervallet, väljer du både kolumnen **Datum\_1** och **Timintervall**. Högerklicka sedan och välj **Härled kolumner med exempel**.

   ![Härled kolumn efter exempel](media/tutorial-bikeshare-dataprep/weatherderivedatehourrange.png)

   Skriv **Jan 01, 2015 12AM-2AM** som exempel mot den första raden och välj Retur.

   Workbench fastställer transformeringen baserat på exemplet som du anger. I det här exemplet är resultatet att datumformatet ändras och sammanfogas med tvåtimmarsfönstret.

   ![Exemplet Jan 01, 2015 12AM-2AM](media/tutorial-bikeshare-dataprep/wetherdatehourrangeexample.png)

   > [!IMPORTANT]
   > På en Mac utför du följande steg i stället för steg 8:
   >
   > * Gå till den första cell som innehåller **Feb 01, 2015 12AM-2AM**. Det bör vara rad 15. Korrigera värdet till **Jan 02, 2015 12AM-2AM** och välj Retur. 
   

1. Vänta på status för att ändra från **Analysera data** till **Granska nästa föreslagna rad**. Den här ändringen kan ta flera sekunder. Välj statuslänken för att gå till den föreslagna raden. 

   ![Föreslagen rad att granska](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguate.png)

   Raden har ett null-värde eftersom källdatumvärdet kan vara antingen dd/mm/åååå eller mm/dd/åååå. Ange det korrekta värdet för **Jan 13, 2015 2AM-4AM** och välj Retur. Workbench använder de två exemplen för att förbättra härledningen på återstående rader.

   ![Korrekt formaterade data](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguated.png)

1. Välj **OK** för att acceptera omvandlingen.

   ![Slutfört transformeringsrutnät](media/tutorial-bikeshare-dataprep/weatherdatehourrangecomputed.png)

   > [!TIP]
   > Använd **Avancerat läge** för **Härled kolumner med exempel** i det här steget. välj nedåtpilen i fönstret **Steg**. I datarutnätet finns det kryssrutor bredvid kolumnerna **DATE\_1** och **Timintervall**. Avmarkera kryssrutan bredvid kolumnen **Timintervall** för att se hur detta ändrar utdatan. Om kolumnen **Timintervall** som indata saknas, behandlas **12.00–02.00** som en konstant och läggs till i de härledda värdena. Välj **Avbryt** för att återgå till det huvudsakliga rutnätet utan att tillämpa ändringarna.
   ![Avancerat läge](media/tutorial-bikeshare-dataprep/derivedcolumnadvancededitdeselectcolumn.png)

1. Om du vill byta namn på kolumnen, dubbelklickar du på rubriken. Ändra namnet till **Datum Timintervall** och tryck sedan på Retur.

1. Markera kolumnerna **DATE**, **DATE\_1**, **DATE\_2**, och **Timintervall**. Högerklicka och välj **Ta bort kolumn**.

## <a name="summarize-data-mean"></a>Sammanfatta data (medel)

Nästa steg är att sammanfatta vädret genom att ta medelvärdet av värdena, grupperat efter timintervall.

1. Välj kolumnen **Datum Timintervall** och välj sedan **Sammanfatta** från menyn **Transformeringar**.

    ![Transformeringsmenyn](media/tutorial-bikeshare-dataprep/weathersummarizemenu.png)

1. För att sammanfatta data drar du kolumner från rutnätet längst ned på sidan till vänster och höger fönster längst upp. Den vänstra panelen innehåller texten **Dra kolumner hit om du vill gruppera data**. Det högra fönstret innehåller texten **Dra kolumner hit om du vill sammanfatta data**. 

    a. Dra kolumnen **Datum Timintervall** från rutnätet längst ned i det vänstra fönstret. Dra **HOURLYDRYBULBTEMPF**, **HOURLYRelativeHumidity** och **HOURLYWindSpeed** till det högra fönstret. 

    b. I det högra fönstret väljer du **Medel** som måttet **Samlat** för varje kolumn. Välj **OK** för att slutföra sammanfattningen.

    ![Sammanfattad dataskärm](media/tutorial-bikeshare-dataprep/weathersummarize.png)

## <a name="transform-dataflow-by-using-script"></a>Transformera dataflöde med hjälp av skript

Genom att ändra data i de numeriska kolumnerna till ett intervall på 0 till 1 kan vissa modeller konvergera snabbt. Det finns för närvarande ingen inbyggd transformering för att göra detta. Använda ett Python-skript för att utföra åtgärden.

1. I menyn **Transformera** väljer du **Transformera dataflöde (skript)**.

1. Ange följande kod i textrutan som visas. Om du har använt kolumnnamnen bör koden fungera utan modifiering. Du skriver en enkel normaliseringslogik för min–max i Python.

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
    
   ![Dialogrutan Transformera dataflöde (skript)](media/tutorial-bikeshare-dataprep/transformdataflowscript.png)

1. Välj __OK__ för att använda skriptet. De numeriska kolumnerna i rutnätet innehåller nu värden i intervallet 0 till 1.

    ![Rutnät som innehåller värden mellan 0 och 1](media/tutorial-bikeshare-dataprep/datagridwithdecimals.png)

Förberedelsen av väderdata är klar. Förbered nu resedata.

## <a name="load-trip-data"></a>Läs in resedata

1. Importera filen `201701-hubway-tripdata.csv` genom att använda stegen i avsnittet [Skapa en ny datakälla](#newdatasource). Använd följande alternativ under importen:

    * __Filmarkering__: Välj **Azure Blob** när du bläddrar för att markera filen.

    * __Samplingsschema__: Välj samplingsschemat **Fullständig fil** och aktivera exemplet.

    * __Datatyp__: Acceptera standardvärdena.

1. När du har importerat data väljer du __Förbered__ för att börja förbereda informationen. Välj det befintliga paketet **BikeShare Data Prep.dprep** och välj sedan __OK__.

    Den här processen lägger till ett **Dataflöde** i den befintliga filen **Förberedelse av data** i stället för att skapa en ny.

    ![Välj det befintliga paketet](media/tutorial-bikeshare-dataprep/addjandatatodprep.png)

1. När rutnätet har lästs in expanderar du __DATAFLOWS__. Det finns nu två dataflöden: **BostonWeather** och **201701-hubway-tripdata**. Välj posten **201701-hubway-tripdata**.

    ![Posten 201701-hubway-tripdata](media/tutorial-bikeshare-dataprep/twodfsindprep.png)

## <a name="use-the-map-inspector"></a>Använda kartkontrollen

Vid dataförberedelse finns användbara visualiseringar, s.k. kontrollanter, tillgängliga för strängdata, numeriska och geografiska data. De hjälper dig att bättre förstå data och identifiera extremvärden. Följ dessa steg för att använda kartkontrollen.

1. Välj både kolumnen **latitud för startstation** och **longitud för startstation**. Högerklicka på en av kolumnerna och välj sedan **Karta**.

    > [!TIP]
    > Om du vill markera flera samtidigt håller du ned Ctrl-tangenten (Cmd ⌘ på Mac) och markerar rubriken för varje kolumn.

    ![Kartvisualisering](media/tutorial-bikeshare-dataprep/launchMapInspector.png)

1. För att maximera kartvisualiseringen, välj ikonen **Maximera**. För att passa in kartan i fönstret väljer du ikonen **E** på den översta vänstra sidan i visualiseringen.

    ![Maximerad bild](media/tutorial-bikeshare-dataprep/maximizedmap.png)

1. Klicka på knappen **Minimera** för att gå tillbaka till rutnätsvyn.

## <a name="use-the-column-statistics-inspector"></a>Använda kolumnstatistikkontroll

Om du vill använda kolumnstatistikkontroll högerklickar du på kolumnen __tripduration__ och väljer __Kolumnstatistik__.

![Kolumnstatistikpost](media/tutorial-bikeshare-dataprep/tripdurationcolstats.png)

Den här processen lägger till en ny visualisering med titeln __tripduration Statistics__ i fönstret __INSPECTORS__.

![Statistikkontroll för tripduration](media/tutorial-bikeshare-dataprep/tripdurationcolstatsinwell.png)

> [!IMPORTANT]
> Det högsta värdet för resans varaktighet är 961 814 minuter, vilket är ungefär två år. Det verkar det finns vissa avvikare i datauppsättningen.

## <a name="use-the-histogram-inspector"></a>Använda histogramkontrollen

Om du vill försöka identifiera extremvärden högerklickar du på kolumnen __tripduration__ och väljer __Histogram__.

![Histogramkontroll](media/tutorial-bikeshare-dataprep/tripdurationhistogram.png)

Histogrammet är inte användbart eftersom det finns extremvärden i diagrammet.

## <a name="add-a-column-by-using-script"></a>Lägg till en kolumn med hjälp av skript

1. Högerklicka på kolumnen **tripduration** och markera **Lägg till kolumn (skript)**.

    ![Menyn Lägg till kolumn (skript)](media/tutorial-bikeshare-dataprep/computecolscript.png)

1. I dialogrutan __Lägg till kolumn (skript)__ använder du följande värden:

    * __Nya kolumnnamn__: logtripduration

    * __Infoga den här nya kolumn efter__: tripduration

    * __Ny kolumnkod__: `math.log(row.tripduration)`

    * __Kodblocktyp__: Uttryck

   ![Dialogrutan Lägg till kolumn (skript)](media/tutorial-bikeshare-dataprep/computecolscriptdialog.png)

1. Välj __OK__ för att lägga till kolumnen **logtripduration**.

1. Högerklicka på kolumnen och välj **Histogram**.

    ![Histogram för kolumnen logtripduration](media/tutorial-bikeshare-dataprep/logtriphistogram.png)

    Det här histogrammet verkar visuellt sett vara en normalfördelning med ett onormalt slut.

## <a name="use-an-advanced-filter"></a>Använda ett avancerat filter

Genom att använda ett filter på data uppdateras kontrollerna med den nya distributionen. 

1. Högerklicka på kolumnen **logtripduration** och välj **Filtrera kolumn**. 

1. I dialogrutan __Redigera__ använder du följande värden:

    * __Filtrera den här talkolumnen__: logtripduration

    * __Jag vill__: Behålla rader

    * __När__: Något av villkoren nedan är Sant (logiskt ELLER)

    * __Om den här kolumnen__: är mindre än

    * __Värdet__: 9

    ![Filtrera alternativ](media/tutorial-bikeshare-dataprep/loftripfilter.png)

1. Välj __OK__ för att tillämpa filtret.

    ![Uppdaterade histogram efter filter används](media/tutorial-bikeshare-dataprep/loftripfilteredinspector.png)

### <a name="the-halo-effect"></a>Halo-effekt

1. Maximera histogrammet **logtripduration**. Det finns ett blått histogram ovanpå ett grått histogram. Den här vyn kallas **Halo-effekt**:

    * Det grå histogrammet representerar fördelningen före åtgärden (i det här fallet filtreringen).

    * Det blå histogrammet representerar histogrammet efter åtgärden. 

   Halo-effekten hjälper att visualisera effekten av en åtgärd på data.

   ![Halo-effekt](media/tutorial-bikeshare-dataprep/loftripfilteredinspectormaximized.png)

    > [!NOTE]
    > Det blå histogrammet verkar vara kortare jämfört med föregående. Detta beror på en ny automatisk bucket av data i det nya området.

1. Om du vill ta bort halon väljer du __Redigera__ och avmarkerar __Visa halo__.

    ![Alternativ för histogrammet](media/tutorial-bikeshare-dataprep/uncheckhalo.png)

1. Välj **OK** att inaktivera halo-effekten. Minimera sedan histogrammet.

### <a name="remove-columns"></a>Ta bort kolumner

I resedata representerar varje rad en cykelhämtningshändelse. I den här självstudien behöver du bara kolumnerna **starttime** och **start station id**. Ta bort de andra kolumnerna genom att markera dessa två kolumner samtidigt, högerklicka på kolumnrubriken och välj sedan **Behåll kolumn**. Andra kolumner tas bort.

![Alternativet Behåll kolumn](media/tutorial-bikeshare-dataprep/tripdatakeepcolumn.png)

### <a name="summarize-data-count"></a>Sammanfatta data (antal)

Använd härledda kolumner för att sammanfatta cykelefterfrågan för en tvåtimmarsperiod.

1. Högerklicka på kolumnen **starttime** och välj **Härled kolumner med exempel**.

    ![Alternativet Härled kolumner med exempel](media/tutorial-bikeshare-dataprep/tripdataderivebyexample.png)

1. För det här exemplet anger du värdet **Jan 01, 2017 12AM-2AM** för den första raden.

    > [!IMPORTANT]
    > I det förra exemplet för att härleda kolumner används flera steg för att härleda en kolumn som innehåller datum- och tidsperioden. I det här exemplet ser du att den här åtgärden kan utföras som ett enda steg genom att tillhandahålla ett exempel på det slutgiltiga resultatet.

    > [!NOTE]
    > Du kan ge ett exempel mot någon av raderna. För det här exemplet är värdet **Jan 01, 2017 12AM-2AM** giltigt för den första dataraden.

    ![Exempeldata](media/tutorial-bikeshare-dataprep/tripdataderivebyexamplefirstexample.png)

   > [!IMPORTANT]
   > På en Mac följer du detta steg i stället för steg 3:
   >
   > * Gå till den första cell som innehåller **Jan 01, 2017 1AM-2AM**. Det bör vara rad 14. Korrigera värdet till **Jan 01, 2017 12AM-2AM** och välj Retur. 

1. Vänta tills programmet beräknar värden mot alla rader. Processen kan ta flera sekunder. När analysen är klar kan du använda länken __Granska nästa föreslagna rad__ för att granska data.

   ![Slutförd analys med granskningslänk](media/tutorial-bikeshare-dataprep/tripdatabyexanalysiscomplete.png)

    Se till att de beräknade värdena är korrekta. Om inte uppdaterar du värdet med det förväntade värdet och trycker på Retur. Vänta sedan på att analysen slutförs. Slutför processen **Granska nästa föreslagna rad** tills du ser **Inga förslag**. **Inga förslag** innebär att programmet har tittat på gränsfall och är nöjt med det syntetiserade programmet. Det är bäst att utföra en visuell granskning av transformerade data innan du godkänner transformeringen. 

1. Välj **OK** för att acceptera omvandlingen. Byt namn på den nyligen skapade kolumnen till **Datum Timintervall**.

    ![Omdöpt kolumn](media/tutorial-bikeshare-dataprep/tripdatasummarize.png)

1. Högerklicka på kolumnrubriken **starttime** och välj **Ta bort kolumn**.

1. Om du vill sammanfatta data går du till menyn __Transformera__ och väljer __Sammanfatta__. Använd följande steg för att skapa transformeringen:

    * Dra __Datum Timintervall__ och __start station id__ till fönstret **Gruppera efter** till vänster.

    * Dra __start station id__ till fönstret **sammanfatta data** till höger.

   ![Sammanfattningsalternativ](media/tutorial-bikeshare-dataprep/tripdatacount.png)

1. Välj **OK** för att acceptera sammanfattningsresultaten.

## <a name="join-dataflows"></a>Slå ihop dataflöden

Använd följande steg för att slå ihop väderdata med resedata:

1. I menyn __Transformeringar__ väljer du __Anslut__.

1. __Tabeller__: Välj **BostonWeather** som det **vänstra** dataflödet och **201701-hubway-tripdata** som det **högra** dataflödet. Välj **Nästa** för att fortsätta.

    ![Tabellval](media/tutorial-bikeshare-dataprep/jointableselection.png)

1. __Nyckelkolumner__: Välj kolumnen **Datum Timintervall** i båda tabellerna och välj sedan __Nästa__.

    ![Nyckelkolumnval](media/tutorial-bikeshare-dataprep/joinkeyselection.png)

1. __Kopplingstyp__: Välj __Matchande rader__ som kopplingstyp och välj sedan __Slutför__.

    ![Matchande rader som kopplingstyp](media/tutorial-bikeshare-dataprep/joinscreen.png)

    Den här processen skapar ett nytt dataflöde med namnet __Kopplingsresultat__.

## <a name="create-additional-features"></a>Skapa ytterligare funktioner

1. Om du vill skapa en kolumn som innehåller veckodagen, högerklicka på kolumnen **Datum Timintervall** och markera **Härled kolumner med exempel**. Använd värdet __Sun__ för ett datum som inträffat på söndag. Till exempel **Jan 01, 2017 12AM-2AM**. Tryck på Retur och välj sedan **OK**. Byt namn på den här kolumnen till __Veckodag__.

    ![Skapa en ny kolumn för veckodagen](media/tutorial-bikeshare-dataprep/featureweekday.png)

1. Om du vill skapa en kolumn som innehåller tidsperioden för raden, högerklicka på kolumnen **Datum Timintervall** och markera **Härled kolumner med exempel**. Använd värdet **12AM-2AM** för den rad som innehåller **Jan 01, 2017 12AM-2AM**. Tryck på Retur och välj sedan **OK**. Byt namn på den här kolumnen till **Period**.

    ![Periodkolumn](media/tutorial-bikeshare-dataprep/featurehourrange.png)

1. Om du vill ta bort kolumnerna **Datum Timintervall** och **r_Datum Timintervall** väljer du Ctrl (Cmd ⌘ på Mac) och markerar varje kolumnrubrik. Högerklicka och välj **Ta bort kolumn**.

## <a name="read-data-from-python"></a>Läs data från Python

Du kan köra ett paket för förberedelse av data från Python eller PySpark och hämta resultatet som en **Dataram**.

Om du vill generera ett Python-skriptexempel, högerklicka på __BikeShare Data Prep__ och välj __Generera kodfil för dataåtkomst__. Python-exempelfilen skapas i din **Projektmapp** och läses även in på en flik i Workbench. Följande skript i Python är ett exempel på den kod som genererades:

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

För att spara dataflöde för **Koppla resultat** till en .csv-fil måste du ändra skriptet `BikeShare Data Prep.py`. 

1. Öppna projektet för redigering i Visual Studio Code.

    ![Öppna projektet i Visual Studio Code](media/tutorial-bikeshare-dataprep/openprojectinvscode.png)

1. Uppdatera Python-skriptet i filen `BikeShare Data Prep.py` med följande kod:

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

1. Ersätt `Your Azure Storage blob path` med sökvägen till utdatafilen som ska skapas. Ersätt båda variablerna `blobfolder` och `csvfiles`.

## <a name="create-an-hdinsight-run-configuration"></a>Skapa en konfiguration för HDInsight-körningen

1. Öppna kommandoradsfönstret i Machine Learning Workbench, välj menyn **Arkiv** och välj **Öppna kommandotolken**. Kommandotolken startar i projektmappen med prompten `C:\Projects\BikeShare>`.

    ![Öppna kommandotolken](media/tutorial-bikeshare-dataprep/opencommandprompt.png)

   >[!IMPORTANT]
   >Du måste använda kommandoradsfönstret (som öppnas från Workbench) för att utföra följande steg.

1. Använd kommandotolken för att logga in på Azure. 

   Workbench-appen och CLI använder oberoende cacheminnen för inloggningsuppgifter vid autentisering mot Azure-resurser. Du behöver bara göra det här en gång, tills token i cacheminnet upphör att gälla. Kommandot `az account list` returnerar listan med prenumerationer som är tillgängliga för din inloggning. Om det finns fler än en använder du ID-värdet från prenumerationen som du vill använda. Ange den prenumerationen som standardkonto med kommandot `az account set -s` och ange sedan ditt prenumerations-ID. Bekräfta sedan inställningen med kontokommandot `show`.

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

1. Skapa konfiguration för HDInsight-körning. Du måste ha namnet på klustret och `sshuser`-lösenordet.

    ```azurecli
    az ml computetarget attach cluster --name hdinsight --address <yourclustername>.azurehdinsight.net --username sshuser --password <your password>
    az ml experiment prepare -c hdinsight
    ```
> [!NOTE]
> När du har skapat ett tomt projekt är standardkonfigurationerna **lokala** och **Docker**. Det här steget skapar en ny körningskonfiguration som är tillgänglig i arbetsstationen Workbench när du kör skripten. 

## <a name="run-in-an-hdinsight-cluster"></a>Kör i ett HDInsight-kluster

Gå tillbaka till i Machine Learning Workbench-programmet för att köra skriptet i HDInsight-klustret.

1. Återgå till startsidan i ditt projekt genom att välja ikonen **Start** till vänster.

1. Välj **hdinsight** från listrutan för att köra skriptet i HDInsight-klustret.

1. Välj **Kör**. Skriptet skickas in som ett jobb. Jobbstatusen ändras till __Slutförd__ när filen har skrivits till den angivna platsen i lagringscontainern.

    ![Körningsskript för HDInsight](media/tutorial-bikeshare-dataprep/hdinsightrunscript.png)


## <a name="substitute-data-sources"></a>Ersätt datakällor

I föregående steg använde du datakällorna `201701-hubway-tripdata.csv` och `BostonWeather.csv` för att förbereda testdata. Om du vill använda paketet med andra resedatafiler, använder du följande steg:

1. Skapa en ny datakälla med hjälp av anvisningarna som gavs tidigare, med följande ändringar i processen:

    * __Filval__: När du väljer en fil kan du välja de sex återstående .csv-filerna med tripdata för resan samtidigt.

    ![Läs in de sex återstående filerna](media/tutorial-bikeshare-dataprep/browseazurestoragefortripdatafiles.png)

    > [!NOTE]
     > Posten __+ 5__ indikerar att det finns fem ytterligare filer utöver den som visas.

    * __Filinformation__: Ange __Befordra rubrikläge__ till **Alla filer har samma rubriker**. Det här värdet anger att var och en av filerna innehåller samma rubrik.

    ![Val av filinformation](media/tutorial-bikeshare-dataprep/headerfromeachfile.png) 

   Spara namnet på den här datakällan eftersom den används i senare steg.

1. Välj mappikonen för att visa filerna i projektet. Expandera katalogen __aml\_config__ och välj sedan filen `hdinsight.runconfig`.

    ![Plats för hdinsight.runconfig](media/tutorial-bikeshare-dataprep/hdinsightsubstitutedatasources.png) 

1. Välj knappen **Redigera** för att öppna filen i Visual Studio Code.

1. Lägg till följande rader i slutet av filen `hdinsight.runconfig` och välj diskikonen för att spara filen.

    ```yaml
    DataSourceSubstitutions:
      201701-hubway-tripdata.dsource: 201501-hubway-tripdata.dsource
    ```

    Den här ändringen ersätter den ursprungliga datakällan med den som innehåller de sex resedatafilerna.

## <a name="save-training-data-as-a-csv-file"></a>Spara träningsdata som en CSV-fil

1. Bläddra till Python-filen `BikeShare Data Prep.py` som du redigerade tidigare. Ange en annan sökväg där du kan spara utbildningsdatan.

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

1. Använd mappen med namnet `traindata` för utbildningsdatans utdata.

1. Om du vill skicka ett nytt jobb väljer du **Kör**. Kontrollera att **hdinsight** är valt. Ett jobb skickas med den nya konfigurationen. Utdata för jobbet är utbildningsdatan. Dessa data skapas med hjälp av samma steg för förberedelse av data som du följde tidigare. Det kan ta några minuter att slutföra jobbet.


## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg
Du har slutfört självstudien för dataförberedelse av BikeShare. I den här självstudien använder du Machine Learning (förhandsversion) till att lära dig att:
> [!div class="checklist"]
> * Förbereda data interaktivt med förberedelseverktyget för Machine Learning-data.
> * Importera, transformera och skapa en testdatamängd.
> * Skapa ett paket för förberedelse av data.
> * Köra paketet för förberedelse av data med Python.
> * Generera en datamängd för träning genom att återanvända paketet för förberedelse av data för ytterligare inkommande filer.

Härnäst, lär dig mer om förberedelse av data:
> [!div class="nextstepaction"]
> [Användarhandbok för förberedelse av data](data-prep-user-guide.md)
