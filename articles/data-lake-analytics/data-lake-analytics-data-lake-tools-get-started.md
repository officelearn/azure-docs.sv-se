<properties
   pageTitle="Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio | Azure"
   description="Lär dig hur du installerar Data Lake-verktyg för Visual Studio, hur du utvecklar och testar U-SQL-skript. "
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/26/2016"
   ms.author="edmaca"/>

# Självstudier: Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Lär dig hur du installerar Data Lake-verktyg för Visual Studio och använder Data Lake-verktyg för Visual Studio för att skriva och testa U-SQL-skript.

U-SQL är ett hyperskalbart, mycket utökningsbart språk för att förbereda, transformera och analysera alla data i Data Lake och andra lagringsplatser. Mer information finns i [U-SQL-referens] (http://go.microsoft.com/fwlink/p/?LinkId=691348).


###Förutsättningar

- **Visual Studio 2015, Visual Studio 2013 update 4 eller Visual Studio 2012. Versionerna Enterprise (Ultimate/Premium), Professional och Community stöds. Versionen Express stöds inte. Visual Studio "15" stöds inte för närvarande och vi arbetar på detta.**
- **Microsoft Azure SDK för .NET version 2.7.1 eller senare**.  Installera den med hjälp av [installationsprogrammet för webbplattformen](http://www.microsoft.com/web/downloads/platform.aspx).
- **[Data Lake-verktyg för Visual Studio](http://aka.ms/adltoolsvs)**.

    När Data Lake-verktyg för Visual Studio har installerats, visas noden "Data Lake Analytics" i Server Explorer under noden "Azure" (du kan öppna Server Explorer genom att trycka på Ctrl + Alt + S).

- **Gå igenom följande två avsnitt i [Kom igång med Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-get-started-portal.md)**.

    - [Skapa ett Azure Data Lake Analytics-konto](data-lake-analytics-get-started-portal.md#create_adl_analytics_account).
    - [Ladda upp SearchLog.tsv till Data Lake-standardlagringskontot](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account).

    För din bekvämlighet finns ett PowerShell-exempelskript för att skapa en Data Lake Analytics-tjänst och ladda upp källdatafilen i [Appx-A PowerShell-exempel för att förbereda självstudierna](data-lake-analytics-data-lake-tools-get-started.md#appx-a-powershell-sample-for-preparing-the-tutorial).

    Data Lake-verktygen stöder inte skapandet av Data Lake Analytics-konton. Du måste skapa det med hjälp av Azure Portal, Azure PowerShell, .NET SDK eller Azure CLI. Om du vill köra ett Data Lake Analytics-jobb behöver du vissa data. Även om Data Lake-verktygen stöder uppladdning av data, kommer du att använda portalen för att ladda upp exempeldata. Detta gör det lättare att följa dessa självstudier.

## Anslut till Azure

**Så här ansluter du till Data Lake Analytics**

1. Öppna Visual Studio.
2. Klicka på **Server Explorer** i **Visa**-menyn för att öppna Server Explorer. Eller tryck på **[CTRL] + [ALT] + S**.
3. Högerklicka på **Azure**, klicka på "Anslut till Microsoft Azure-prenumeration" och följ sedan instruktionerna.
4. Gå till **Server Explorer**, expandera **Azure** och expandera sedan **Data Lake Analytics**. En lista över dina Data Lake Analytics-konton visas om det finns några. Du kan inte skapa Data Lake Analytics-konton från Visual Studio. För att skapa ett konto, se [Kom igång med Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-get-started-portal.md) eller [Kom igång med Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-get-started-powershell.md).

## Ladda upp källdatafiler

Du har laddat upp vissa data i avsnittet **Förutsättning** tidigare i självstudierna.  

Om du vill använda dina egna data följer du procedurerna för att ladda upp data från Data Lake-verktygen.

**Ladda upp filer till beroende Azure Data Lake-konto**

1. Från **Server Explorer**, expandera **Azure**, expandera **Data Lake Analytics**, expandera Data Lake Analytics-kontot, expandera **Lagringskonton**. Du bör se Data Lake-standardlagringskontot samt de länkade Data Lake-lagringskontona och länkade Azure Storage-kontona. Data Lake-standardkontot har en etikett "Standardlagringskonto".
2. Högerklicka på Data Lake-standardlagringskontot och klicka sedan på **Explorer**.  Data Lake-verktygen för Visual Studio Explorer öppnas.  Till vänster visas en trädvy och till höger visas innehållsvyn.
3. Bläddra till mappen dit du vill ladda upp filer,
4. högerklicka på ett tomt utrymme och klicka sedan på **Ladda upp**.

    ![U-SQL Visual Studio-projekt U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-upload-files.png)

**Ladda upp filer till ett länkat Azure Blob-lagringskonto**

1. Från **Server Explorer**, expandera **Azure**, expandera **Data Lake Analytics**, expandera Data Lake Analytics-kontot, expandera **Lagringskonton**. Du bör se Data Lake-standardlagringskontot samt de länkade Data Lake-lagringskontona och länkade Azure Storage-kontona.
2. Expandera Azure Storage-kontot.
3. Högerklicka på behållaren dit du vill ladda upp filer och klicka sedan på **Explorer**. Om du inte har en behållare, måste du först skapa en med hjälp av Azure-portalen, Azure PowerShell eller andra verktyg.
4. Bläddra till mappen dit du vill ladda upp filer,
5. högerklicka på ett tomt utrymme och klicka sedan på **Ladda upp**.

## Utveckla U-SQL-skript

Data Lake Analytics-jobb skrivs på U-SQL-språket. Läs mer om U-SQL i [Kom igång med U-SQL-språket](data-lake-analytics-u-sql-get-started.md) och [Referens för U-SQL-språket](http://go.microsoft.com/fwlink/?LinkId=691348).

**Skapa och skicka ett Data Lake Analytics-jobb**

1. Klicka på **Nytt** i **Arkiv**-menyn och klicka sedan på **Projekt**.
2. Välj typen **U-SQL-projekt**.

    ![nytt U-SQL Visual Studio-projekt](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Klicka på **OK** Visual Studio skapar en lösning med en **Script.usql**-fil.
4. Ange följande skript i **Script.usql**:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();

        @res =
            SELECT *
            FROM @searchlog;        

        OUTPUT @res   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    U-SQL-skriptet läser källdatafilen med hjälp av **Extractors.Tsv()** och skapar sedan en CSV-fil med hjälp av **Outputters.Csv()**.

    Ändra inte de två sökvägarna om du inte har kopierat källfilen till en annan plats.  Data Lake Analytics skapar utdatamappen om den inte finns.

    Det är enklare att använda relativa sökvägar för filer lagrade i Data Lake-standardkonton. Du kan också använda absoluta sökvägar.  Exempel

        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    Du måste använda absoluta sökvägar för att få åtkomst till filer i länkade Storage-konton.  Syntaxen för filer som lagras i ett länkat Azure Storage-konto är:

        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Åtkomstbehörighet till Azure Blob-behållare med offentliga blobbar eller offentliga behållare stöds inte.  

    Observera följande funktioner:

    - **IntelliSense**

        Namn automatiskt slutfört och medlemmarna kommer att visas för raduppsättningen, klasser, databaser, scheman och användardefinierade objekt (UDO).

        IntelliSense för katalogentiteter (databaser, scheman, tabeller, användardefinierade objekt o.s.v.) är relaterat till ditt beräkningskonto. Du kan kontrollera aktuellt aktivt beräkningskonto, databas och schema i det översta verktygsfältet och aktivera dem i listrutorna.

    - **Expandera * kolumner**

        Högerklicka på *, så ser du en blå understrykning under *. Håll musmarkören på den blå understrykningen och klicka sedan på nedpilen.
        ![Data Lake Visual Studio-verktygen expanderar *](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-expand-asterisk.png)

        Klicka på **Expanderar kolumner**, verktyget ersätter * med kolumnnamn.

    - **Automatisk formatering**

        Användarna kan ändra indrag av omfångsskriptet baserat på kodstrukturen under Redigera -> Avancerat:

        - Formatera dokument (Ctrl + E, D): Formaterar hela dokumentet   
        - Formatval (Ctrl + K, Ctrl + F): Formaterar markeringen. Om ingen markering har gjorts, formateras den rad markören står på.  

        Alla formateringsregler konfigureras under Verktyg -> Alternativ -> Textredigerare - > SIP -> Formatering.  
    - **Smarta indrag**

        Data Lake-verktyg för Visual Studio kan dra in uttryck automatiskt när du skriver skript. Den här funktionen är inaktiverad som standard, användare måste aktivera den genom att kontrollera U-SQL -> Alternativ och inställningar > Växlar -> Aktivera Smarta indrag.

    - **Gå till Definition och Hitta alla referenser**

        Högerklicka på namnet på en raduppsättning/parameter/ kolumn/UDO etc. och klicka på Gå till definition (F12) för att gå till den aktuella definitionen. Genom att klicka på Sök alla referenser (SKIFT + F12), visas alla referenser.

    - **Infoga Azure-sökväg**

        I stället för att komma ihåg Azure-sökvägen och skriva den manuellt när du skriver skriptet, ger Data Lake-verktyg för Visual Studio ett enkelt sätt: Högerklicka i redigeraren, klicka på Infoga Azure-sökväg. Navigera till filen i Azure Blob-dialogrutan för bläddring. Klicka på **OK** sökvägen till filen kommer att infogas i koden.

5. Ange Data Lake Analytics-kontot, databas och schema. Du kan välja **(lokalt)** för att köra skriptet lokalt för testning. Mer information finns i [Kör U-SQL lokalt](#run-u-sql-locally).

    ![Skicka U-SQL Visual Studio-projekt](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

    Mer information finns i [Använd U-SQL-katalogen](data-lake-analytics-use-u-sql-catalog.md).

5. Från **Solution Explorer**, högerklicka på **Script.usql** och klicka sedan på **Skapa skript**. Kontrollera resultatet i utdatarutan.
6. Från **Solution Explorer**, högerklicka på **Script.usql** och klicka sedan på **Skicka skript**. Om du vill kan du även klicka på **Skicka** från Script.usql-fönstret.  Se föregående skärmbild.  Klicka på pilen bredvid knappen Skicka för att skicka via avancerade alternativ:
7. Ange **Jobbnamn**, kontrollera **Analytics-konto**, och klicka sedan på **Skicka**. Resultat för skicka och jobblänk är tillgängliga i resultatfönstret för Data Lake-verktyg för Visual Studio när överföringen är klar.

    ![Skicka U-SQL Visual Studio-projekt](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-advanced.png)

8. Du måste klicka på Uppdatera om du vill se senaste jobbstatus och uppdatera skärmen. När jobbet har slutförts, visas **Jobbdiagram**, **Metadataåtgärder**, **Tillståndshistorik**, **Diagnostik**:

    ![Prestandadiagram för U-SQL Visual Studio Data Lake Analytics-jobbet](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

    * Jobbsammanfattning. Visa sammanfattningsinformation för aktuella jobb, t.ex.: tillstånd, status, körningstid, runtimenamn, skickat av osv.   
    * Jobbinformation. Detaljerad information om det här jobbet anges, inklusive vy för skript, resurs, brytpunktskörning.
    * Jobbdiagram. Fyra diagram tillhandahålls för att visualisera jobbets information: Förlopp, Lästa data, Skrivna data, Körningstid, Genomsnittlig körningstid per nod, Indatagenomflöde, Utdatagenomflöde.
    * Åtgärder för MetaData. Visar alla metadataåtgärder.
    * Tillståndshistorik.
    * Diagnostik. Data Lake-verktyg för Visual Studio kommer automatiskt att diagnostisera jobbkörningen. Du får aviseringar när det finns några fel eller prestandaproblem i arbetet. Det finns mer information i Jobbdiagnostik (länka TBD).

**Om du vill kontrollera jobbstatus**

1. Från Server Explorer, expandera **Azure**, expandera **Data Lake Analytics**, Expandera namnet på Data Lake Analytics-kontot
2. Dubbelklicka på **Jobb** för en lista över jobb.
2. Klicka på ett jobb för att se statusen.

**Visa jobbutdata**

1. Från **Server Explorer**, expandera **Azure**, expandera **Data Lake Analytics**, expandera Data Lake Analytics-kontot, expandera **Lagringskonton**, högerklicka på standardkontot för lagring av Data Lake och klicka sedan på **Explorer**.
2.  Dubbelklicka på **utdata** för att öppna mappen
3.  Dubbelklicka på **SearchLog-From-adltools.csv**.


###Jobbuppspelning

Via Jobbuppspelning kan du se förloppet för jobbkörningen och identifiera visuella prestandaavvikelser och flaskhalsar. Den här funktionen kan användas innan jobbet är slutfört (dvs. under den tid som jobbet körs aktivt) samt när körningen har slutförts. En uppspelning under jobbkörningen tillåter användaren att spela upp förloppet fram till den aktuella tiden.

**Om du vill se förlopp för jobbkörningen**  

1. Klicka på **Beläggningsprofil** i det övre högra hörnet. Se föregående skärmbild.
2. Klicka på knappen Spela upp i nedre vänstra hörnet för att granska jobbkörningens förlopp.
3. Under uppspelningen klickar du på **Pausa** för att stoppa den eller dra förloppsindikatorn direkt till specifika platser.


###Termisk karta

Data Lake-verktyg för Visual Studio innehåller för användaren valbara färgöverlägg för jobbet för att indikera förlopp, data-i/o, körningstid, i/o-genomflöde i varje fas. Användare kan via detta räkna ut potentiella problem och distribution av jobbegenskaper direkt och intuitivt. Du kan välja en datakälla som ska visas i listrutan.  

## Kör U-SQL lokalt

Med hjälp av lokal U-SQL-körning i Visual Studio kan du:

- Köra U-SQL-skript lokalt, tillsammans med C#-sammansättningar.
- Felsöka C#-sammansättningar lokalt.
- Skapa/ta bort/visa lokala databaser, sammansättningar, scheman och tabeller i Server Explorer precis som du kan göra för Azure Data Lake Analytics-tjänsten.

Du ser ett *Lokalt* konto i Visual Studio och installationsprogrammet skapar en *DataRoot*-mapp *C:\LocalRunRoot*. Mappen DataRoot kommer att användas:

- Lagra metadata, inklusive tabeller, databaser, tabellvärdesfunktioner osv.
- För vissa skript: om en relativ sökväg refereras till i/o-sökvägar, kommer vi att leta upp DataRoot (samt skriptets sökväg)
- Mappen DataRoot kommer INTE att refereras till om du försöker registrera en sammansättning och använder en relativ sökväg (du hittar mer information i "Använd sammansättningar vid lokal körning")

Följande videoklipp visar funktioner för lokal U-SQL-körning:

>[AZURE.VIDEO usql-localrun]

### Kända problem och begränsningar

- Lokal U-SQL-körning stöder inte lokala frågor om filuppsättningar. Se [U-SQL-filuppsättningar](https://msdn.microsoft.com/library/azure/mt621294.aspx). Detta kommer att lösas i framtiden.
- Långsam prestanda på grund av låg parallellitet eftersom jobbplaner utförs i följd i en enda process.
- Lokal körning kan inte visa jobbdiagram i Visual Studio. Detta kommer att åtgärdas i framtiden.
- Det går inte att skapa tabell/DB o.s.v. i Server Explorer för det lokala kontot.
- När en relativ sökväg refereras:

    - I skriptdata (EXTRAHERA * FRÅN "/sökväg/abc") - söks både DataRoot-sökvägen och skriptsökvägen igenom.
    - I skriptutada (UTDATA TILL "sökväg/abc"): kommer DataRoot-sökvägen att användas som utdatamapp.
    - I sammansättningsregistreringen (SKAPA SAMMANSÄTTNING xyz från "/sökväg/abc"): söks skriptsökvägen igenom, men inte DataRoot.
    - I registrerad tabellvärdesfunktion/vy eller andra metadataentiteter: söks DataRoot-sökvägen igenom, men inte skriptets sökväg.

    För skript som kördes på Data Lake-tjänsten, ska standardkontot för lagring användas som rotmapp och genomsöks därmed.

### Testa U-SQL-skript lokalt
Instruktioner om hur du utvecklar U-SQL-skript finns i [Utveckla U-SQL-skript](#develop-and-test-u-sql-scripts). Om du vill skapa och köra U-SQL-skript lokalt väljer du **(Lokalt)** i klusterlistrutan och klickar sedan på **Skicka**. Kontrollera att du refererar till korrekta data – referera antingen till den absoluta sökvägen eller placera data under mappen DataRoot.

![Skicka U-SQL Visual Studio-projekt lokalt](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-local-run.png)

Du kan också högerklicka på ett skript och sedan klicka på **Kör lokal plan** i snabbmenyn eller trycka på **CTRL + F5** för att utlösa lokal körning.

### Använda sammansättningar i lokal körning

Det finns två sätt att köra anpassade C#-filer:

- Skrivsammansättningar i koden bakom filen och sammansättningarna kommer automatiskt att registreras och tas bort när skriptet är klar.
- Skapa ett C#-sammansättningsprojekt och registrera DLL-filen för utdata till det lokala kontot via ett skript som nedan. Observera att sökvägen är i förhållande till skriptet i stället för mappen DataRoot.

![Använd sammansättningar i lokal U-SQL-körning](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-local-run-assembly.png)

### Felsök skript och C#-sammansättningar lokalt

Du kan felsöka C#-sammansättningar utan att skicka och registrera dem till Azure Data Lake Analytics-tjänsten. Du kan ange brytpunkter i både koden bakom filen och ett refererat C#-projekt.

**Felsöka lokal kod i koden bakom filen**
1.  Ange brytpunkter i koden bakom filen.
2.  Tryck på **F5** om du vill felsöka skript lokalt.

Följande procedur fungerar bara i Visual Studio 2015. Du kan behöva lägga till PDB-filer manuellt i äldre Visual Studio.

**Om du vill felsöka lokal kod i ett refererat C#-projekt**
1.  Skapa ett C#-sammansättningsprojekt och skapa det för att generera DLL-filen för utdata.
2.  Registrera DLL-filen med hjälp av ett U-SQL-uttryck:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
3.  Ange brytpunkter i C#-koden.
4.  Tryck på **F5** om du vill felsöka skriptet med referens till C#-DLL-filen lokalt.  

##Se även

Om du vill komma igång med Data Lake Analytics med hjälp av olika verktyg, se:

- [Kom igång med Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-get-started-portal.md)
- [Kom igång med Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Kom igång med Data Lake Analytics med hjälp av NET SDK](data-lake-analytics-get-started-net-sdk.md)

Om du vill se fler utvecklingsämnen:

- [Analysera webbloggar med hjälp av Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)
- [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Kom igång med Azure Data Lake Analytics U-SQL-språk](data-lake-analytics-u-sql-get-started.md)
- [Utveckla användardefinierade U-SQL-operatörer för Data Lake Analytics-jobb](data-lake-analytics-u-sql-develop-user-defined-operators.md)

##Appx-A PowerShell-exempel för att förbereda självstudier

Följande PowerShell-skript förbereder ett Azure Data Lake Analytics-konto och datakällan, så att du kan gå vidare till [Utveckla U-SQL-skript](data-lake-analytics-data-lake-tools-get-started.md#develop-u-sql-scripts).

    #region - used for creating Azure service names
    $nameToken = "<Enter an alias>"
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    #endregion

    #region - service names
    $resourceGroupName = $namePrefix + "rg"
    $dataLakeStoreName = $namePrefix + "adas"
    $dataLakeAnalyticsName = $namePrefix + "adla"
    $location = "East US 2"
    #endregion


    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an Azure Data Lake Analytics service account
    Write-Host "Create a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
    New-AzureRmDataLakeStoreAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeStoreName `
        -Location $location

    Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
    New-AzureRmDataLakeAnalyticsAccount `
        -Name $dataLakeAnalyticsName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultDataLake $dataLakeStoreName

    Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
    Get-AzureRmDataLakeAnalyticsAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeAnalyticsName  
    #endregion

    #region - prepare the source data
    Write-Host "Import the source data ..."  -ForegroundColor Green
    $localFolder = "C:\Tutorials\Downloads\" # A temp location for the file.
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location  
    New-Item -Path $localFolder -ItemType Directory -Force

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

    Write-Host "List the source data ..."  -ForegroundColor Green
    Get-AzureRmDataLakeStoreChildItem -Account $dataLakeStoreName -Path  "/Samples/Data/"
    #endregion



<!--HONumber=Jun16_HO2-->


