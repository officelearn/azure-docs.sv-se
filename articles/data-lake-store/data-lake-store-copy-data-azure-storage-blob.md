---
title: Kopiera data från Azure Storage-blobbar till Data Lake Storage Gen1
description: Använda AdlCopy-verktyget för att kopiera data från Azure Storage Blobbar till Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: ad408df140be49da2e50ef810285dd850e9da6a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638878"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Kopiera data från Azure Storage Blobbar till Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [Använda DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Använda AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Data Lake Storage Gen1 innehåller ett kommandoradsverktyg, [AdlCopy,](https://www.microsoft.com/download/details.aspx?id=50358)för att kopiera data från följande källor:

* Från Azure Storage blobbar till Data Lake Storage Gen1. Du kan inte använda AdlCopy för att kopiera data från Data Lake Storage Gen1 till Azure Storage blobbar.
* Mellan två Data Lake Storage Gen1-konton.

Du kan också använda AdlCopy-verktyget i två olika lägen:

* **Fristående**, där verktyget använder Data Lake Storage Gen1-resurser för att utföra aktiviteten.
* **Med hjälp av ett DataSjöanalyskonto**används de enheter som tilldelats ditt DataSjöanalyskonto för att utföra kopieringen. Du kanske vill använda det här alternativet när du vill utföra kopieringsuppgifterna på ett förutsägbart sätt.

## <a name="prerequisites"></a>Krav

