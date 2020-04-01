---
title: 'Lagring: Migrera lokala Apache Hadoop till Azure HDInsight'
description: Lär dig metodtips för lagring för att migrera lokala Hadoop-kluster till Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: f19d4adad675cdf95f59aca0f752f46211b75e8f
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436921"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight"></a>Migrera lokala Apache Hadoop-kluster till Azure HDInsight

Den här artikeln innehåller rekommendationer för datalagring i Azure HDInsight-system. Det är en del av en serie som innehåller metodtips för att hjälpa till med att migrera lokala Apache Hadoop-system till Azure HDInsight.

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>Välj rätt lagringssystem för HDInsight-kluster

Den lokala katalogstrukturen för Apache Hadoop File System (HDFS) kan återskapas i Azure Storage eller Azure Data Lake Storage. Du kan sedan ta bort HDInsight-kluster som används för beräkning utan att förlora användardata på ett säkert sätt. Båda tjänsterna kan användas både som standardfilsystem och ett ytterligare filsystem för ett HDInsight-kluster. HDInsight-klustret och lagringskontot måste finnas i samma region.

### <a name="azure-storage"></a>Azure Storage

HDInsight-kluster kan använda blob-behållaren i Azure Storage som standardfilsystem eller ytterligare filsystem.Lagringskonto på standardnivå stöds för användning med HDInsight-kluster. Premier-nivån stöds inte. Standardcontainern lagrar klusterspecifik information, till exempel jobbhistorik och loggar.Det går inte att dela en blob-behållare som standardfilsystem för flera kluster.

De lagringskonton som definieras i skapandeprocessen och `%HADOOP_HOME%/conf/core-site.xml` deras respektive nycklar lagras i på klusternoderna. De kan också nås under avsnittet "Anpassad kärnplats" i HDFS-konfigurationen i Ambari-användargränssnittet. Lagringskontonyckeln krypteras som standard och ett anpassat dekrypteringsskript används för att dekryptera nycklarna innan de skickas vidare till Hadoop-demoner. Jobben, inklusive Hive, MapReduce, Hadoop streaming och Pig, har en beskrivning av lagringskonton och metadata med dem.

Azure Storage kan geo-replikeras. Även om geo-replikering ger geografisk återställning och dataredundans, påverkar en redundans till den geo-replikerade platsen prestanda och det kan medföra extra kostnader. Rekommendationen är att välja geo-replikering klokt och endast om värdet av data är värt den extra kostnaden.

Ett av följande format kan användas för att komma åt data som lagras i Azure Storage:

|Format för dataåtkomst |Beskrivning |
|---|---|
|`wasb:///`|Få tillgång till standardlagring med okrypterad kommunikation.|
|`wasbs:///`|Få tillgång till standardlagring med hjälp av krypterad kommunikation.|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|Används när du kommunicerar med ett lagringskonto som inte är standard. |

[Skalbarhetsmål för standardlagringskonton](../../storage/common/scalability-targets-standard-account.md) visar de aktuella gränserna för Azure Storage-konton. Om behoven för programmet överskrider skalbarhetsmålen för ett enskilt lagringskonto kan programmet skapas för att använda flera lagringskonton och sedan partitionera dataobjekt över dessa lagringskonton.

[Azure Storage Analytics](../../storage/storage-analytics.md) tillhandahåller mått för alla lagringstjänster och Azure-portalen kan konfigureras samla in mått som ska visualiseras via diagram. Aviseringar kan skapas för att meddela när tröskelvärden har uppnåtts för lagringsresursmått.

Azure Storage erbjuder [mjuk borttagning för blob-objekt för](../../storage/blobs/storage-blob-soft-delete.md) att återställa data när de av misstag ändras eller tas bort av ett program eller annan lagringskontoanvändare.

Du kan skapa [blob-ögonblicksbilder](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob). En ögonblicksbild är en skrivskyddad version av en blob som tas vid en tidpunkt och det ger ett sätt att säkerhetskopiera en blob. När en ögonblicksbild har skapats kan den läsas, kopieras eller tas bort, men inte ändras.

> [!Note]
> För äldre versioner av lokala Hadoop-distributioner som inte har "wasbs"-certifikatet måste de importeras till Java-förtroendearkivet.

Följande metoder kan användas för att importera certifikat till Java-förtroendearkivet:

Ladda ned Azure Blob TLS/SSL-certifikatet till en fil

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

