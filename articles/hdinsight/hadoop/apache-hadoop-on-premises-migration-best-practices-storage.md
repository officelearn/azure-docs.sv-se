---
title: Migrera lokala Apache Hadoop-kluster till Azure HDInsight - Metodtips för lagring
description: Läs om bästa metoder för att migrera lokala Hadoop-kluster till Azure HDInsight.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: c62a5384edf66fd9309bc7afcb50ada48e3fca7d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095293"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---storage-best-practices"></a>Migrera lokala Apache Hadoop-kluster till Azure HDInsight - Metodtips för lagring

Den här artikeln innehåller rekommendationer för att lagra data i Azure HDInsight-system. Det är en del i en serie som ger bästa praxis för att hjälpa migrera lokala Apache Hadoop-system till Azure HDInsight.

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>Välj rätt lagringssystemet för HDInsight-kluster

Katalogstruktur för en lokal Apache Hadoop File System (HDFS) skapas igen i Azure Storage eller Azure Data Lake Storage. Du kan sedan ta bort HDInsight-kluster som används för beräkning utan att förlora användardata. Båda tjänsterna kan användas som både standardfilsystemet och ett ytterligare filsystem för ett HDInsight-kluster. HDInsight-klustret och storage-kontot måste finnas i samma region.

### <a name="azure-storage"></a>Azure-lagring

HDInsight-kluster kan använda blob-behållare i Azure Storage som standardfilsystemet eller ett ytterligare filsystem. Lagringskontot som Standard-nivån stöds för användning med HDInsight-kluster. Premier nivå stöds inte. Standardcontainern lagrar klusterspecifik information, till exempel jobbhistorik och loggar. Det går inte att dela en blob-container som standardfilsystem över flera kluster.

De lagringskonton som definieras under skapandeprocessen och respektive nycklar som lagras i `%HADOOP_HOME%/conf/core-site.xml` på klusternoderna. De kan även nås under avsnittet ”anpassade core site” i HDFS-konfigurationen i Ambari UI. Lagringskontonyckeln krypteras som standard och ett anpassat dekryptering skript används för att dekryptera nycklarna innan överförs till Hadoop-daemon. Jobb inklusive Hive, MapReduce, Hadoop-strömmande och Pig, utför du en beskrivning av lagringskonton och metadata med dem.

Azure-lagring kan vara geo-replikerade. Även om geo-replikering ger geografisk återställning och dataredundans, en växling till den geo-replikerade platsen en avsevärd försämring av prestanda och kan medföra ytterligare kostnader. Rekommendationen är att välja geo-replikering omsorg och bara om värdet av data är den extra kostnaden.

Något av följande format kan användas för att komma åt data som lagras i Azure Storage:

|Dataformatet för åtkomst |Beskrivning |
|---|---|
|`wasb:///`|Åtkomst till standard storage med hjälp av dekrypterade kommunikation.|
|`wasbs:///`|Standard storage med krypterad kommunikation.|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|Används vid kommunikation med ett icke-standard storage-konto. |


[Azure Storage skalbarhets- och prestandamål](../../storage/common/storage-scalability-targets.md) visar en lista över aktuella begränsningar i Azure storage-konton. Om behov av programmet överskrider det för skalbarhetsmål för ett enda lagringskonto, kan du skapat programmet om du vill använda flera storage-konton och sedan partitionera dataobjekt över dessa lagringskonton.

[Azure Storage Analytics](../../storage/storage-analytics.md) tillhandahåller mått för alla lagringstjänster och Azure-portalen kan vara konfigurerade samla in mått för att vara visualiseras med diagram. Du kan skapa aviseringar som meddelar när tröskelvärdet har uppnåtts för mätvärden i storage resurs.

Azure Storage erbjuder [mjuk borttagning för blob-objekt](../../storage/blobs/storage-blob-soft-delete.md) för att återställa data när den av misstag ändras eller tas bort av ett program eller en annan användare för storage-konto.

Du kan skapa [blobögonblicksbilder](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob). En ögonblicksbild är en skrivskyddad version av en blob som utförs en gång i tid och den gör det möjligt att säkerhetskopiera en blob. När en ögonblicksbild har skapats, kan det läsas, kopieras, eller ta bort, men inte har ändrats.

