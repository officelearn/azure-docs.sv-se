---
title: Använd Azure Data Box för att migrera data från lokala HDFS lagra till Azure Storage
description: Migrera data från en lokal HDFS-databas till Azure Storage
services: storage
author: normesta
ms.service: storage
ms.date: 06/11/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 4445a8566c04d30cfb8743cbd33623f2e23f0dde
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595406"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Använda Azure Data Box för att migrera data från en lokal HDFS-databas till Azure Storage

Du kan migrera data från ett lokalt HDFS Arkiv av ditt Hadoop-kluster till Azure Storage (blob-lagring eller Data Lake Storage Gen2) med hjälp av en Data Box-enhet. Du kan välja från en 80 TB Data box-enhet eller en 770 TB Data Box-aktiverat.

Den här artikeln hjälper dig att utföra dessa uppgifter:

> [!div class="checklist"]
> * Förbered att migrera dina data.
> * Kopiera dina data till en Data Box eller en Data Box tung enhet.
> * Skicka tillbaka enheten till Microsoft.
> * Flytta data till Data Lake Storage Gen2.

## <a name="prerequisites"></a>Förutsättningar

Du behöver dessa saker att slutföra migreringen.

* Två lagringskonton; som har ett hierarkiskt namnområde är aktiverat och en som inte.

* Ett lokalt Hadoop-kluster som innehåller källdata.

