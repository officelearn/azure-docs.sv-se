<properties 
   pageTitle="Kom igång med Azure Data Lake Analytics med hjälp av Azure-kommandoradsgränssnittet | Microsoft Azure" 
   description="Lär dig att skapa ett Data Lake Store-konto med hjälp av Azure-kommandoradsgränssnittet samt skapa ett Data Lake Analytics-jobb med hjälp av U-SQL och skicka jobbet. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>


# Självstudier: Kom igång med Azure Data Lake Analytics med hjälp av Azure-kommandoradsgränssnittet (CLI)

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Lär dig hur du använder Azure CLI för att skapa Azure Data Lake Analytics-konton, definiera Data Lake Analytics-jobb i [U-SQL](data-lake-analytics-u-sql-get-started.md) och skicka jobb till Data Lake Analytics-konton. Mer information om Data Lake Analytics finns i [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).

I de här självstudierna utvecklar du ett jobb som läser en fil med tabbavgränsade värden (TVS) och konverterar den till en fil med kommaavgränsade värden (CSV). Klicka på flikarna överst i det här avsnittet om du vill gå igenom samma självstudier med andra verktyg.

##Krav

Innan du påbörjar de här självstudierna måste du ha:

- **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Azure CLI**. Se [Installera och konfigurera Azure CLI](../xplat-cli-install.md).
    - Hämta och installera **förhandsversionen** [Azure CLI-verktyg](https://github.com/MicrosoftBigData/AzureDataLake/releases) för att kunna slutföra den här demon.
- **Autentisering**, med följande kommando:

        azure login
    Mer information om autentisering med ett arbets- eller skolkonto finns i [Anslut till en Azure-prenumeration från Azure CLI](../xplat-cli-connect.md).
- **Växla till läget Azure Resource Manager**, med följande kommando:

        azure config mode arm
        
## Skapa ett Data Lake Analytics-konto

Du måste ha ett Data Lake Analytics-konto innan du kan köra några jobb. Om du vill skapa ett Data Lake Analytics-konto, måste du ange följande:

- **Azure-resursgrupp**: Ett Data Lake Analytics-konto måste skapas i en Azure-resursgrupp. Med [Azure Resource Manager](../resource-group-overview.md) kan du arbeta med resurserna i ditt program som en grupp. Du kan distribuera, uppdatera eller ta bort alla resurser i programmet i en enda, samordnad åtgärd.  

    Om du vill räkna upp resursgrupper i din prenumeration:
    
        azure group list 
    
    Om du vill skapa en ny resursgrupp:

        azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **Data Lake Analytics-kontonamn**
- **Plats**: Ett av de Azure-datacenter som stöder Data Lake Analytics.
- **Data Lake-standardkonto**: Varje Data Lake Analytics-konto har ett Data Lake-standardkonto.

    Om du vill skapa en lista över befintliga Data Lake-konton:
    
        azure datalake store account list

    Om du vill skapa ett nytt Data Lake-konto:

        azure datalake store account create "<Data Lake Store Account Name>" "<Azure Location>" "<Resource Group Name>"

    > [AZURE.NOTE] Data Lake-kontonamnet får bara innehålla gemener och siffror.



**Om du vill skapa ett Data Lake Analytics-konto**

        azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"

        azure datalake analytics account list
        azure datalake analytics account show "<Data Lake Analytics Account Name>"          

![Data Lake Analytics visa konto](./media/data-lake-analytics-get-started-cli/data-lake-analytics-show-account-cli.png)

> [AZURE.NOTE] Data Lake Analytics-kontonamnet får bara innehålla gemener och siffror.


## Ladda upp data till Data Lake Store

I självstudierna bearbetas vissa sökloggar.  Sökloggen kan lagras i Data Lake Store eller Azure Blob-lagring. 

Azure Portal innehåller ett användargränssnitt för att kopiera vissa exempeldatafiler till Data Lake-standardkontot, bland annat en sökloggfil. Se [Förbereda källdata](data-lake-analytics-get-started-portal.md#prepare-source-data) för att ladda upp data till Data Lake Store-standardkontot.

Om du vill ladda upp filer med hjälp av CLI, använder du följande kommando:

    azure datalake store filesystem import "<Data Lake Store Account Name>" "<Path>" "<Destination>"
    azure datalake store filesystem list "<Data Lake Store Account Name>" "<Path>"

Data Lake Analytics kan också använda Azure Blob-lagring.  Information om att ladda upp data till Azure Blob-lagring finns i [Använda Azure CLI med Azure Storage](../storage/storage-azure-cli.md).

## Skicka Data Lake Analytics-jobb

Data Lake Analytics-jobb skrivs på U-SQL-språket. Läs mer om U-SQL i [Kom igång med U-SQL-språket](data-lake-analytics-u-sql-get-started.md) och [Referens för U-SQL-språket](http://go.microsoft.com/fwlink/?LinkId=691348).

**Skapa ett Data Lake Analytics-jobbskript**

- Skapa en textfil med följande U-SQL-skript och spara filen på din arbetsstation:

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
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    U-SQL-skriptet läser källdatafilen med hjälp av **Extractors.Tsv()** och skapar sedan en CSV-fil med hjälp av **Outputters.Csv()**. 
    
    Ändra inte de två sökvägarna om du inte har kopierat filen till en annan plats.  Data Lake Analytics skapar utdatamappen om den inte finns.
    
    Det är enklare att använda relativa sökvägar för filer lagrade i Data Lake-standardkonton. Du kan också använda absoluta sökvägar.  Exempel 
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Du måste använda absoluta sökvägar för att få åtkomst till filer i länkade Storage-konton.  Syntaxen för filer som lagras i ett länkat Azure Storage-konto är:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Åtkomstbehörighet till Azure Blob-behållare med offentliga blobbar eller offentliga behållare stöds inte.      

    
**Skicka jobbet**


    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"
    
    
Följande kommandon kan användas för att visa jobb, hämta jobbinformation och avbryta jobb:

    azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job Id>"
    azure datalake analytics job list "<Data Lake Analytics Account Name>"
    azure datalake analytics job show "<Data Lake Analytics Account Name>" "<Job Id>"

När jobbet har slutförts kan du använda följande cmdletar för att visa och ladda ned filen:
    
    azure datalake store filesystem list "<Data Lake Store Account Name>" "/Output"
    azure datalake store filesystem export "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" "<Destination>"
    azure datalake store filesystem read "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" <Length> <Offset>

## Se även

- Klicka på flikväljarna överst på sidan om du vill se samma självstudier med andra verktyg.
- Om du vill se en mer komplex fråga, se [Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Information om att utveckla U-SQL-program finns i [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Information om U-SQL finns i [Kom igång med U-SQL-språk i Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
- Information om hanteringsuppgifter finns i [Hantera Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-manage-use-portal.md).
- Om du vill få en översikt över Data Lake Analytics, se [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).




<!--HONumber=Sep16_HO3-->