> [!Note]
> För äldre versioner av lokala Hadoop-distributioner som inte har ”wasbs” certifikat behöver de för att importeras till arkivet med betrodda Java.

Följande metoder kan användas för att importera certifikat i arkivet med betrodda Java:

Hämta Azure Blob-ssl-certifikat till en fil

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

Importera filen ovan till Java-tillförlitlighetslagret på alla noder

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

Kontrollera att certifikatet har lagts till i arkivet med betrodda

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

Mer information finns i följande artiklar:

- [Använda Azure storage med Azure HDInsight-kluster](../hdinsight-hadoop-use-blob-storage.md)
- [Skalbarhets- och prestandamål i Azure Storage](../../storage/common/storage-scalability-targets.md)
- [Prestanda och skalbarhetschecklista för Microsoft Azure Storage](../../storage/common/storage-performance-checklist.md)
- [Övervaka, diagnostisera och felsöka Microsoft Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Övervaka ett lagringskonto i Azure portal](../../storage/common/storage-monitor-storage-account.md)

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Azure Data Lake Storage implementerar HDFS och POSIX style-modellen för åtkomstkontroll. Det ger bra detaljerade åtkomstkontroll förstklassigt integrering med AAD. Det finns ingen gräns för storleken på data som den kan lagra eller dess kapacitet att köra massivt parallelliserade analyser.

Mer information finns i följande artiklar:

- [Skapa HDInsight-kluster med Data Lake Storage med hjälp av Azure portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Använda Data Lake Storage med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-store.md)

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 är den senaste storage erbjuder. Den kombinerar de viktigaste funktionerna från första generationen av Azure Data Lake Storage med Hadoop-kompatibel fil system slutpunkt direktintegrerat i Azure Blob Storage. Den här förbättringen kombinerar skalning och kostnaden fördelarna med lagring av objekt med tillförlitlighet och prestanda som vanligtvis kopplade till endast lokala filsystem.

ADLS Gen 2 är byggt ovanpå [Azure Blob storage](../../storage/blobs/storage-blobs-introduction.md) och hjälper dig att samverka med data med hjälp av paradigm för lagring av system- och båda fil. Funktioner från [Azure Data Lake Storage Gen1](../../data-lake-store/index.md), till exempel filsystemen, filen säkerhet på radnivå och skala kombineras med nivåindelad lagring med låg kostnad, hög tillgänglighet/haveriberedskap och en stor SDK/verktyg ekosystemet från [Azure Blob storage](../../storage/blobs/storage-blobs-introduction.md). I Data Lake Storage Gen2 kvar samma egenskaper som objektlagring medan att lägga till fördelarna med ett gränssnitt filsystem optimerade för analytics arbetsbelastningar.

En grundläggande funktion i Data Lake Storage Gen2 är att lägga till en [hierarkiskt namnområde](../../storage/data-lake-storage/namespace.md) till Blob storage-tjänsten som organiserar objekt/filer i en hierarki med kataloger för högpresterande dataåtkomst. En hierarkisk struktur gör det möjligt för åtgärder som att byta namn på eller tar bort en katalog för att vara enkel atomiska metadataåtgärder på katalogen snarare än räkna upp och bearbetning av alla objekt som delar namnprefixet för katalogen.

Tidigare hade molnbaserad analys att angripa i delar av prestanda, hantering och säkerhet. Nyckel-funktionerna i Azure Data Lake Storage (ADLS) Gen2 är följande:

