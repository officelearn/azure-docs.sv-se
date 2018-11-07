---
title: Kopiera data från Azure Storage BLOB till Azure Data Lake Storage Gen1 | Microsoft Docs
description: Använd AdlCopy för att kopiera data från Azure Storage BLOB till Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: dc273ef8-96ef-47a6-b831-98e8a777a5c1
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 7e218cea543b48371647531cf6d62d8c10a07978
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51230758"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Kopiera data från Azure Storage BLOB till Azure Data Lake Storage Gen1
> [!div class="op_single_selector"]
> * [Använda DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Använda AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Azure Data Lake Storage Gen1 ger ett kommandoradsverktyg [AdlCopy](https://aka.ms/downloadadlcopy), för att kopiera data från följande källor:

* Från Azure Storage BLOB till Data Lake Storage Gen1. Du kan inte använda AdlCopy för att kopiera data från Data Lake Storage Gen1 till Azure Storage-blobbar.
* Mellan två Azure Data Lake Storage Gen1 konton.

Du kan också använda verktyget AdlCopy i två olika lägen:

* **Fristående**, där verktyget använder Data Lake Storage Gen1 resurser för att utföra uppgiften.
* **Med hjälp av ett Data Lake Analytics-konto**, där enheter som tilldelats till ditt Data Lake Analytics-konto som används för att utföra kopieringen. Du kanske vill använda det här alternativet när du försöker för att utföra uppgifter kopia på ett förutsägbart sätt.

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar den här artikeln måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Storage-Blobbar** behållare med vissa data.
* **Ett konto med Azure Data Lake Storage Gen1**. Anvisningar för hur du skapar ett finns i [Kom igång med Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Azure Data Lake Analytics-kontot (valfritt)** – Se [Kom igång med Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) för anvisningar om hur du skapar ett Data Lake Analytics-konto.
* **AdlCopy verktyget**. Installera verktyget AdlCopy från [ http://aka.ms/downloadadlcopy ](https://aka.ms/downloadadlcopy).

## <a name="syntax-of-the-adlcopy-tool"></a>Syntaxen för verktyget AdlCopy
Använd följande syntax för att arbeta med verktyget AdlCopy

    AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern

Parametrarna i syntaxen beskrivs nedan:

| Alternativ | Beskrivning |
| --- | --- |
| Källa |Anger platsen för datakällan i Azure storage-blob. Källan kan vara en blobbehållare, en blob eller ett annat Data Lake Storage Gen1-konto. |
| Dest |Anger Data Lake Storage Gen1 målet att kopiera till. |
| SourceKey |Anger lagringsåtkomstnyckel för Azure storage blob-källan. Detta krävs endast om källan är en blob-behållare eller en blob. |
| Konto |**Valfritt**. Använd det här alternativet om du vill använda Azure Data Lake Analytics-konto för att köra jobbet kopia. Om du använder alternativet /Account i syntax men inte anger ett Data Lake Analytics-konto, använder AdlCopy ett standardkonto för att köra jobbet. Även om du använder det här alternativet kan du måste lägga till källan (Azure Storage Blob) och målet (Azure Data Lake Storage Gen1) som datakällor för Data Lake Analytics-kontot. |
| Enheter |Anger antalet Data Lake Analytics-enheter som ska användas för kopieringsjobbet. Det här alternativet är obligatoriskt om du använder den **/kontot** alternativet för att ange Data Lake Analytics-kontot. |
| Mönster |Anger ett regex-mönster som indikerar vilken BLOB-objekt eller filer som ska kopieras. AdlCopy använder skiftlägeskänslig matchning. Det Standardmönster som används när inga mönster har angetts är att kopiera alla objekt. Att ange flera-filmönster stöds inte. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Använd AdlCopy (som fristående) för att kopiera data från en Azure Storage blob
1. Öppna en kommandotolk och navigera till den katalog där AdlCopy är installerad, vanligtvis `%HOMEPATH%\Documents\adlcopy`.
2. Kör följande kommando för att kopiera en specifik blob från behållaren källa till ett Data Lake Storage Gen1 mapp:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Exempel:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[!NOTE] 
    >Ovanstående syntax anger den fil som ska kopieras till en mapp i Data Lake Storage Gen1-konto. AdlCopy verktyget skapar en mapp om namnet på angivna mappen inte finns.

    Du uppmanas att ange autentiseringsuppgifter för Azure-prenumerationen som du har ditt Data Lake Storage Gen1-konto. Du ser utdata som liknar följande:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

1. Du kan också kopiera alla blobbar från en behållare till Data Lake Storage Gen1-konto med följande kommando:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Exempel:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

### <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

Om du kopierar från en Azure Blob Storage-konto, kan du strypas under kopia på blob storage-sida. Detta försämras prestanda för dina kopieringsjobb. Mer information om gränserna för Azure Blob Storage finns gränser för Azure Storage i [Azure-prenumeration och tjänstbegränsningar](../azure-subscription-service-limits.md).

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>Använd AdlCopy (som fristående) för att kopiera data från ett annat Data Lake Storage Gen1 konto
Du kan också använda AdlCopy för att kopiera data mellan två Gen1 för Data Lake Storage-konton.

1. Öppna en kommandotolk och navigera till den katalog där AdlCopy är installerad, vanligtvis `%HOMEPATH%\Documents\adlcopy`.
2. Kör följande kommando för att kopiera en viss fil från ett Data Lake Storage Gen1-konto till en annan.

        AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/

    Exempel:

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

   > [!NOTE]
   > Ovanstående syntax anger den fil som ska kopieras till en mapp i målet Gen1 för Data Lake Storage-konto. AdlCopy verktyget skapar en mapp om namnet på angivna mappen inte finns.
   >
   >

    Du uppmanas att ange autentiseringsuppgifter för Azure-prenumerationen som du har ditt Data Lake Storage Gen1-konto. Du ser utdata som liknar följande:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.
3. Följande kommando kopierar alla filer från en speciell mapp i källan Gen1 för Data Lake Storage-konto till en mapp i målet Gen1 för Data Lake Storage-konto.

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

### <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

När du använder AdlCopy som ett fristående verktyg, kopian körs på delade, Azure hanterade resurser. Prestanda kan du få i den här miljön är beroende av systembelastning och tillgängliga resurser. Det här läget är bäst för små överföringar för en ad hoc-generering. Inga parametrar behöver justeras när du använder AdlCopy som ett fristående verktyg.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Använd AdlCopy (med Data Lake Analytics-konto) för att kopiera data
Du kan också använda Data Lake Analytics-kontot för att köra jobbet AdlCopy för att kopiera data från Azure storage BLOB till Data Lake Storage Gen1. Du använder vanligtvis det här alternativet när data flyttas mellan gigabyte och terabyte och du vill ha bättre och förutsägbara prestanda-dataflöde.

Om du vill använda Data Lake Analytics-kontot med AdlCopy för att kopiera från en Azure Storage Blob, måste du lägga källan (Azure Storage Blob) som en datakälla för ditt Data Lake Analytics-konto. Anvisningar för att lägga till ytterligare datakällor i Data Lake Analytics-kontot finns i [hantera Data Lake Analytics-kontot datakällor](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Om du kopierar från en Azure Data Lake Storage Gen1-konto som källa med ett Data Lake Analytics-konto, behöver du inte associera Gen1 för Data Lake Storage-konto med Data Lake Analytics-kontot. Kravet på att associera källagringen med Data Lake Analytics-konto är endast om källan är en Azure Storage-konto.
>
>

Kör följande kommando för att kopiera från en Azure Storage blob till ett Data Lake Storage Gen1-konto med Data Lake Analytics-konto:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>

Exempel:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2

På samma sätt, kör följande kommando för att kopiera alla filer från en speciell mapp i källan Gen1 för Data Lake Storage-konto till en mapp i Data Lake Storage Gen1-destinationskonto med Data Lake Analytics-konto:

    AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

### <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

När du kopierar data mellan terabyte ger med AdlCopy med ditt Azure Data Lake Analytics-konto bättre och mer förutsägbar prestanda. Parametern som ska finjusteras är antalet Azure Data Lake Analytics enheter ska användas för Kopiera projekt. Öka antalet enheter ökar prestandan för dina kopieringsjobb. Varje fil som ska kopieras kan använda maximalt en enhet. Att ange fler enheter än antalet filer kopieras ökar inte prestanda.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Använd AdlCopy för att kopiera data med hjälp av mönstermatchning
I det här avsnittet får du lära dig hur du använder AdlCopy för att kopiera data från en källa (i vårt exempel nedan använder vi Azure Storage Blob) till ett mål Gen1 för Data Lake Storage-konto med mönstermatchning. Du kan till exempel använda stegen nedan för att kopiera alla filer med CSV-fil från källbloben till målet.

1. Öppna en kommandotolk och navigera till den katalog där AdlCopy är installerad, vanligtvis `%HOMEPATH%\Documents\adlcopy`.
2. Kör följande kommando för att kopiera alla filer med filnamnstillägget *.csv från en specifik blob från behållaren källa till ett Data Lake Storage Gen1 mapp:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Exempel:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

## <a name="billing"></a>Fakturering
* Om du använder verktyget AdlCopy som fristående kommer du att debiteras för kostnader för nätverksegress för att flytta data, om källan Azure Storage-konto inte är i samma region som Gen1 för Data Lake Storage-konto.
* Om du använder verktyget AdlCopy med Data Lake Analytics-konto, standard [Datasjöanalys fakturataxor](https://azure.microsoft.com/pricing/details/data-lake-analytics/) kommer att gälla.

## <a name="considerations-for-using-adlcopy"></a>Använda AdlCopy
* AdlCopy (för version 1.0.5), har stöd för kopiering av data från källor som gemensamt har mer än tusentals filer och mappar. Om det uppstår problem med att kopiera en stor datauppsättning kan du dock distribuera filer/mappar till olika undermappar och Använd sökvägen till dessa undermappar som källa i stället.

## <a name="performance-considerations-for-using-adlcopy"></a>Prestandaöverväganden för att använda AdlCopy

AdlCopy har stöd för kopiering av data som innehåller tusentals filer och mappar. Om du får problem med kopiera en stor datauppsättning, kan du distribuera filer/mappar till mindre undermappar. AdlCopy har skapats för ad hoc-kopior. Om du vill kopiera data regelbundet, bör du använda [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) som ger fullständig hantering runt i kopieringsåtgärder.

## <a name="release-notes"></a>Viktig information
* 1.0.13 - om du vill kopiera data på samma konto för Azure Data Lake Storage Gen1 över flera adlcopy kommandon du behöver inte ange dina autentiseringsuppgifter för varje körning igen längre. Adlcopy cachelagrar nu informationen från de många körningarna.

## <a name="next-steps"></a>Nästa steg
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Använd Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
