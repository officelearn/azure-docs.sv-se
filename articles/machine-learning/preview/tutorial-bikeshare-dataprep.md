---
title: "Cykeln filresurs kursen - förberedelse av avancerade data med Azure Machine Learning arbetsstationen"
description: "Och förberedelse självstudier för slutpunkt till slutpunkt-data med hjälp av Azure Machine Learning arbetsstationen"
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
ms.openlocfilehash: e85515c29d8f626c7eb2bfb636dc6c18da78b5c5
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="bike-share-tutorial-advanced-data-preparation-with-azure-machine-learning-workbench"></a>Cykeln filresurs Självstudier: avancerade förberedelse av data med Azure Machine Learning arbetsstationen
Azure Machine Learning-tjänster (förhandsversion) är en integrerad, slutpunkt-till-slutpunkt datavetenskap och avancerade analyser lösning för professionella datavetare och förbereda data, utveckla experiment och distribuera modeller i molnskala.

I kursen får du använder Azure Machine Learning services (förhandsvisning) att lära dig hur du:
> [!div class="checklist"]
> * Förbereda data interaktivt med förberedelseverktyget för Azure Machine Learning Data
> * Importera, transformera och skapa en test-datamängd
> * Skapa ett paket för förberedelse av Data
> * Kör förberedelse datapaketet använder Python
> * Generera en datauppsättning för träning genom att återanvända paketet förberedelse av Data för ytterligare inkommande filer

> [!IMPORTANT]
> Den här självstudiekursen bara förbereds data, den skapa inte modellen förutsägelse.
>
> Du kan använda förberedda data för att träna förutsägelse modeller. Du kan till exempel skapa en modell för att förutsäga cykel begäran under en 2 timmar.

## <a name="prerequisites"></a>Krav
* Azure Machine Learning arbetsstationen måste vara installerad lokalt. Mer information i [installation Quickstart](quickstart-installation.md).
* Om du är bekant med att skapa ett nytt projekt i arbetsstationen.

