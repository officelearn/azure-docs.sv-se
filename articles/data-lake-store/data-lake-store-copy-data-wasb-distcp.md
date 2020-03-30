---
title: Kopiera data till och från WASB till Azure Data Lake Storage Gen1 med DistCp
description: Använda verktyget DistCp för att kopiera data till och från Azure Storage-blobbar till Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 455e73ece2d46a508b3077c13c8106fe53beb4de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638841"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Använda DistCp för att kopiera data mellan Azure Storage-blobbar och Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [Använda DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Använda AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Om du har ett HDInsight-kluster med åtkomst till Azure Data Lake Storage Gen1 kan du använda Hadoop-ekosystemverktyg som DistCp för att kopiera data till och från en WASB -klusterlagring (HDInsight cluster storage) till ett Data Lake Storage Gen1-konto. Den här artikeln visar hur du använder verktyget DistCp.

## <a name="prerequisites"></a>Krav

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Data Lake Storage Gen1-konto**. Instruktioner om hur du skapar en finns i [Komma igång med Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Azure HDInsight-kluster** med åtkomst till ett Data Lake Storage Gen1-konto. Se [Skapa ett HDInsight-kluster med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Se till att du aktiverar Fjärrskrivbord för klustret.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Använda DistCp från ett HDInsight Linux-kluster

Ett HDInsight-kluster levereras med DistCp-verktyget, som kan användas för att kopiera data från olika källor till ett HDInsight-kluster. Om du har konfigurerat HDInsight-klustret så att datasjölagringgeny1 används som ytterligare lagringsutrymme kan du använda DistCp-direkt för att kopiera data till och från ett DataSjölagringsgenm1-konto. I det här avsnittet tittar vi på hur du använder DistCp-verktyget.

1. Från skrivbordet använder du SSH för att ansluta till klustret. Se [Anslut till ett Linux-baserat HDInsight-kluster](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Kör kommandona från SSH-prompten.

1. Kontrollera om du kan komma åt Azure Storage-blobbar (WASB). Kör följande kommando:

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   Utdata innehåller en lista över innehållet i lagringsbloben.

1. På samma sätt kontrollerar du om du kan komma åt datasjölagringsgenm1-kontot från klustret. Kör följande kommando:

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    Utdata innehåller en lista över filer och mappar i datasjölagringsgenm1-kontot.

1. Använd DistCp för att kopiera data från WASB till ett Data Lake Storage Gen1-konto.

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    Kommandot kopierar innehållet i mappen **/example/data/gutenberg/** i WASB till **/myfolder** i datasjölagringsgenm1-kontot.

1. På samma sätt kan du använda DistCp för att kopiera data från ett Data Lake Storage Gen1-konto till WASB.

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    Kommandot kopierar innehållet i **/myfolder** i datasjölagring gen1-kontot till **/example/data/gutenberg/** folder i WASB.

## <a name="performance-considerations-while-using-distcp"></a>Prestandaöverväganden när du använder DistCp

Eftersom DistCp-verktygets lägsta granularitet är en enda fil är det viktigaste parametern för att optimera den mot Data Lake Storage Gen1 om du anger det maximala antalet samtidiga kopior. Du kan styra antalet samtidiga kopior genom att ange parametern antal mappers ('m') på kommandoraden. Den här parametern anger det maximala antalet mappers som används för att kopiera data. Standardvärdet är 20.

Exempel:

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>Så här fastställer du antalet mappers som ska användas

Här är några riktlinjer som du kan använda.

* **Steg 1: Bestäm totalt YARN-minne** - Det första steget är att bestämma YARN-minnet som är tillgängligt för klustret där du kör DistCp-jobbet. Den här informationen är tillgänglig i Ambari-portalen som är associerad med klustret. Navigera till YARN och visa fliken **Configs** för att se YARN-minnet. För att få det totala YARN-minnet multiplicerar du YARN-minnet per nod med antalet noder som du har i klustret.

* **Steg 2: Beräkna antalet mappers** - Värdet **m** är lika med kvoten av totalt YARN-minne dividerat med YARN-behållarens storlek. YARN-containerstorleksinformationen finns också i Ambari-portalen. Navigera till YARN och visa fliken **Configs.** YARN-behållarstorleken visas i det här fönstret. Ekvationen för att komma fram till antalet mappers (**m**) är:

   `m = (number of nodes * YARN memory for each node) / YARN container size`

Exempel:

Anta att du har fyra D14v2s-noder i klustret och att du vill överföra 10 TB data från 10 olika mappar. Var och en av mapparna innehåller varierande mängder data och filstorlekarna i varje mapp är olika.

* Totalt GARN-minne - Från Ambari-portalen bestämmer du att YARN-minnet är 96 GB för en D14-nod. Så totalt YARN-minne för fyra nodkluster är:

   `YARN memory = 4 * 96GB = 384GB`

* Antal mappers - Från Ambari-portalen bestämmer du att YARN-behållarstorleken är 3072 för en D14-klusternod. Så antalet mappers är:

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

Om andra program använder minne kan du välja att bara använda en del av klustrets YARN-minne för DistCp.

### <a name="copying-large-datasets"></a>Kopiera stora datauppsättningar

När storleken på den datauppsättning som ska flyttas är stor (till exempel > 1 TB) eller om du har många olika mappar kan du överväga att använda flera DistCp-jobb. Det finns sannolikt ingen prestandavinst, men det sprider ut jobben så att om något jobb misslyckas, måste du bara starta om det specifika jobbet i stället för hela jobbet.

### <a name="limitations"></a>Begränsningar

* DistCp försöker skapa mappers som är lika stora för att optimera prestanda. Att öka antalet mappers kanske inte alltid ökar prestanda.

* DistCp är begränsat till endast en mapper per fil. Därför bör du inte ha fler mappers än du har filer. Eftersom DistCp bara kan tilldela en mapper till en fil, begränsar detta mängden samtidighet som kan användas för att kopiera stora filer.

* Om du har ett litet antal stora filer, dela upp dem i 256 MB filsegment för att ge dig mer potentiell samtidighet.

* Om du kopierar från ett Azure Blob-lagringskonto kan kopieringsjobbet begränsas på Blob-lagringssidan. Detta försämrar prestanda för ditt kopieringsjobb. Mer information om gränserna för Azure Blob-lagring finns i Azure Storage-gränser på [Azure-prenumerations- och tjänstgränser](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="see-also"></a>Se även

* [Kopiera data från Azure Storage-blobbar till Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Använda Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använda Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