- **Hadoop-kompatibel åtkomst**: Azure Data Lake Storage Gen2 kan du hantera och komma åt data precis som med en [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Den nya [ABFS drivrutinen](../../storage/data-lake-storage/abfs-driver.md) är tillgängliga i alla Apache Hadoop-miljöer som ingår i [Azure HDInsight](../index.yml). Den här drivrutinen ger dig tillgång till data som lagras i Data Lake Storage Gen2.

- **En överordnad uppsättning av behörigheter av POSIX**: Säkerhetsmodellen för Data Lake Gen2 har fullständigt stöd för ACL och POSIX behörigheter tillsammans med vissa extra kornighet som är specifika för Data Lake Storage Gen2. Inställningarna kan konfigureras via Administrationsverktyg eller ramverk som Hive och Spark.

- **Kostnadseffektiv**: Lagringskapacitet för data Lake Storage Gen2 funktioner billigt och transaktioner. Som data övergångar via dess hela livscykel, faktureringstaxor ändras för att minimera kostnaderna via inbyggda funktioner som [livscykel för Azure Blob storage](../../storage/common/storage-lifecycle-management-concepts.md).

- **Fungerar med Blob storage-verktyg, ramverk och appar**: Data Lake Storage Gen2 fortsätter att fungera med en mängd olika verktyg, ramverk och program som finns i dag för Blob storage.

- **Optimerad drivrutinen**: Azure Blob-filsystem-drivrutinen (ABFS) är [optimerats specifikt](../../storage/data-lake-storage/abfs-driver.md) för analys av stordata. De motsvarande REST-API: erna exponeras via dfs-slutpunkt, dfs.core.windows.net.

Något av följande format kan användas för att komma åt data som lagras i ADLS Gen2:
- `abfs:///`: Åtkomst till standard Data Lake-lagring för klustret.
- `abfs[s]://file_system@account_name.dfs.core.windows.net`: Används vid kommunikation med en icke-standard Data Lake Storage.

Mer information finns i följande artiklar:

- [Introduktion till Azure Data Lake Storage Gen2](../../storage/data-lake-storage/introduction.md)
- [Azure Blob-filsystem-drivrutinen (ABFS.md)](../../storage/data-lake-storage/abfs-driver.md)
- [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>Skydda Azure Storage-nycklar i lokala Hadoop-klusterkonfiguration

Azure storage-nycklar som läggs till konfigurationsfilerna Hadoop upprätta en anslutning mellan på lokala HDFS och Azure Blob storage. De här nycklarna kan skyddas genom att kryptera dem med Hadoop ramverket. När krypterat, de lagras och används på ett säkert sätt.

**Att etablera autentiseringsuppgifterna:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**Lägga till ovanstående providersökvägen core-site.xml eller Ambari-konfigurationen under anpassade core-plats:**

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
> Egenskapen providern path kan också läggas till kommandoraden distcp istället för att lagra nyckel på klusternivå på core-site.xml på följande sätt:

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>Begränsa dataåtkomst i Azure storage med hjälp av SAS

HDInsight som standard har fullständig åtkomst till data i Azure Storage-konton som är associerade med klustret. Signaturer för delad åtkomst (SAS) för blob-behållaren kan användas för att begränsa åtkomsten till data, till exempel ge användare med skrivskyddad åtkomst till data.

### <a name="using-the-sas-token-created-with-python"></a>Med hjälp av SAS-token som skapats med python

1. Öppna den [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) filen och ändra följande värden:

    |Token-egenskapen|Beskrivning|
    |---|---|
    |policy_name|Namnet du använder för lagrade principen för att skapa.|
    |storage_account_name|Namnet på ditt lagringskonto.|
    |storage_account_key|Nyckel för lagringskontot.|
    |storage_container_name|Behållare i lagringskontot som du vill begränsa åtkomst till.|
    |example_file_path|Sökväg till en fil som har överförts till behållaren.|

2. Filen SASToken.py levereras med den `ContainerPermissions.READ + ContainerPermissions.LIST` behörigheter och kan justeras baserat på användningsfallet.

3. Kör skriptet på följande sätt: `python SASToken.py`

4. När skriptet har körts visas den SAS-token som liknar följande text: `sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. För att begränsa åtkomsten till en behållare med signatur för delad åtkomst, lägger du till en anpassad post core-plats-konfiguration för klustret under Ambari HDFS Peeringkonfigurationer avancerade anpassad core-site Lägg till egenskap.

6. Använd följande värden för den **nyckel** och **värdet** fält:

    **nyckeln**: `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **Värdet**: SAS-NYCKELN som returneras av Python-program från steg 4 ovan.

7. Klicka på den **Lägg till** knappen för att spara den här nyckeln och värdet och klicka sedan på den **spara** för att spara konfigurationsändringarna. När du uppmanas, lägga till en beskrivning av ändringen (”att lägga till SAS-lagringsåtkomst” till exempel) och klicka sedan på **spara**.

8. I Ambari-webbgränssnittet, Välj HDFS från listan till vänster och välj sedan **starta om alla påverkade** från de åtgärder som tjänsten listrutan till höger. När du uppmanas, väljer **bekräfta starta om alla**.

9. Upprepa processen för MapReduce2 och YARN.

Det finns tre viktiga saker att komma ihåg om användningen av SAS-token i Azure:

1. När SAS-token har skapats med ”läsa + LIST-behörigheter, användare som har åtkomst till Blob-behållare med den SAS-token inte” skriva och ta bort ”data. Användare som har åtkomst till Blob-behållare med den SAS-token och försök en skrivning eller för att ta bort, visas ett meddelande som `"This request is not authorized to perform this operation"`.

2. När SAS-token genereras med `READ + LIST + WRITE` behörigheter (att begränsa `DELETE` endast), kommandon som `hadoop fs -put` först skriva till en `\_COPYING\_` filen och försök sedan att byta namn på filen. Den här åtgärden för HDFS som mappar till en `copy+delete` för WASB. Eftersom den `DELETE` behörighet har inte angetts, ”put” skulle misslyckas. Den `\_COPYING\_` åtgärden är en Hadoop-funktion avsedd att ge vissa samtidighetskontroll. Det finns för närvarande inget sätt att begränsa bara åtgärden ”ta bort” utan att påverka ”” skrivåtgärder samt.

3. Tyvärr provider för hadoop-autentiseringsuppgifter och viktiga avkrypteringsprovider (ShellDecryptionKeyProvider) för närvarande arbetar inte med SAS-token och så det för närvarande inte skyddas från synlighet.

Mer information finns i [Använd Azure Storage signaturer för delad åtkomst att begränsa åtkomsten till data i HDInsight](../hdinsight-storage-sharedaccesssignature-permissions.md).

## <a name="use-data-encryption-and-replication"></a>Använd datakryptering och replikering

Alla data som skrivs till Azure Storage krypteras automatiskt med [Storage Service Encryption (SSE)](../../storage/common/storage-service-encryption.md). Data i Azure storage-konto replikeras alltid för hög tillgänglighet. När du skapar ett lagringskonto kan välja du någon av följande replikeringsalternativ:

- [Lokalt redundant lagring (LRS)](../../storage/common/storage-redundancy-lrs.md)
- [Zonredundant lagring (ZRS)](../../storage/common/storage-redundancy-zrs.md)
- [Geo-redundant lagring (GRS)](../../storage/common/storage-redundancy-grs.md)
- [Geo-redundant lagring med läsbehörighet (RA-GRS)](../../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Azure Data Lake Storage tillhandahåller lokalt redundant lagring (LRS), men du bör också kopiera viktiga data till ett annat Data Lake Storage-konto i en annan region med en frekvens som är anpassad efter behoven i återställningsplanen. Det finns en mängd olika metoder för att kopiera data, inklusive [ADLCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md), DistCp, [Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md), eller [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md). Vi rekommenderar också att tillämpa åtkomstprinciper för Data Lake Storage-konto för att förhindra oavsiktlig borttagning.

Mer information finns i följande artiklar:

- [Azure storage-replikering](../../storage/common/storage-redundancy.md)
- [Vägledning för haveriberedskap för Azure Data Lake Storage (ADLS)](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>Bifoga ytterligare Azure storage-konton ska ingå i klustret

Under skapandeprocessen HDInsight väljs ett Azure Storage-konto eller Azure Data Lake storage-konto som standardfilsystem. Förutom den här standardkontot för lagring, kan ytterligare lagringskonton läggas från samma Azure-prenumeration eller andra Azure-prenumerationer under skapandeprocessen klustret eller när du har skapat ett kluster.

Ytterligare storage-konto kan läggas till i en på följande sätt:
- Ambari HDFS Config avancerade anpassade core-site lägga till lagring kontonamn och nyckel att starta om tjänsterna
- Med hjälp av [skripta åtgärd](../hdinsight-hadoop-add-storage.md) genom att skicka lagringskontonamn och nyckel

> [!Note]
> I giltig användningsfall begränsningar i Azure storage kan du öka via en begäran till [Azure-supporten](https://azure.microsoft.com/support/faq/).

Mer information finns i följande artiklar:
- [Lägga till ytterligare lagringskonton till HDInsight](../hdinsight-hadoop-add-storage.md)

## <a name="next-steps"></a>Nästa steg

Läs nästa artikel i den här serien:

- [Data migration bästa praxis för lokalt till Azure HDInsight Hadoop-migrering](apache-hadoop-on-premises-migration-best-practices-data-migration.md)