* En [Azure Data Box-enhet](https://azure.microsoft.com/services/storage/databox/).

  * [Beställa Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) eller [Data Box aktiverat](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). När ordning din enhet, Kom ihåg att välja ett lagringskonto som **inte** har aktiverade på den hierarkiska namnområden. Det beror på att Data Box-enheter inte stöder ännu direkt inmatning i Azure Data Lake Storage Gen2. Du måste kopiera till ett lagringskonto och gör sedan en kopia till Gen2 ADLS-konto. Instruktioner för detta anges i stegen nedan.

  * Ansluta och ansluta din [Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) eller [Data Box tung](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) till ett lokalt nätverk.

Om du är redo kan vi ska börja.

## <a name="copy-your-data-to-a-data-box-device"></a>Kopiera dina data till en Data Box-enhet

Om dina data passar in i en enskild Data Box-enhet, ska du kopiera data till Data Box-enhet. 

Om datastorleken på din överskrider kapaciteten för Data Box-enhet kan sedan använda den [valfria metod för att dela upp data på flera enheter för Data Box](#appendix-split-data-across-multiple-data-box-devices) och utför det här steget. 

Om du vill kopiera data från ditt lokala HDFS store till en Data Box-enhet, ska du konfigurera några saker och sedan använder den [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) verktyget.

Följ dessa steg för att kopiera data via REST API: er för Blob/Object-lagring till din Data Box-enhet. REST API-gränssnittet gör enheten visas som ett HDFS-Arkiv i klustret.

1. Identifiera primitiver för säkerhet och anslutningen att ansluta till REST-gränssnittet på Data Box eller Data Box tung innan du kopierar data via REST. Logga in på det lokala webbtjänst Konfigurationssgränssnittet i Data Box och gå till **Anslut och kopiera** sidan. Mot Azure storage-konto för din enhet under **åtkomstinställningar**, leta upp och välj **REST**.

    ![”Anslut och Kopiera”-sida](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. I storage-konto för åtkomst och dialogrutan för filöverföring av data, kopiera den **Blobbtjänstens slutpunkt** och **lagringskontonyckel**. Från blob-tjänstens slutpunkt utelämna den `https://` och avslutande snedstreck.

    I det här fallet slutpunkten är: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. Värddelen av den URI som du använder är: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Ett exempel finns i så här [Anslut till REST över http](/azure/databox/data-box-deploy-copy-data-via-rest). 

     ![Dialogrutan ”åtkomst till lagringskontot och ladda upp data”](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Lägga till slutpunkten och Data Box eller Data Box tung noden IP-adressen `/etc/hosts` på varje nod.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    Om du använder någon annan mekanism för DNS, bör du kontrollera att Data Box-slutpunkt kan matchas.

4. Ställ in variabeln shell `azjars` till platsen för den `hadoop-azure` och `azure-storage` jar-filer. Du hittar de här filerna under installationskatalogen Hadoop.

    För att avgöra om de här filerna finns, använder du följande kommando: `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`. Ersätt den `<hadoop_install_dir>` med sökvägen till den katalog där du har installerat Hadoop. Var noga med att använda fullständiga sökvägar.

    Exempel:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Skapa behållaren som du vill använda för kopiering av data. Som en del av det här kommandot bör du också ange en målkatalog. Detta kan vara en dummy målkatalog nu.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Ersätt den `<blob_service_endpoint>` platshållare med namnet på blobbtjänstens slutpunkt.

    * Ersätt den `<account_key>` med åtkomstnyckeln för ditt konto.

    * Ersätt den `<container-name>` platshållare med namnet på behållaren.

    * Ersätt den `<destination_directory>` platshållare med namnet på den katalog som du vill kopiera dina data till.

6. Kör en lista-kommando för att se till att dina behållare och katalogen har skapats.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Ersätt den `<blob_service_endpoint>` platshållare med namnet på blobbtjänstens slutpunkt.

   * Ersätt den `<account_key>` med åtkomstnyckeln för ditt konto.

   * Ersätt den `<container-name>` platshållare med namnet på behållaren.

7. Kopiera data från Hadoop HDFS till Data Box Blob storage till den behållare som du skapade tidigare. Om den katalog som du kopierar till inte hittas skapas kommandot den automatiskt.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Ersätt den `<blob_service_endpoint>` platshållare med namnet på blobbtjänstens slutpunkt.

    * Ersätt den `<account_key>` med åtkomstnyckeln för ditt konto.

    * Ersätt den `<container-name>` platshållare med namnet på behållaren.

    * Ersätt den `<exlusion_filelist_file>` platshållare med namnet på den fil som innehåller listan över undantagna filer.

    * Ersätt den `<source_directory>` platshållare med namnet på den katalog som innehåller de data som du vill kopiera.

    * Ersätt den `<destination_directory>` platshållare med namnet på den katalog som du vill kopiera dina data till.

    Den `-libjars` alternativet används för att göra den `hadoop-azure*.jar` och beroende `azure-storage*.jar` filer tillgängliga för `distcp`. Detta kan redan ha vissa kluster.

    I följande exempel visas hur `distcp` används för att kopiera data.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    Att kopiera snabbare:

    * Försök att ändra antalet Mappningskomponenter. (I ovanstående exempel används `m` = 4 Mappningskomponenter.)

    * Försök att köra flera `distcp` parallellt.

    * Kom ihåg att stora filer bättre prestanda än små filer.

## <a name="ship-the-data-box-to-microsoft"></a>Leverera Data Box till Microsoft

Följ dessa steg för att förbereda och skicka Data Box-enhet till Microsoft.

1. Först [Förbered för att skicka på din Data Box eller Data Box tung](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).

2. När enheten förberedelser har slutförts kan du ladda ner BOM-filer. Du ska använda dessa BOM eller manifest filer senare för att kontrollera data har överförts till Azure.

3. Stäng enheten och ta bort kablarna.

4. Schemalägga en upphämtning med Avbrottsfria.

    * Data Box-enheter, se [leverera Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

    * Data Box tung enheter, se [leverera din Data Box tung](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).

5. När Microsoft tar emot din enhet, det är anslutet till datacenternätverket och data har överförts till storage-kontot som du har angett (med hierarkisk namnområden inaktiverad) när du placerade ordningen som enheten. Kontrollera att alla dina data har överförts till Azure mot BOM-filer. Du kan nu flytta dessa data till ett lagringskonto för Data Lake Storage Gen2.

## <a name="move-the-data-into-azure-data-lake-storage-gen2"></a>Flytta data till Azure Data Lake Storage Gen2

Du har redan data i Azure Storage-kontot. Nu ska du kopiera data till ditt Azure Data Lake storage-konto och tillämpa behörigheter för åtkomst till filer och kataloger.

> [!NOTE]
> Det här steget krävs om du använder Azure Data Lake Storage Gen2 som datalager. Om du använder bara ett blob storage-konto utan hierarkiskt namnområde som datalager kan du hoppa över det här avsnittet.

### <a name="copy-data-to-the-azure-data-lake-storage-gen-2-account"></a>Kopiera data till Azure Data Lake Storage Gen 2-konto

Du kan kopiera data med hjälp av Azure Data Factory eller genom att använda ditt Azure-baserade Hadoop-kluster.

* Om du vill använda Azure Data Factory, se [Azure Data Factory för att flytta data till ADLS Gen2](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). Se till att ange **Azure Blob Storage** som källa.

* Om du vill använda ditt Azure-baserade Hadoop-kluster, kör du kommandot DistCp:

    ```bash
    hadoop distcp -Dfs.azure.account.key.<source_account>.dfs.windows.net=<source_account_key> abfs://<source_container> @<source_account>.dfs.windows.net/<source_path> abfs://<dest_container>@<dest_account>.dfs.windows.net/<dest_path>
    ```

    * Ersätt den `<source_account>` och `<dest_account>` platshållarna med namnen på de käll- och storage-kontona.

    * Ersätt den `<source_container>` och `<dest_container>` platshållarna med namnen på käll- och behållare.

    * Ersätt den `<source_path>` och `<dest_path>` platshållare med katalogsökvägar käll- och målservrar.

    * Ersätt den `<source_account_key>` med åtkomstnyckeln för lagringskontot som innehåller data.

    Det här kommandot kopierar både data och metadata från ditt lagringskonto till ditt Data Lake Storage Gen2 storage-konto.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Skapa ett huvudnamn för tjänsten för ditt konto i Azure Data Lake Storage Gen2

Om du vill skapa ett huvudnamn för tjänsten [så här: Använd portalen för att skapa ett Azure AD-program och huvudnamn för tjänsten som kan komma åt resurser](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

* När du utför stegen i avsnittet [Tilldela programmet till en roll](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) i artikeln ska du tilldela rollen **Storage Blob Data-deltagare** till tjänstens huvudnamn.

* När du utför stegen i den [få värden för att logga in](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) avsnittet av artikeln, program-ID och klientens hemliga värden i en textfil. Du kommer att behöva dem snart.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Generera en lista med kopierade filer med deras behörigheter

Kör följande kommando från en lokal Hadoop-kluster:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Det här kommandot genererar en lista över kopierade filer med deras behörigheter.

> [!NOTE]
> Beroende på antalet filer i med HDFS, kan det här kommandot ta lång tid att köra.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Generera en lista över identiteter och mappa den till Azure Active Directory (Lägg till) identiteter

1. Ladda ned den `copy-acls.py` skript. Se den [hämta hjälpskript och ställa in din edge-nod för att köra dem](#download-helper-scripts) i den här artikeln.

2. Kör detta kommando för att generera en lista med unika identiteter.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Det här skriptet skapar en fil med namnet `id_map.json` som innehåller de identiteter som du behöver för att mappa till ADD-baserade identiteter.

3. Öppna den `id_map.json` filen i en textredigerare.

4. Varje JSON-objekt som visas i filen, uppdatera den `target` attributet AAD UPN User Principal Name ()-eller objekt-ID (OID) med rätt mappad identitet. När du är klar kan du spara filen. Du behöver den här filen i nästa steg.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Tillämpa behörigheter för kopierade filer och tillämpa identitet mappningar

Kör detta kommando för att ge behörigheter till de data som du kopierade till Gen2 för Data Lake Storage-konto:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Ersätt platshållaren `<storage-account-name>` med namnet på ditt lagringskonto.

* Ersätt den `<container-name>` platshållare med namnet på behållaren.

* Ersätt den `<application-id>` och `<client-secret>` platshållare med program-ID och klienthemlighet som du samlat in när du skapade tjänstens huvudnamn.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Tillägg: Dela data på flera Data Box-enheter

Innan du flyttar dina data till en Data Box-enhet måste du se till att dina data ordnas för att få plats på en Data Box-enhet och utelämna onödiga filer om du vill ladda ned vissa helper-skript.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Hämta hjälpskript och konfigurera din edge-nod för att köra dem.

1. Från din edge eller huvudnoden för ditt lokala Hadoop-kluster, kör du följande kommando:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Det här kommandot klonar GitHub-lagringsplatsen som innehåller hjälpkomponentskript.

2. Kontrollera att som har den [jq](https://stedolan.github.io/jq/) paketet är installerat på den lokala datorn.

   ```bash
   
   sudo apt-get install jq
   ```

3. Installera den [begäranden](http://docs.python-requests.org/en/master/) python-paketet.

   ```bash
   
   pip install requests
   ```

4. Ställ in behörighet att köra de nödvändiga skripten.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Se till att dina data är ordnade efter på en Data Box-enhet

Om storleken på dina data överstiger storleken på en enskild Data Box-enhet kan du dela upp filer i grupper som du kan lagra till flera Data Box-enheter.

Om dina data inte överstiger storleken på ett enskilt Data Box-enhet, kan du gå vidare till nästa avsnitt.

1. Med förhöjd behörighet kör den `generate-file-list` skript som du laddade ned genom att följa riktlinjerna i föregående avsnitt.

   Här följer en beskrivning av kommandoparametrarna:

   ```
   sudo -u hdfs ./generate-file-list.py [-h] [-s DATABOX_SIZE] [-b FILELIST_BASENAME]
                    [-f LOG_CONFIG] [-l LOG_FILE]
                    [-v {DEBUG,INFO,WARNING,ERROR}]
                    path

   where:
   positional arguments:
   path                  The base HDFS path to process.

   optional arguments:
   -h, --help            show this help message and exit
   -s DATABOX_SIZE, --databox-size DATABOX_SIZE
                        The size of each Data Box in bytes.
   -b FILELIST_BASENAME, --filelist-basename FILELIST_BASENAME
                        The base name for the output filelists. Lists will be
                        named basename1, basename2, ... .
   -f LOG_CONFIG, --log-config LOG_CONFIG
                        The name of a configuration file for logging.
   -l LOG_FILE, --log-file LOG_FILE
                        Name of file to have log output written to (default is
                        stdout/stderr)
   -v {DEBUG,INFO,WARNING,ERROR}, --log-level {DEBUG,INFO,WARNING,ERROR}
                        Level of log information to output. Default is 'INFO'.
   ```

2. Kopiera den genererade filen innehåller till HDFS så att de är tillgängliga för den [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) jobbet.

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Undanta onödiga filer

Du måste du undanta vissa kataloger från DisCp jobbet. Till exempel undanta kataloger som innehåller information om tillstånd som håller klustret som kör.

Skapa en fil som anger listan över kataloger som ska läggas till på den lokala Hadoop-kluster som du planerar att starta jobbet DistCp.

Här är ett exempel:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur Data Lake Storage Gen2 fungerar med HDInsight-kluster. Se [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
