---
title: "Kopiera data från Azure Storage-Blobbar till Data Lake Store | Microsoft Docs"
description: "Använd AdlCopy för att kopiera data från Azure Storage-Blobbar till Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: dc273ef8-96ef-47a6-b831-98e8a777a5c1
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 33bfd200f72cb56b391cab52bf90f19b568893a9
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2018
---
# <a name="copy-data-from-azure-storage-blobs-to-data-lake-store"></a>Kopiera data från Azure Storage-blobar till Data Lake Store
> [!div class="op_single_selector"]
> * [Använda DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Använda AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Azure Data Lake Store ger ett kommandoradsverktyg [AdlCopy](http://aka.ms/downloadadlcopy), för att kopiera data från följande källor:

* Från Azure Storage BLOB till Data Lake Store. Du kan inte använda AdlCopy för att kopiera data från Data Lake Store till Azure Storage BLOB.
* Mellan två Azure Data Lake Store-konton.

Du kan också använda verktyget AdlCopy i två olika lägen:

* **Fristående**, där verktyget använder Data Lake Store-resurser för att utföra åtgärden.
* **Med ett Data Lake Analytics-konto**, där enheter som tilldelats till ditt Data Lake Analytics-konto som används för att utföra kopieringen. Du kanske vill använda det här alternativet när du behöver utföra uppgifter kopiera förutsägbart.

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar den här artikeln måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Storage-Blobbar** behållare med vissa data.
* **Ett Azure Data Lake Store-konto**. Anvisningar om hur du skapar en finns [Kom igång med Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Azure Data Lake Analytics-kontot (valfritt)** -finns [Kom igång med Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) anvisningar om hur du skapar ett Data Lake Store-konto.
* **AdlCopy verktyget**. Installera verktyget AdlCopy från [http://aka.ms/downloadadlcopy](http://aka.ms/downloadadlcopy).

## <a name="syntax-of-the-adlcopy-tool"></a>Syntaxen för verktyget AdlCopy
Använd följande syntax för att arbeta med verktyget AdlCopy

    AdlCopy /Source <Blob or Data Lake Store source> /Dest <Data Lake Store destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Unit <Number of Analytics units> /Pattern

Parametrarna i syntaxen beskrivs nedan:

| Alternativ | Beskrivning |
| --- | --- |
| Källa |Anger platsen för datakällan i Azure storage blob. Källan kan vara en blob-behållare, en blobb eller en annan Data Lake Store-konto. |
| Målet |Anger målet att kopiera till Data Lake Store. |
| SourceKey |Anger lagringsåtkomstnyckel för Azure storage blob-källa. Detta krävs endast om källan är en blob-behållare eller en blob. |
| Konto |**Valfritt**. Använd det här alternativet om du vill använda Azure Data Lake Analytics-konto för att köra jobbet kopia. Om du använder alternativet /Account syntaxen men inte anger ett Data Lake Analytics-konto, använder AdlCopy ett standardkonto för att köra jobbet. Även om du använder det här alternativet måste du lägga källa (Azure Storage Blob) och mål (Azure Data Lake Store) som datakällor för Data Lake Analytics-kontot. |
| Enheter |Anger antalet Data Lake Analytics-enheter som ska användas för Kopiera projekt. Det här alternativet är obligatoriskt om du använder den **/kontot** alternativet för att ange Data Lake Analytics-kontot. |
| Mönster |Anger ett regex-mönster som anger vilka blobbar eller filer som ska kopieras. AdlCopy använder skiftlägeskänsliga matchar. Det Standardmönster som används när inga mönster har angetts används för att kopiera alla objekt. Ange flera filen mönster stöds inte. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Använd AdlCopy (som fristående) för att kopiera data från ett Azure Storage-blob
1. Öppna en kommandotolk och gå till den katalog där AdlCopy installeras normalt `%HOMEPATH%\Documents\adlcopy`.
2. Kör följande kommando för att kopiera en specifik blobb från käll-behållare till ett Data Lake Store:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Exempel:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[AZURE.NOTE]Ovanstående syntax anger den fil som ska kopieras till en mapp i Data Lake Store-konto. AdlCopy verktyget skapar en mapp om namnet på angivna mappen inte finns.

    Du uppmanas att ange autentiseringsuppgifter för Azure-prenumerationen som du har ditt Data Lake Store-konto. Du kommer se utdata som liknar följande:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

1. Du kan också kopiera alla blobbar från en behållare till Data Lake Store-konto med följande kommando:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Exempel:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

### <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

Om du kopierar från ett Azure Blob Storage-konto kan du begränsas vid kopiering av blob storage-sida. Detta försämrar prestanda för Kopiera projekt. Mer information om gränserna för Azure Blob Storage finns Azure Lagringsgränser på [Azure-prenumeration och tjänstbegränsningarna](../azure-subscription-service-limits.md).

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-store-account"></a>Använd AdlCopy (som fristående) för att kopiera data från en annan Data Lake Store-konto
Du kan också använda AdlCopy för att kopiera data mellan två Data Lake Store-konton.

1. Öppna en kommandotolk och gå till den katalog där AdlCopy installeras normalt `%HOMEPATH%\Documents\adlcopy`.
2. Kör följande kommando för att kopiera en fil från ett Data Lake Store-konto till en annan.

        AdlCopy /Source adl://<source_adls_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adls_account>.azuredatalakestore.net/<path>/

    Exempel:

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

   > [!NOTE]
   > Ovanstående syntax anger den fil som ska kopieras till en mapp i målet Data Lake Store-konto. AdlCopy verktyget skapar en mapp om namnet på angivna mappen inte finns.
   >
   >

    Du uppmanas att ange autentiseringsuppgifter för Azure-prenumerationen som du har ditt Data Lake Store-konto. Du kommer se utdata som liknar följande:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.
3. Följande kommando kopierar alla filer i en mapp i Data Lake Store-konto för källan till en mapp i målet Data Lake Store-konto.

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

### <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

När du använder AdlCopy som ett fristående verktyg kopian körs på delade, Azure hanterade resurser. Prestanda kan du få i den här miljön är beroende av systembelastning och tillgängliga resurser. Det här läget är bäst för mindre överföringar på ad hoc-basis. Inga parametrar behöver justeras när du använder AdlCopy som ett fristående verktyg.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Använd AdlCopy (med Data Lake Analytics-konto) för att kopiera data
Du kan också använda ditt Data Lake Analytics-konto för att köra jobbet AdlCopy för att kopiera data från Azure storage BLOB till Data Lake Store. Du använder normalt bara det här alternativet när data flyttas är inom räckhåll gigabyte och terabyte och du vill bättre och förutsägbar prestanda genomflöde.

Om du vill använda ditt Data Lake Analytics-konto med AdlCopy för att kopiera från en Azure Storage Blob, läggas källan (Azure Storage Blob) till som en datakälla för Data Lake Analytics-kontot. Anvisningar för att lägga till ytterligare datakällor i Data Lake Analytics-kontot finns [hantera Data Lake Analytics-kontot datakällor](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Om du kopierar från ett Azure Data Lake Store-konto som källa med ett Data Lake Analytics-konto behöver du inte associera Data Lake Store-kontot med Data Lake Analytics-konto. Kravet på att associera arkivet källa med Data Lake Analytics-kontot är endast om källan är ett Azure Storage-konto.
>
>

Kör följande kommando för att kopiera från en Azure Storage blob till ett Data Lake Store-konto med Data Lake Analytics-konto:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

Exempel:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2

På liknande sätt, kör följande kommando för att kopiera alla filer i en mapp i Data Lake Store-konto för källan till en mapp på målservern Data Lake Store-kontot med Data Lake Analytics-konto:

    AdlCopy /Source adl://mysourcedatalakestore.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastore.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

### <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

När du kopierar data i intervallet terabyte ger med din egen Azure Data Lake Analytics-konto AdlCopy bättre och mer förutsägbar prestanda. Parametern som ska justeras är antalet Azure Data Lake Analytics-enheter ska användas för Kopiera projekt. Öka antalet enheter kommer att öka prestanda för Kopiera projekt. Varje fil som ska kopieras kan använda maximalt en enhet. Ange fler enheter än antalet filer som kopieras kommer inte att öka prestanda.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Använd AdlCopy för att kopiera data med hjälp av mönstermatchning
I det här avsnittet kan du lära dig hur du använder AdlCopy för att kopiera data från en källa (i vårt exempel nedan använder vi Azure Storage Blob) till ett mål Data Lake Store-konto med hjälp av mönstermatchning. Du kan till exempel använda instruktionerna nedan för att kopiera alla filer med filnamnstillägget .csv från blob för källan till målet.

1. Öppna en kommandotolk och gå till den katalog där AdlCopy installeras normalt `%HOMEPATH%\Documents\adlcopy`.
2. Kör följande kommando för att kopiera alla filer med filnamnstillägget *.csv från en specifik blobb från käll-behållare till ett Data Lake Store:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Exempel:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

## <a name="billing"></a>Fakturering
* Om du använder verktyget AdlCopy som fristående kommer du att debiteras för kostnader för nätverksegress för att flytta data, om källan Azure Storage-konto inte är i samma region som Data Lake Store.
* Om du använder verktyget AdlCopy med Data Lake Analytics-kontot, standard [Data Lake Analytics fakturering priser](https://azure.microsoft.com/pricing/details/data-lake-analytics/) gäller.

## <a name="considerations-for-using-adlcopy"></a>Överväganden för att använda AdlCopy
* AdlCopy (för version 1.0.5), har stöd för kopiering av data från källor som gemensamt har mer än tusentals filer och mappar. Om det uppstår problem kopiera en stor datauppsättning kan du dock distribuera filer/mappar till olika undermappar och Använd sökvägen till de underordnade mapparna som källa i stället.

## <a name="performance-considerations-for-using-adlcopy"></a>Prestandaöverväganden för att använda AdlCopy

AdlCopy har stöd för kopiering av data som innehåller tusentals filer och mappar. Om du får problem med kopiera en stor datauppsättning kan du distribuera filer/mappar till mindre underordnade mappar. AdlCopy skapades för ad hoc-kopior. Om du vill kopiera data regelbundet, bör du använda [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) som ger fullständig hantering runt åtgärderna kopia.

## <a name="release-notes"></a>Viktig information
* 1.0.13 - om du kopierar data till samma Azure Data Lake Store-konto över flera adlcopy kommandon du behöver inte ange dina autentiseringsuppgifter för varje kör längre. Adlcopy nu cachelagras informationen över flera körs.

## <a name="next-steps"></a>Nästa steg
* [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
* [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