Importera ovanstående fil till Java trust store på alla noder

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

Kontrollera att det tillagda certifikatet finns i förtroendearkivet

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

Mer information finns i följande artiklar:

- [Använda Azure Storage med Azure HDInsight-kluster](../hdinsight-hadoop-use-blob-storage.md)
- [Skalbarhetsmål för standardlagringskonton](../../storage/common/scalability-targets-standard-account.md)
- [Skalbarhets- och prestandamål för Blob-lagring](../../storage/blobs/scalability-targets.md)
- [Prestanda och skalbarhetschecklista för Microsoft Azure Storage](../../storage/common/storage-performance-checklist.md)
- [Övervaka, diagnostisera och felsök Microsoft Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Övervaka ett lagringskonto i Azure-portalen](../../storage/common/storage-monitor-storage-account.md)

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Azure Data Lake Storage implementerar åtkomstkontrollmodell för HDFS- och POSIX-format. Det ger förstklassig integration med AAD för finkornig åtkomstkontroll. Det finns inga gränser för storleken på data som den kan lagra, eller dess förmåga att köra massivt parallella analyser.

Mer information finns i följande artiklar:

- [Skapa HDInsight-kluster med DataSjölagring med hjälp av Azure-portalen](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Använda Datasjölagring med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-store.md)

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 är det senaste lagringserbjudandet. Den förenar kärnfunktionerna från den första generationen av Azure Data Lake Storage med en Hadoop-kompatibel filsystemslutpunkt som är direkt integrerad i Azure Blob Storage. Den här förbättringen kombinerar skalnings- och kostnadsfördelarna med objektlagring med den tillförlitlighet och prestanda som vanligtvis endast associeras med lokala filsystem.

ADLS Gen 2 bygger på [Azure Blob-lagring](../../storage/blobs/storage-blobs-introduction.md) och låter dig samverka med data med hjälp av både filsystem och objektlagringsparadigm. Funktioner från [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml), till exempel filsystemssenmantik, säkerhet på filnivå och skala kombineras med låg kostnad, nivåinställd lagring, hög tillgänglighet/katastrofåterställning och ett stort SDK/verktygsekosystem från Azure [Blob-lagring](../../storage/blobs/storage-blobs-introduction.md). I Data Lake Storage Gen2 finns alla egenskaper hos objektlagring kvar samtidigt som fördelarna med ett filsystemgränssnitt som är optimerat för analysarbetsbelastningar lägger till.

En grundläggande funktion i Data Lake Storage Gen2 är tillägget av ett [hierarkiskt namnområde](../../storage/data-lake-storage/namespace.md) till Blob-lagringstjänsten, som organiserar objekt/filer i en hierarki av kataloger för performant dataåtkomst.Den hierarkiska strukturen gör det möjligt för åtgärder som att byta namn på eller ta bort en katalog som enstaka atoma metadataåtgärder i katalogen i stället för att räkna upp och bearbeta alla objekt som delar namnprefixet för katalogen.

Tidigare var molnbaserade analyser tvungna att kompromissa inom områdena prestanda, hantering och säkerhet. De viktigaste funktionerna i ADLS-gen2 (Azure Data Lake Storage) Gen2 är följande:

- **Hadoop-kompatibel åtkomst:** Azure Data Lake Storage Gen2 gör att du kan hantera och komma åt data på samma sätt som med ett [HADoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Den nya [ABFS-drivrutinen](../../storage/data-lake-storage/abfs-driver.md) är tillgänglig i alla Apache Hadoop-miljöer som ingår i [Azure HDInsight](../index.yml). Med den här drivrutinen kan du komma åt data som lagras i Data Lake Storage Gen2.

- **En superuppsättning av POSIX-behörigheter**: Säkerhetsmodellen för Data Lake Gen2 stöder helt ACL- och POSIX-behörigheter tillsammans med några extra granularitet som är specifika för Data Lake Storage Gen2. Inställningar kan konfigureras via administrationsverktyg eller via ramverk som Hive och Spark.

- **Kostnadseffektivt:** Data Lake Storage Gen2 har billig lagringskapacitet och transaktioner. När data övergår genom hela livscykeln ändras faktureringsfrekvensen för att minimera kostnaderna via inbyggda funktioner som [Azure Blob-lagringslivscykel](../../storage/common/storage-lifecycle-management-concepts.md).

- **Fungerar med Blob-lagringsverktyg, ramverk och appar**: Data Lake Storage Gen2 fortsätter att arbeta med ett brett utbud av verktyg, ramverk och program som finns idag för Blob-lagring.

- **Optimerad drivrutin:** Azure Blob Filesystem-drivrutinen (ABFS) är [optimerad speciellt](../../storage/data-lake-storage/abfs-driver.md) för stordataanalys. Motsvarande REST-API:er visas via dfs-slutpunkten, dfs.core.windows.net.

Ett av följande format kan användas för att komma åt data som lagras i ADLS Gen2:
- `abfs:///`: Öppna standardlagringen för datasjölagring för klustret.
- `abfs://file_system@account_name.dfs.core.windows.net`: Används vid kommunikation med en datasjölagring som inte är standard.

Mer information finns i följande artiklar:

- [Introduktion till Azure Data Lake Storage Gen2](../../storage/data-lake-storage/introduction.md)
- [Azure Blob Filesystem-drivrutinen (ABFS.md)](../../storage/data-lake-storage/abfs-driver.md)
- [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>Säkra Azure Storage-nycklar i lokal Hadoop-klusterkonfiguration

Azure Storage-nycklarna som läggs till i Hadoop-konfigurationsfilerna upprättar anslutning mellan lokal HDFS- och Azure Blob-lagring. Dessa nycklar kan skyddas genom att kryptera dem med Hadoop-referensproviderramverket. När de har krypterats kan de lagras och nås på ett säkert sätt.

**Så här etablerar du autentiseringsuppgifterna:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**Så här lägger du till ovanstående providersökväg till core-site.xml eller i Ambari-konfigurationen under anpassad kärnplats:**

```xml
<property>
    <name>hadoop.security.credential.provider.path</name>
        <value>
        jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks
        </value>
    <description>Path to interrogate for protected credentials.</description>
</property>
```

> [!Note]
> Egenskapen providersökväg kan också läggas till på distcp-kommandoraden i stället för att lagra nyckeln på klusternivå på core-site.xml enligt följande:

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>Begränsa Azure Storage-dataåtkomst med SAS

HDInsight har som standard fullständig åtkomst till data i Azure Storage-konton som är associerade med klustret. SAS (Shared Access Signatures) på blob-behållaren kan användas för att begränsa åtkomsten till data, till exempel ge användarna skrivskyddad åtkomst till data.

### <a name="using-the-sas-token-created-with-python"></a>Använda SAS-token som skapats med python

1. Öppna [SASToken.py-filen](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) och ändra följande värden:

    |Egenskapen Token|Beskrivning|
    |---|---|
    |policy_name|Namnet som ska användas för den lagrade principen att skapa.|
    |storage_account_name|Namnet på ditt lagringskonto.|
    |storage_account_key|Nyckeln för lagringskontot.|
    |storage_container_name|Behållaren i lagringskontot som du vill begränsa åtkomsten till.|
    |example_file_path|Sökvägen till en fil som överförs till behållaren.|

2. Den SASToken.py filen levereras `ContainerPermissions.READ + ContainerPermissions.LIST` med behörigheterna och kan justeras baserat på användningsfallet.

3. Kör skriptet på följande sätt:`python SASToken.py`

4. Den visar SAS-token som liknar följande text när skriptet är klart:`sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. Om du vill begränsa åtkomsten till en behållare med Signature för delad åtkomst lägger du till en anpassad post i konfigurationen av kärnplatsen för klustret under egenskapen Ambari HDFS Configs Advanced Custom core-site Add.

6. Använd följande värden för fälten **Nyckel** och **värde:**

    **Nyckel:** `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **Värde:** SAS-nyckeln returneras av Python-programmet FRÅN steg 4 ovan.

7. Klicka på knappen **Lägg** till om du vill spara den här nyckeln och värdet och klicka sedan på knappen **Spara** för att spara konfigurationsändringarna. När du uppmanas att lägga till en beskrivning av ändringen ("lägga till SAS-lagringsåtkomst" till exempel) och klicka sedan på **Spara**.

8. I webbgränssnittet i Ambari väljer du HDFS i listan till vänster och väljer sedan **Starta om alla som påverkas** från listrutan Serviceåtgärder till höger. När du uppmanas till det väljer du **Bekräfta omstart av alla**.

9. Upprepa denna process för MapReduce2 och YARN.

Det finns tre viktiga saker att komma ihåg om användningen av SAS-token i Azure:

1. När SAS-token skapas med behörigheter för "LÄS + LISTA" kan användare som kommer åt Blob-behållaren med den SAS-token inte "skriva och ta bort" data. Användare som kommer åt Blob-behållaren med den SAS-token och `"This request is not authorized to perform this operation"`provar en skriv- eller borttagningsåtgärd får ett meddelande som .

2. När SAS-token genereras `READ + LIST + WRITE` med behörigheter `DELETE` (för att `hadoop fs -put` begränsa endast), `\_COPYING\_` kommandon som först skriva till en fil och sedan försöka byta namn på filen. Denna HDFS-åtgärd `copy+delete` mappar till en för WASB. Eftersom `DELETE` tillståndet inte angavs, skulle "put" misslyckas. Åtgärden `\_COPYING\_` är en Hadoop-funktion avsedd att ge viss samtidighetskontroll. För närvarande finns det inget sätt att begränsa bara "DELETE" operation utan att påverka "WRITE" åtgärder också.

3. Tyvärr fungerar inte hadoop-autentiseringsuppgifter och dekrypteringsnyckelprovider (ShellDecryptionKeyProvider) för närvarande inte med SAS-token och så att den för närvarande inte kan skyddas från synlighet.

Mer information finns i [Använda Azure Storage Shared Access Signaturer för att begränsa åtkomsten till data i HDInsight](../hdinsight-storage-sharedaccesssignature-permissions.md).

## <a name="use-data-encryption-and-replication"></a>Använda datakryptering och replikering

Alla data som skrivs till Azure Storage krypteras automatiskt med [SSE (Storage Service Encryption).](../../storage/common/storage-service-encryption.md) Data i Azure Storage-kontot replikeras alltid för hög tillgänglighet.När du skapar ett lagringskonto kan du välja något av följande replikeringsalternativ:

- [Lokalt redundant lagring (LRS)](../../storage/common/storage-redundancy-lrs.md)
- [Zonupptraktad lagring (ZRS)](../../storage/common/storage-redundancy-zrs.md)
- [Geo-redundant lagring (GRS)](../../storage/common/storage-redundancy-grs.md)
- [Geo-redundant lagring med läsbehörighet (RA-GRS)](../../storage/common/storage-redundancy.md)

Azure Data Lake Storage tillhandahåller lokalt redundant lagring (LRS) men du bör också kopiera viktiga data till ett annat Data Lake Storage-konto i en annan region med en frekvens anpassad till behoven i katastrofåterställningsplanen.Det finns olika metoder för att kopiera data, inklusive [ADLCopy,](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) [DistCp,](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html) [Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md)eller [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md).Vi rekommenderar också att du tillämpar åtkomstprinciper för Data Lake Storage-kontot för att förhindra oavsiktlig borttagning.

Mer information finns i följande artiklar:

- [Azure Storage-replikering](../../storage/common/storage-redundancy.md)
- [Katastrofvägledning för Azure Data Lake Storage (ADLS)](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>Bifoga ytterligare Azure Storage-konton i klustret

Under skapandet av HDInsight väljs ett Azure Storage-konto eller Azure Data Lake Storage-konto som standardfilsystem. Utöver det här standardlagringskontot kan ytterligare lagringskonton läggas till från samma Azure-prenumeration eller olika Azure-prenumerationer under klusterskapandeprocessen eller efter att ett kluster har skapats.

Ytterligare lagringskonto kan läggas till på ett på följande sätt:
- Ambari HDFS Config Advanced Custom core-site Lägg till lagringskontonamn och nyckel Starta om tjänsterna
- Använda [skriptåtgärden](../hdinsight-hadoop-add-storage.md) genom att skicka namn och nyckel för lagringskontot

> [!Note]
> I giltiga användningsfall kan gränserna för Azure-lagring ökas via en begäran till [Azure Support](https://azure.microsoft.com/support/faq/).

Mer information finns i [Add additional storage accounts to HDInsight](../hdinsight-hadoop-add-storage.md) (Lägga till fler lagringskonton till HDInsight).

## <a name="next-steps"></a>Nästa steg

Läs nästa artikel i den här serien: [Metodtips för datamigrering för lokal till Azure HDInsight Hadoop-migrering](apache-hadoop-on-premises-migration-best-practices-data-migration.md).