Innan du påbörjar den här artikeln måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Storage blobbar** behållare med vissa data.
* **Ett Data Lake Storage Gen1-konto**. Instruktioner om hur du skapar en finns i [Komma igång med Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **DataSjöanalyskonto (valfritt)** – Se [Kom igång med Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) för instruktioner om hur du skapar ett DataSjöanalyskonto.
* **AdlCopy-verktyg**. Installera [AdlCopy-verktyget](https://www.microsoft.com/download/details.aspx?id=50358).

## <a name="syntax-of-the-adlcopy-tool"></a>Syntax för adlcopy-verktyget

Använd följande syntax för att arbeta med AdlCopy-verktyget

    AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern

Parametrarna i syntaxen beskrivs nedan:

| Alternativ | Beskrivning |
| --- | --- |
| Källa |Anger platsen för källdata i Azure storage blob. Källan kan vara en blob-behållare, en blob eller ett annat Data Lake Storage Gen1-konto. |
| Dest |Anger målet Datasjölagring gen1 att kopiera till. |
| SourceKey (Källa) |Anger lagringsåtkomstnyckeln för Azure storage blob-källan. Detta krävs endast om källan är en blob-behållare eller en blob. |
| Konto |**Valfritt**. Använd detta om du vill använda Azure Data Lake Analytics-konto för att köra kopieringsjobbet. Om du använder alternativet /Account i syntaxen men inte anger ett DataSjöanalyskonto använder AdlCopy ett standardkonto för att köra jobbet. Om du använder det här alternativet måste du också lägga till källan (Azure Storage Blob) och målet (Azure Data Lake Storage Gen1) som datakällor för ditt DataSjöanalyskonto. |
| Enheter |Anger antalet DataSjöanalysenheter som ska användas för kopieringsjobbet. Det här alternativet är obligatoriskt om du använder alternativet **/Account** för att ange DataSjöanalyskontot. |
| Mönster |Anger ett regex-mönster som anger vilka blobbar eller filer som ska kopieras. AdlCopy använder skiftlägeskänslig matchning. Standardmönstret när inget mönster har angetts är att kopiera alla objekt. Det går inte att ange flera filmönster. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Använda AdlCopy (som fristående) för att kopiera data från en Azure Storage-blob

1. Öppna en kommandotolk och navigera till katalogen `%HOMEPATH%\Documents\adlcopy`där AdlCopy är installerat, vanligtvis .
1. Kör följande kommando om du vill kopiera en specifik blob från källbehållaren till en datasjölagringsgenm1-mapp:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Ett exempel:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[!NOTE]
    >Syntaxen ovan anger den fil som ska kopieras till en mapp i datasjölagringsgenm1-kontot. AdlCopy-verktyget skapar en mapp om det angivna mappnamnet inte finns.

    Du uppmanas att ange autentiseringsuppgifterna för Azure-prenumerationen som du har ditt Data Lake Storage Gen1-konto under. Du kommer att se en utdata som liknar följande:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

1. Du kan också kopiera alla blobbar från en behållare till datasjölagringsgenm1-kontot med följande kommando:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Ett exempel:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

### <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

Om du kopierar från ett Azure Blob Storage-konto kan du begränsas under kopieringen på blob-lagringssidan. Detta försämrar prestanda för ditt kopieringsjobb. Mer information om gränserna för Azure Blob Storage finns i Azure Storage-gränser på [Azure-prenumerations- och tjänstgränser](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>Använda AdlCopy (som fristående) för att kopiera data från ett annat Data Lake Storage Gen1-konto

Du kan också använda AdlCopy för att kopiera data mellan två Data Lake Storage Gen1-konton.

1. Öppna en kommandotolk och navigera till katalogen `%HOMEPATH%\Documents\adlcopy`där AdlCopy är installerat, vanligtvis .
1. Kör följande kommando för att kopiera en viss fil från ett Data Lake Storage Gen1-konto till ett annat.

        AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/

    Ett exempel:

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

   > [!NOTE]
   > Syntaxen ovan anger den fil som ska kopieras till en mapp i kontot Data Lake Storage Gen1. AdlCopy-verktyget skapar en mapp om det angivna mappnamnet inte finns.
   >
   >

    Du uppmanas att ange autentiseringsuppgifterna för Azure-prenumerationen som du har ditt Data Lake Storage Gen1-konto under. Du kommer att se en utdata som liknar följande:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.
1. Följande kommando kopierar alla filer från en viss mapp i källkontot Data Lake Storage Gen1 till en mapp i kontot Data Lake Storage Gen1.

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

### <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

När adlCopy används som ett fristående verktyg körs kopian på delade, Azure-hanterade resurser. Vilken prestanda du kan få i den här miljön beror på systembelastning och tillgängliga resurser. Det här läget används bäst för små överföringar på ad hoc-basis. Inga parametrar behöver justeras när AdlCopy används som ett fristående verktyg.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Använda AdlCopy (med Data Lake Analytics-konto) för att kopiera data

Du kan också använda ditt DataSjöanalyskonto för att köra AdlCopy-jobbet för att kopiera data från Azure storage blobbar till Data Lake Storage Gen1. Du använder vanligtvis det här alternativet när de data som ska flyttas ligger i intervallet gigabyte och terabyte och du vill ha bättre och förutsägbart prestandadataflöde.

Om du vill använda ditt DataSjöanalyskonto med AdlCopy för att kopiera från en Azure Storage Blob måste källan (Azure Storage Blob) läggas till som en datakälla för ditt DataSjöanalyskonto. Instruktioner om hur du lägger till ytterligare datakällor i ditt DataSjöanalyskonto finns i [Hantera datasjöanalyskontodatakällor](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Om du kopierar från ett Azure Data Lake Storage Gen1-konto som källa med ett DataSjöanalyskonto behöver du inte associera Data Lake Storage Gen1-kontot med DataSjöanalyskontot. Kravet på att associera källarkivet med Data Lake Analytics-kontot är endast när källan är ett Azure Storage-konto.
>
>

Kör följande kommando för att kopiera från en Azure Storage-blob till ett Data Lake Storage Gen1-konto med Data Lake Analytics-konto:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>

Ett exempel:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2

På samma sätt kör du följande kommando för att kopiera alla filer från en viss mapp i källkontot Data Lake Storage Gen1 till en mapp i kontot Data Lake Storage Gen1 med Data Lake Analytics-konto:

    AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

### <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

När du kopierar data i intervallet terabyte ger adlCopy med ditt eget Azure Data Lake Analytics-konto bättre och mer förutsägbara prestanda. Parametern som ska justeras är antalet Azure Data Lake Analytics-enheter som ska användas för kopieringsjobbet. Om du ökar antalet enheter ökar kopieringsjobbets prestanda. Varje fil som ska kopieras kan använda högst en enhet. Om du anger fler enheter än antalet filer som kopieras ökar inte prestanda.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Använda AdlCopy för att kopiera data med mönstermatchning

I det här avsnittet får du lära dig hur du använder AdlCopy för att kopiera data från en källa (i vårt exempel nedan använder vi Azure Storage Blob) till ett målDataSjölagringsgenm1-konto med mönstermatchning. Du kan till exempel använda stegen nedan för att kopiera alla filer med CSV-tillägget från källblobben till målet.

1. Öppna en kommandotolk och navigera till katalogen `%HOMEPATH%\Documents\adlcopy`där AdlCopy är installerat, vanligtvis .
1. Kör följande kommando om du vill kopiera alla filer med *.csv-tillägget från en specifik blob från källbehållaren till en datasjölagringsgenm1-mapp:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Ett exempel:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

## <a name="billing"></a>Fakturering

* Om du använder AdlCopy-verktyget som fristående debiteras du för utgående kostnader för att flytta data, om kontot för azure storage-källa inte finns i samma region som Data Lake Storage Gen1-kontot.
* Om du använder AdlCopy-verktyget med ditt DataSjöanalyskonto gäller [standardfaktureringspriser för DataSjöanalys.](https://azure.microsoft.com/pricing/details/data-lake-analytics/)

## <a name="considerations-for-using-adlcopy"></a>Överväganden för att använda AdlCopy

* AdlCopy (för version 1.0.5) stöder kopiering av data från källor som tillsammans har mer än tusentals filer och mappar. Om du stöter på problem med att kopiera en stor datauppsättning kan du distribuera filerna/mapparna till olika undermappar och använda sökvägen till undermapparna som källa i stället.

## <a name="performance-considerations-for-using-adlcopy"></a>Prestandaöverväganden för att använda AdlCopy

AdlCopy stöder kopiering av data som innehåller tusentals filer och mappar. Om du stöter på problem med att kopiera en stor datauppsättning kan du distribuera filerna/mapparna till mindre undermappar. AdlCopy har skapats för ad hoc-kopior. Om du försöker kopiera data regelbundet bör du överväga att använda [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) som tillhandahåller fullständig hantering kring kopieringsåtgärderna.

## <a name="release-notes"></a>Viktig information

* 1.0.13 - Om du kopierar data till samma Azure Data Lake Storage Gen1-konto över flera adlcopy-kommandon behöver du inte längre ange dina autentiseringsuppgifter för varje körning. Adlcopy cachelagrar nu den informationen över flera körningar.

## <a name="next-steps"></a>Nästa steg

* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Använda Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använda Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