## <a name="data-acquisition"></a>Datainsamling
Den här kursen använder den [Boston Hubway dataset](https://s3.amazonaws.com/hubway-data/index.html) och Boston väder data från [NOAA](http://www.noaa.gov/).

1. Hämta datafiler från följande länkar till din lokala utvecklingsmiljö. 
   * [Boston väder data](https://azuremluxcdnprod001.blob.core.windows.net/docs/azureml/bikeshare/BostonWeather.csv). 
   * Hubway resa data från Hubway webbplats.

      - [201501-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201501-hubway-tripdata.zip)
      - [201504-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201504-hubway-tripdata.zip)
      - [201510-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201510-hubway-tripdata.zip)
      - [201601-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201601-hubway-tripdata.zip)
      - [201604-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201604-hubway-tripdata.zip)
      - [201610-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201610-hubway-tripdata.zip)
      - [201701-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201701-hubway-tripdata.zip)

2. Packa upp varje ZIP-filen efter nedladdning.

## <a name="learn-about-the-datasets"></a>Lär dig mer om datauppsättningar
1. Den __Boston väder__ filen innehåller följande väder-relaterade fält rapporteras på timme:
   * Datum
   * REPORTTPYE
   * HOURLYDRYBULBTEMPF
   * HOURLYRelativeHumidity
   * HOURLYWindSpeed

2. Den __Hubway__ ordnas data i filer per år och månad. Till exempel filen `201501-hubway-tripdata.zip` innehåller en CSV-fil som innehåller data för januari 2015. Data innehåller följande fält för varje rad som representerar en cykel resa:

   * Resa varaktighet (i sekunder)
   * Starttiden och startdatumet
   * Stoppa tid och datum
   * Starta Stationsnamn & ID
   * End Stationsnamn & ID
   * Cykel-ID
   * Användartyp (Avslappnad = 24 timmar eller 72 timmar Pass-användaren. Medlemmen = årliga eller varje månad)
   * Postnummer (om användaren är medlem)
   * Kön (automatisk rapporterat av medlem)

## <a name="create-a-new-project"></a>Skapa ett nytt projekt
1. Starta den **Azure Machine Learning arbetsstationen** från start-menyn eller starta.

2. Skapa ett nytt Azure Machine Learning-projekt.  Klicka på den  **+**  knappen på den **projekt** sidan eller **filen** > **ny**.
   - Använd den **tomt projekt** mall.
   - Namnge projektet **BikeShare**. 

## <a id="newdatasource"></a>Skapa en ny datakälla

1. Skapa en ny datakälla. Klicka på den **Data** i verktygsfältet (cylinder ikonen) vänstra. Visar den **datavy**.

   ![Bild av fliken vyn data](media/tutorial-bikeshare-dataprep/navigatetodatatab.png)

2. Lägg till en datakälla. Välj den  **+**  ikon och väljer sedan **Lägg till datakälla**.

   ![Bild av posten Lägg till datakälla](media/tutorial-bikeshare-dataprep/newdatasource.png)

## <a name="add-weather-data"></a>Lägg till väder data

1. **Datalagret**: Välj datalager som innehåller data. Eftersom du använder filer, markerar **fil(er) / Directory**. Välj **nästa** att fortsätta.

   ![Bild av filen eller filerna / katalogposten](media/tutorial-bikeshare-dataprep/datasources.png)

2. **Filen val**: lägga till väder-data. Bläddra och välj den `BostonWeather.csv` filen som du hämtade tidigare. Klicka på **Nästa**.

   ![Bild av filval med BostonWeater.csv valt](media/tutorial-bikeshare-dataprep/pickweatherdatafile.png)

3. **Filinformation**: verifiera filen schemat som upptäcks. Azure Machine Learning arbetsstationen analyserar data i filen och härleder schemat ska användas.

   ![Bild av filinformation](media/tutorial-bikeshare-dataprep/fileparameters.png)

   > [!IMPORTANT]
   > Arbetsstationen identifieras inte korrekt schema i vissa fall. Du bör alltid kontrollera att parametrarna är korrekta för datauppsättningen. Kontrollera att de är inställda på följande värden för väder-data:
   >
   > * __Filtyp__: fil fil (csv, TVs, txt, etc.)
   > * __Avgränsare__: kommatecken [,]
   > * __Kommentera tecknet__: inget värde har angetts.
   > * __Hoppa över rader läge__: Hoppa inte över
   > * __Filen kodning__: utf-8
   > * __Befordra huvuden läge__: Använd rubriker från första filen

   Förhandsgranskning av data ska visas följande kolumner:
   * **Sökväg**
   * **DATUM**
   * **REPORTTYPE**
   * **HOURLYDRYBULBTEMPF**
   * **HOURLYRelativeHumidity**
   * **HOURLYWindSpeed**

   Om du vill fortsätta, Välj **nästa**.

4. **Datatyper**: granska de datatyper som identifieras automatiskt. Azure Machine Learning arbetsstationen analyserar data i filen och härleder datatyperna som ska använda.

   Dessa data, ändra den `DATA TYPE` för alla kolumner till `String`.

   > [!NOTE]
   > `String`används för att markera funktionerna i arbetsstationen senare i den här kursen. 

   ![Bild av datatyper](media/tutorial-bikeshare-dataprep/datatypedetection.png)

   Om du vill fortsätta, Välj __nästa__. 

5. **Provtagning**: Om du vill skapa ett schema för provtagning, Välj den **+ ny** knappen. Välj den nya __upp 10000__ rad som har lagts till och markera sedan __redigera__. Ange __exempel strategi__ till **fullständig**, och välj sedan **tillämpa**.

   ![Bild för att lägga till en ny provtagning](media/tutorial-bikeshare-dataprep/weatherdatasampling.png)

   Att använda den __fullständig__ strategi, Välj den __fullständig__ posten och välj sedan __inställd som aktiv__. En stjärna visas bredvid __fullständig__ som indikerar att den aktiva strategin.

   ![Bild av fullständiga filen som aktiv strategin](media/tutorial-bikeshare-dataprep/fullfileactive.png)

   Om du vill fortsätta, Välj **nästa**.

6. **Kolumnen sökväg**: den __kolumnen sökväg__ avsnitt kan du inkludera den fullständiga sökvägen som en kolumn i den importerade informationen. Välj __ingår inte i kolumnen sökväg__.

   > [!TIP]
   > Inklusive sökväg som en kolumn är användbart om du importerar en mapp med många filer med olika namn. Det är också användbart om filnamnen innehåller information som du vill extrahera senare.

   ![Bild av värdet ingår inte i kolumnen sökväg](media/tutorial-bikeshare-dataprep/pathcolumn.png)

7. **Slutför**: Slutför datakällan, välja den **Slutför** knappen.

    En ny datakälla flik med namnet __BostonWeather__ öppnas. Ett exempel på data visas i ett rutnät. Exemplet baseras på aktiva samplingsfrekvensen schemat som angavs tidigare.

    Observera den **steg** rutan till höger på skärmen visar de enskilda åtgärder som vidtas när du skapar den här datakällan.

   ![Bild som visar datakällan, exempel och steg](media/tutorial-bikeshare-dataprep/weatherdataloaded.png)

### <a name="view-data-source-metrics"></a>Visa datakälla mått

Välj den __mått__ längst upp till vänster i på fliken rutnätsvy. Den här vyn visar fördelningen och andra statistik samplade data.

![Bild av mätvärdena visar](media/tutorial-bikeshare-dataprep/weathermetrics.png)

> [!NOTE]
> Om du vill konfigurera synligheten för statistik, använda den **välja mått** listrutan. Kontrollera och avmarkera det mått om du vill ändra diagramvyn.

Returnera den __datavy__väljer __Data__ från det övre vänstra hörnet på sidan.

## <a name="add-data-source-to-data-preparation-package"></a>Lägga till datakälla paket för förberedelse av data

1. Välj __Förbered__ att börja förbereda informationen. 

2. När du uppmanas, anger du ett namn för förberedelse av Data-paketet som `BikeShare Data Prep`. 

3. Välj __OK__ att fortsätta.

   ![Bild av dialogrutan Förbered](media/tutorial-bikeshare-dataprep/dataprepdialog.png)

4. Ett nytt paket med namnet **BikeShare Data Prep** visas under __förberedelse av Data__ avsnitt i den __Data__ fliken. 

   Visa paketet, markera den här posten. 

5. Välj den  **>>**  för att expandera för att visa den __Dataflows__ som ingår i paketet. I det här exemplet __BostonWeather__ är den enda dataflöde.

   > [!IMPORTANT]
   > Ett paket kan innehålla flera Dataflows.

   ![Bild av dataflows som ingår i paketet](media/tutorial-bikeshare-dataprep/weatherdataloadedingrid.png)

## <a name="filter-data-by-value"></a>Filtrera data efter värde
1. Filtrera data, högerklicka på en cell med ett visst värde och välj __Filter__, och sedan vilken typ av filter.

2. Markera en cell som innehåller värdet för den här självstudiekursen `FM-15` och sedan anger du filtret till ett filter för **är lika med**.  Nu data filtreras till endast returnerar rader där den __REPORTTYPE__ är `FM-15`.

   ![Bild av dialogrutan filter](media/tutorial-bikeshare-dataprep/weatherfilterinfm15.png)

   > [!NOTE]
   > RM-15 är en typ av meteorologiska Terminal luftfart rutinunderhåll väder rapport (METAR). Rapporterar FM 15 observeras empiriskt kan vara den mest fullständig, lite data som saknas.

## <a name="remove-a-column"></a>Ta bort en kolumn

Du behöver inte längre den __REPORTTYPE__ kolumn. Högerklicka på kolumnrubriken och välj **ta bort kolumnen**.

   ![Bild av alternativet Ta bort kolumnen](media/tutorial-bikeshare-dataprep/weatherremovereporttype.png)

## <a name="change-datatypes-and-remove-errors"></a>Ändra datatyper och ta bort fel
1. När du trycker på __Ctrl (kommandot ⌘ på Mac)__ med att välja kolumnrubriker kan du markera flera kolumner på samma gång. Använd detta för att välja följande kolumnrubriker:
   * **HOURLYDRYBULBTEMPF**
   * **HOURLYRelativeHumidity**
   * **HOURLYWindSpeed**

2. **Högerklicka på** en av de valda kolumnrubriker och välj **konvertera fälttypen till numeriska**. Detta omvandlar datatyp för kolumnerna till numeriska.

   ![Konvertera flera kolumner till numeriska](media/tutorial-bikeshare-dataprep/weatherconverttonumeric.png)

3. Filtrera ut felvärdena. Vissa kolumner har problem med konverteringen av datatyp. Det här problemet visas med röd färg i den __kvalitet datastapel__ för kolumnen.

   Om du vill ta bort de rader som innehåller fel, högerklicka på den **HOURLYDRYBULBTEMPF** kolumnrubriken. Välj **Filterkolumn**. Använd standard **jag vill att** som **Behåll rader**. Ändra den **villkor** listrutan och välj **är inte ett fel**. Välj **OK** att tillämpa filtret.

4. För att minimera de återstående raderna med fel i de övriga kolumnerna måste du upprepa proceduren filter för **HOURLYRelativeHumidity** och **HOURLYWindSpeed** kolumner.

## <a name="use-by-example-transformations"></a>Använda som exempel omvandlingar

Om du vill använda data i en förutsägelse för två-timmarsformat block, måste du beräkna genomsnittliga väderlek villkoren för två timmar punkter. Om du vill göra detta måste använda du följande åtgärder:

* Dela den **datum** kolumn i separata **datum** och **tid** kolumner. Se avsnittet nästa detaljerade anvisningar.

* Härleda ett **Hour_Range** kolumnen från den **tid** kolumn. Se följande avsnitt för detaljerade anvisningar.

* Härledd en **datum\_timme\_intervallet** kolumnen från den **datum** och **Hour_Range** kolumner. Se följande avsnitt för detaljerade anvisningar.

### <a name="split-column-by-example"></a>Dela kolumner med exempel

1. Dela den **datum** kolumn till separata datum- och time-kolumner. Högerklicka på den **datum** kolumnrubriken och välj **delad kolumn efter exempel**.

   ![Bild av kolumnen delning av exempel på post](media/tutorial-bikeshare-dataprep/weathersplitcolumnbyexample.png)

2. Azure Machine Learning arbetsstationen automatiskt identifierar en avgränsare för en meningsfull och skapar två kolumner genom att dela data i datum- och tidsvärden. 

3. Välj __OK__ att acceptera Åtgärdsresultat dela.

   ![Bild av dela kolumnerna DATE_1 och DATE_2](media/tutorial-bikeshare-dataprep/weatherdatesplitted.png)

### <a name="derive-column-by-example"></a>Härled kolumner med exempel

1. För att härleda en två timmars intervall, högerklicka på den __datum\_2__ kolumnrubriken och välj **härledd kolumn efter exempel**.

   ![Bild av härledd kolumn av exempel på post](media/tutorial-bikeshare-dataprep/weatherdate2range.png)

   En ny tom kolumn läggs med null-värden.

2. Klicka på den första tom cellen i kolumnen nytt. Ge ett exempel på det tidsintervall som önskas genom att skriva `12AM-2AM` i den nya kolumnen och tryck sedan på Ange.

   ![Bild av den nya kolumnen med ett värde av 12: 00 - 02: 00](media/tutorial-bikeshare-dataprep/weathertimerangeexample.png)

   > [!NOTE]
   > Azure ML-arbetsstationen Syntetiserar program baserat på de exempel som tillhandahålls av du och använder samma program på övriga rader. Alla andra rader fylls i automatiskt baserat på de exempel som du angav. Arbetsstationen också analyserar dina data och försöker identifiera edge fall. 
  
3. Texten **analysera Data** ovanför rutnätet anger att arbetsstationen försöker identifiera edge fall. När du är klar status ändras till **granska nästa föreslagna rad** eller **inga förslag**. I det här exemplet **granska nästa föreslagna rad** returneras.

4. Om du vill granska de föreslagna ändringarna, Välj **granska nästa föreslagna rad**. Cellen som du bör granska och korrigera (vid behov) är markerad på skärmen.

   ![Bild av granska raden](media/tutorial-bikeshare-dataprep/weatherreviewnextsuggested.png)

    Välj __OK__ att acceptera omvandlingen.
 
5. Du kommer tillbaka till diagramvyn för __BostonWeather__. Rutnätet innehåller nu tre kolumner som lagts till tidigare.

   ![Bild av rutnätsvy med tillagda rader](media/tutorial-bikeshare-dataprep/timerangecomputed.png)

   > [!TIP]
   >  Alla ändringar du har gjort sparas på den **steg** fönstret. Gå till steg som du skapade i den **steg** klickar du på nedpilen och välj **redigera**. Fönstret Avancerade för **härledd kolumn efter exempel** visas. Alla exempel bevaras här. Du kan också lägga till exempel manuellt genom att dubbelklicka på en rad i rutnätet nedan. Välj **Avbryt** att återgå till huvudsakliga rutnätet utan att tillämpa ändringar. Du kan också komma åt den här vyn genom att välja **Avancerat läge** när du utför en **härledd kolumn efter exempel** transformeringen.

6. Om du vill byta namn på kolumnen, dubbelklickar du på kolumnrubriken och skriv **timme intervallet**. Tryck på **RETUR** att spara ändringen.

   ![Byta namn på kolumnen](media/tutorial-bikeshare-dataprep/weatherhourrangecolumnrename.png)

7. Att härleda datum och tid intervallet, välja flera den **datum\_1** och **timme intervallet** kolumner, högerklicka och välj sedan **härledd kolumn efter exempel**.

   ![Härledda kolumn efter exempel](media/tutorial-bikeshare-dataprep/weatherderivedatehourrange.png)

   Typen `Jan 01, 2015 12AM-2AM` exempel mot den första raden och tryck på **RETUR**.

   Arbetsstationen anger transformation som baseras på exemplet som du anger. I det här exemplet är resultatet att datumformatet ändras och användas i sammanslagningen av fönstret två timmar.

   ![Bild av exemplet ' 01 januari 2015 12: 00 - 02: 00](media/tutorial-bikeshare-dataprep/wetherdatehourrangeexample.png)


8. Vänta tills status för att ändra från **analysera Data** till **granska nästa föreslagna rad**. Det kan ta flera sekunder. Välj länken status för att navigera till den föreslagna raden. 

   ![Bild av den föreslagna raden att granska](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguate.png)

   Raden har ett null-värde eftersom källan date-värde kan vara för dd/mm/åååå eller mm/dd/åååå. Ange det korrekta värdet för `Jan 13, 2015 2AM-4AM` och tryck på **RETUR**. Arbetsstationen använder de två exemplen för att förbättra härledning för återstående rader.

   ![Bild av korrekt formaterad data](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguated.png)

9. Välj **OK** att acceptera transformeringen.

   ![Bild av slutförda omvandling rutnätet](media/tutorial-bikeshare-dataprep/weatherdatehourrangecomputed.png)

   > [!TIP]
   > Du kan använda Avancerat läge av **härledd kolumn efter exempel** för det här steget genom att klicka på pilen i den **steg** fönstret. Det finns kryssrutorna bredvid kolumnnamnen i datarutnätet, **datum\_1** och **timme intervallet** kolumner. Avmarkera kryssrutan bredvid den **timme intervallet** kolumnen att se hur detta ändrar utdata. Om den **timme intervallet** kolumnen som indata, **12: 00 - 02: 00** behandlas som en konstant och läggs till i de härledda värdena. Välj **Avbryt** att återgå till huvudsakliga rutnätet utan att tillämpa ändringarna.

10. Om du vill byta namn på kolumnen, dubbelklickar du på rubriken. Ändra namnet till **timme datumintervall** och tryck sedan på **RETUR**.

11. Markera den **datum**, **datum\_1**, **datum\_2**, och **timme intervallet** kolumner. Högerklicka och välj **ta bort kolumnen**.

## <a name="summarize-data-mean"></a>Sammanfatta data (medel)

Nästa steg är att sammanfatta väder genom att göra medelvärdet av värden, grupperade efter timmars intervall.

1. Välj den **timme datumintervall** kolumn och välj sedan **Summarize** från den **transformerar** menyn.

    ![Transformerar menyn](media/tutorial-bikeshare-dataprep/weathersummarizemenu.png)

2. För att sammanfatta data dra kolumner i rutnätet längst ned på sidan till vänster och höger fönsterruta längst upp. Den vänstra rutan innehåller texten **dra kolumner hit om du vill gruppera data**. Den högra rutan innehåller texten **dra kolumner hit kan du sammanfatta data**. 

    Dra den **timme datumintervall** kolumn från rutnätet längst ned i den vänstra rutan. Dra **HOURLYDRYBULBTEMPF**, **HOURLYRelativeHumidity**, och **HOURLYWindSpeed** till det högra fönstret. 

    I den högra rutan, Välj **innebär** som den **sammanställd** för varje kolumn. Klicka på **OK** att slutföra sammanfattningen.

   ![Bild av sammanfattningsdata skärmen](media/tutorial-bikeshare-dataprep/weathersummarize.png)

## <a name="transform-dataflow-using-script"></a>Transformera dataflöde med hjälp av skript

Ändra data i numeriska kolumner till ett intervall av 0-1 kan vissa modeller att Konvergera snabbt. Det finns för närvarande ingen inbyggd transformation Allmänt göra transformationen men Python-skriptet kan användas för att utföra den här åtgärden.

1. Från den **transformera** väljer du **transformera dataflöde**.

2. Ange följande kod i textrutan som visas. Om du har använt kolumnnamnen bör koden fungera utan modifiering. Du skriver en enkel min max normalisering logik i Python.

    > [!WARNING]
    > Skriptet förväntar kolumnnamnen använt tidigare i den här självstudiekursen. Om du har olika kolumnnamn måste du ändra namn i skriptet.

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
    
    ![Transformera data flödet dialogruta för skript](media/tutorial-bikeshare-dataprep/transformdataflowscript.png)

3. Välj __OK__ du använder skriptet. Numeriska kolumner i rutnätet innehåller nu värden i intervallet 0-1.

    ![Rutnät som innehåller värden mellan 0 och 1](media/tutorial-bikeshare-dataprep/datagridwithdecimals.png)

Förbereder väder-data är klar. Förbered data för kommunikation.

## <a name="load-trip-data"></a>Läs in resa data

1. Så här importerar du den `201701-hubway-tripdata.csv` fil, Följ stegen i den [skapa en ny datakälla](#newdatasource) avsnitt. Använd följande alternativ under importen:

    * __Sampling schemat__: **fullständig** provtagning schemat, aktivera exemplet, och 
    * __Datatypen__: Godkänn standardinställningarna.

2. När du har importerat data, Välj den __Förbered__ om du vill börja förbereda data. Välj den befintliga **BikeShare Data Prep.dprep** paketet och välj sedan __OK__.

    Den här processen lägger till en **Dataströmsblocket** i den befintliga **förberedelse av Data** fil i stället för att skapa en ny.

    ![Bild av du väljer det befintliga paketet](media/tutorial-bikeshare-dataprep/addjandatatodprep.png)

3. När rutnätet har lästs in, expandera __DATAFLOWS__. Det finns nu två dataflows: **BostonWeather** och **201701-hubway-tripdata**. Välj den **201701-hubway-tripdata** transaktionen.

    ![Bild av 201701-hubway-tripdata-post](media/tutorial-bikeshare-dataprep/twodfsindprep.png)

## <a name="use-map-inspector"></a>Använd kartan inspector

Det finns ett antal användbara visualiseringar kallas för förberedelse av data **kontrollanter** för String, numeriska och geografiska data att förstå bättre data och för att identifiera avvikare. Använd följande steg för att använda kartan inspector:

1. Väljer du den **starta station latitud** och **starta station longitud** kolumner. Högerklicka på en av kolumnerna och välj sedan **kartan**.

    > [!TIP]
    > Om du vill aktivera flerval, håll ned __Ctrl (kommandot ⌘ på Mac)__ och Välj rubriken för varje kolumn.

    ![Bild av visualiseringen karta](media/tutorial-bikeshare-dataprep/launchMapInspector.png)

2. För att maximera kartan visualiseringen, Välj den **Maximera** ikon. För att passa kartan till fönstret Välj den **E** ikon på den översta vänstra sidan av visualiseringen.

    ![Maximerat bild](media/tutorial-bikeshare-dataprep/maximizedmap.png)

3. Klicka på den **minimera** vill gå tillbaka till diagramvyn.

## <a name="use-column-statistics-inspector"></a>Använd kolumnen statistik Inspector

Om du vill använda kolumnen statistik inspector, högerklicka på den __tripduration__ kolumn och markera __Kolumnstatistik__.

![Kolumnen statistik post](media/tutorial-bikeshare-dataprep/tripdurationcolstats.png)

Den här processen lägger till en ny visualisering med titeln __tripduration statistik__ i den __KONTROLLANTER__ fönstret.

![Bild av tripduration statistik inspector](media/tutorial-bikeshare-dataprep/tripdurationcolstatsinwell.png)

> [!IMPORTANT]
> Det högsta värdet för resa duration är **961,814 minuter**, vilket är ungefär två år. Det verkar det finns vissa avvikare i datauppsättningen.

## <a name="use-histogram-inspector"></a>Använd histogram inspector

Om du vill försöka identifiera avvikare högerklickar du på den __tripduration__ kolumn och markera __Histogram__.

![Histogram inspector](media/tutorial-bikeshare-dataprep/tripdurationhistogram.png)

Histogrammet är inte användbart, eftersom avvikare skeva i diagrammet.

## <a name="add-column-using-script"></a>Lägg till kolumn med hjälp av skript

1. Högerklicka på den **tripduration** kolumn och markera **Add Column (skript)**.

    ![Bild av menyn Lägg till kolumn (skript)](media/tutorial-bikeshare-dataprep/computecolscript.png)

2. I den __Add Column (skript)__ dialogrutan, använder du följande värden:

    * __Nya kolumnnamnet__: logtripduration
    * __Infoga den här nya kolumn efter__: tripduration
    * __Den nya kolumnen koden__:`math.log(row.tripduration)`
    * __Code blocktyp__: uttryck

   ![Dialogrutan Lägg till kolumn (skript)](media/tutorial-bikeshare-dataprep/computecolscriptdialog.png)

3. Välj __OK__ att lägga till den **logtripduration** kolumn.

4. Högerklicka på kolumnen och välj **Histogram**.

    ![Histogram för kolumnen logtripduration](media/tutorial-bikeshare-dataprep/logtriphistogram.png)

  Det här histogrammet verkar visuellt, normalfördelning med en onormalt sida.

## <a name="use-advanced-filter"></a>Använda avancerade Filter

Använda ett filter på data uppdaterar kontrollanterna med den nya distributionsplatsen. Högerklicka på den **logtripduration** kolumn och markera **filterkolumnen**. I den __redigera__ dialogrutan, använder du följande värden:

* __Det här numret Filterkolumn__: logtripduration
* __Jag vill__: Behåll rader
* __När__: något av villkoren nedan är True (logisk OR)
* __Om den här kolumnen__: mindre än
* __Värdet__: 9

![Filteralternativ](media/tutorial-bikeshare-dataprep/loftripfilter.png)

Välj __OK__ att tillämpa filtret.

![Uppdaterade histogram efter filter används](media/tutorial-bikeshare-dataprep/loftripfilteredinspector.png)

### <a name="the-halo-effect"></a>Halo effekt

1. Maximera den **logtripduration** histogram. Det finns en blå histogram högst upp på en grå histogram. Den här vyn kallas den **Halo effekt**:

    * Den **grå histogram** representerar distribution innan åtgärden (i det här fallet filtreringen).
    * Den **blå histogram** representerar histogrammet efter åtgärden. 

   Halo effekten hjälper med visualisera effekten av en åtgärd på data.

   ![Bild av halo effekt](media/tutorial-bikeshare-dataprep/loftripfilteredinspectormaximized.png)

    > [!NOTE]
    > Observera att det blå histogrammet visas kortare jämfört med föregående. Detta beror på automatisk bucketing igen på data i det nya området.

2. Om du vill ta bort halo, Välj __redigera__ och avmarkerar __visa halo__.

    ![Alternativ för histogrammet](media/tutorial-bikeshare-dataprep/uncheckhalo.png)

3. Välj **OK** att inaktivera halo effekt och minimera histogrammet.

### <a name="remove-columns"></a>Ta bort kolumner

Varje rad representerar en cykel pickup händelse i resa-data. Den här kursen behöver du bara den **starttime** och **starta station id** kolumner. Ta bort de andra kolumnerna som du väljer flera dessa två kolumner, högerklicka på kolumnrubriken och välj sedan **Behåll kolumnen**. Andra kolumner tas bort.

![Bild av alternativet Behåll kolumn](media/tutorial-bikeshare-dataprep/tripdatakeepcolumn.png)

### <a name="summarize-data-count"></a>Sammanfatta data (antal)

Använd härledda kolumner för att sammanfatta cykel efterfrågan på en 2-timmarsperiod.

1. Högerklicka på den **starttime** kolumn och markera **härledd kolumn efter exempel**

    ![Bild av härledd av exempel alternativet](media/tutorial-bikeshare-dataprep/tripdataderivebyexample.png)

2. Det här exemplet anger du värdet `Jan 01, 2017 12AM-2AM` för den första raden.

    > [!IMPORTANT]
    > I det förra exemplet härledande kolumner används flera steg för att härleda en kolumn som innehåller datum och tid period. I det här exemplet ser du att den här åtgärden kan utföras som ett enda steg genom att tillhandahålla ett exempel på det slutgiltiga resultatet.

    > [!NOTE]
    > Du kan ge ett exempel mot någon av raderna. I det här exemplet värdet för `Jan 01, 2017 12AM-2AM` är giltig för den första raden med data.

    ![Bild av exempeldata](media/tutorial-bikeshare-dataprep/tripdataderivebyexamplefirstexample.png)
   
3. Vänta tills programmet beräknar värden mot alla rader. Det kan ta flera sekunder. När analysen är klar kan du använda den __granska nästa föreslagna rad__ länken för att granska data.

   ![Bild av slutförda analysen med granska länk](media/tutorial-bikeshare-dataprep/tripdatabyexanalysiscomplete.png)

    Se till att de beräknade värdena är korrekta. Om inte, uppdatera värdet med det förväntade värdet och tryck på RETUR. Vänta sedan att slutföra. Slutför den **granska nästa föreslagna rad** bearbeta tills du ser **inga förslag**. När du ser **inga förslag**, programmet har tittat på edge-fall och är nöjd med syntetiskt programmet. Det är bäst att utföra en granskning av omvandlade data innan du godkänner omvandlingen. 

4. Välj **OK** att acceptera transformeringen. Byt namn på den nyligen skapade kolumnen att **timme datumintervall**.

    ![Bild av kolumnen har bytt namn till](media/tutorial-bikeshare-dataprep/tripdatasummarize.png)

5. Högerklicka på den **starttime** kolumnrubriken och välj **ta bort kolumnen**.

6. Om du vill sammanfatta data, Välj __Summarize__ från den __transformera__ menyn. Utför följande åtgärder för att skapa omvandlingen:

    * Dra __timme datumintervall__ och __starta station id__ till vänster (grupp av)-rutan.
    * Dra __starta station id__ till höger (sammanfatta data) rutan.

   ![En avbildning av alternativen sammanfattning](media/tutorial-bikeshare-dataprep/tripdatacount.png)

7. Välj **OK** att acceptera sammanfattning resultatet.

## <a name="join-dataflows"></a>Anslut dataflows

Använd följande steg för att ansluta väder-data med resa data:

1. Välj __ansluta__ från den __transformerar__ menyn.

2. __Tabeller__: Välj **BostonWeather** som vänstra dataflöde och **201701-hubway-tripdata** som rätt dataflöde. Om du vill fortsätta, Välj **nästa**.

    ![Bild av tabeller val](media/tutorial-bikeshare-dataprep/jointableselection.png)

3. __Nyckelkolumner__: Välj den **timme datumintervall** kolumn i både tabeller och välj sedan __nästa__.

    ![Bild av kopplingen på nyckelkolumner](media/tutorial-bikeshare-dataprep/joinkeyselection.png)

4. __Kopplingstyp__: Välj __matchande rader__ som kopplingstyp och välj sedan __Slutför__.

    ![Matchande rader kopplingstyp](media/tutorial-bikeshare-dataprep/joinscreen.png)

    Den här processen skapar en ny dataflöde med namnet __ansluta resultatet__.

## <a name="create-additional-features"></a>Skapa ytterligare funktioner

1. Om du vill skapa en kolumn som innehåller dagen i veckan, högerklicka på den **timme datumintervall** kolumn och markera **härledd kolumn efter exempel**. Använd värdet __Sun__ för ett datum som inträffat på söndag. Till exempel **01 januari 2017 12: 00 - 02: 00**. Tryck på **RETUR** och välj sedan **OK**. Byt namn på den här kolumnen till __veckodag__.

    ![Bild av hur du skapar en ny kolumn som innehåller dagen i veckan](media/tutorial-bikeshare-dataprep/featureweekday.png)

2. Om du vill skapa en kolumn som innehåller tidsperioden för en rad, högerklicka på den **timme datumintervall** kolumn och markera **härledd kolumn efter exempel**. Använd värdet **12: 00 - 02: 00** för den rad som innehåller **01 januari 2017 12: 00 - 02: 00**. Tryck på **RETUR** och välj sedan **OK**. Byt namn på den här kolumnen till **Period**.

    ![Bild av periodkolumnen](media/tutorial-bikeshare-dataprep/featurehourrange.png)

3. Ta bort den **timme datumintervall** och **rDate timme intervallet** kolumner, tryck på **Ctrl (kommandot ⌘ på Mac)** och markera varje kolumnrubriken. Högerklicka och välj **ta bort kolumnen**.

## <a name="read-data-from-python"></a>Läsa data från Python

Du kan köra ett paket för förberedelse av data från Python eller PySpark och hämta resultatet som ett **Data ram**.

Om du vill generera ett exempel Python-skriptet, högerklicka på __BikeShare Data Prep__ och välj __generera Data Access kodfilen__. Exempel Python-filen skapas i din **projektmappen**, och även har lästs in på en flik arbetsstationen. Följande skript i Python är ett exempel på den kod som genereras:

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

Den här självstudien är namnet på filen `BikeShare Data Prep.py`. Den här filen används senare under kursen.

## <a name="save-test-data-as-a-csv-file"></a>Spara testdata som en CSV-fil

Spara den **ansluta resultatet** dataflöde till en. CSV-fil måste du ändra den `BikeShare Data Prep.py` skript. Uppdatera Python-skriptet med följande kod:

```python
from azureml.dataprep.package import run

# dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
df = run('BikeShare Data Prep.dprep', dataflow_idx=2)

# Example file path: C:\\Users\\Jayaram\\BikeDataOut\\BikeShareTest.csv
df.to_csv('Your Test Data File Path here')
```

Välj **kör** högst upp på skärmen. Skriptet har skickats in som en **jobbet** på den lokala datorn. När jobbet har statusen ändras till __slutförd__, filen har skrivits till den angivna platsen.

## <a name="substitute-data-sources"></a>Ersätt datakällor

I föregående steg, som du använde den `201701-hubway-tripdata.csv` och `BostonWeather.csv` datakällor för att förbereda testdata. Om du vill använda paketet med andra resa-datafiler, använder du följande steg:

1. Skapa en ny **datakällan** med hjälp av anvisningarna tidigare, med följande ändringar i processen:

    * __Filen val__: när du väljer en fil, välja flera sex återstående resa tripdata. CSV-filer.

        ![Läsa in sex återstående filer](media/tutorial-bikeshare-dataprep/selectsixfiles.png)

        > [!NOTE]
        > Den __+ 5__ posten indikerar att det finns fem ytterligare filer utöver det som visas.

    * __Filinformation__: Ange __befordra huvuden läge__ till **alla filer har samma sidhuvuden**. Det här värdet anger att var och en av filerna som innehåller samma huvud.

        ![Välj information](media/tutorial-bikeshare-dataprep/headerfromeachfile.png) 

   Spara namnet på den här datakällan eftersom den används i senare steg.

2. Välj mappikonen för att visa filerna i projektet. Expandera den __aml\_config__ katalogen och välj sedan den `local.runconfig` filen.

    ![Bild av platsen för local.runconfig](media/tutorial-bikeshare-dataprep/localrunconfig.png) 

3. Lägg till följande rader i slutet av den `local.runconfig` filen och klicka på diskikonen för att spara filen.

    ```yaml
    DataSourceSubstitutions:
      201701-hubway-tripdata.dsource: 201501-hubway-tripdata.dsource
    ```

    Den här ändringen ersätts den ursprungliga datakällan med den som innehåller sex resa datafiler.

## <a name="save-training-data-as-a-csv-file"></a>Spara träningsdata som en CSV-fil

Navigera till filen Python `BikeShare Data Prep.py` du redigera tidigare och ange en annan sökväg till filen för att spara Data utbildning.

```python
from azureml.dataprep.package import run
# dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
df = run('BikeShare Data Prep.dprep', dataflow_idx=2)

# Example file path: C:\\Users\\Jayaram\\BikeDataOut\\BikeShareTrain.csv
df.to_csv('Your Training Data File Path here')
```

Om du vill skicka ett nytt jobb, Använd den **kör** längst upp på sidan. En **jobbet** har skickats in med den nya konfigurationen. Utdata för jobbet är utbildning-Data. Dessa data skapas med hjälp av samma steg för förberedelse av Data som du skapade tidigare. Det kan ta några minuter att slutföra jobbet.

## <a name="next-steps"></a>Nästa steg
Du har slutfört självstudierna cykeln filresurs förberedelse av Data. I den här kursen används Azure Machine Learning services (förhandsvisning) att lära dig hur du:
> [!div class="checklist"]
> * Förbereda data interaktivt med förberedelseverktyget för Azure Machine Learning Data
> * Importera, transformera och skapa en test-datamängd
> * Skapa ett paket för förberedelse av Data
> * Kör förberedelse datapaketet använder Python
> * Generera en datauppsättning för träning genom att återanvända paketet förberedelse av Data för ytterligare inkommande filer

Lär dig mer om förberedelse av data sedan:
> [!div class="nextstepaction"]
> [Användarhandboken för förberedelse av data](data-prep-user-guide.md)
