---
title: Migrera från HDFS-lagring på prem till Azure Storage med Azure Data Box
description: Migrera data från ett lokalt HDFS-arkiv till Azure Storage
author: normesta
ms.service: storage
ms.date: 02/14/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: c0c6a8637223727a9b0c88245d939605f6a8530e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302008"
---
# <a name="migrate-from-on-prem-hdfs-store-to-azure-storage-with-azure-data-box"></a>Migrera från HDFS-lagring på prem till Azure Storage med Azure Data Box

Du kan migrera data från ett lokalt HDFS-arkiv för ditt Hadoop-kluster till Azure Storage (blob storage eller Data Lake Storage Gen2) med hjälp av en Data Box-enhet. Du kan välja mellan en 80 TB Data Box eller en 770 TB Data Box Heavy.

Den här artikeln hjälper dig att utföra dessa uppgifter:

> [!div class="checklist"]
> * Förbered att migrera data.
> * Kopiera dina data till en dataruta eller en databox tung enhet.
> * Skicka tillbaka enheten till Microsoft.
> * Tillämpa åtkomstbehörigheter för filer och kataloger (endast Data Lake Storage Gen2)

## <a name="prerequisites"></a>Krav

Du behöver dessa saker för att slutföra migreringen.

* Ett Azure Storage-konto.

* Ett lokalt Hadoop-kluster som innehåller källdata.

* En [Azure Data Box-enhet](https://azure.microsoft.com/services/storage/databox/).

  * [Beställ din databox](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) eller [databox tung](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). 

  * Kabel och anslut din [Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) eller Data [Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) till ett lokalt nätverk.

Om du är redo, låt oss börja.

## <a name="copy-your-data-to-a-data-box-device"></a>Kopiera dina data till en Data Box-enhet

Om dina data passar in i en enda Data Box-enhet kopierar du data till DataBox-enheten. 

Om datastorleken överskrider databoxenhetens kapacitet använder du den [valfria proceduren för att dela upp data mellan flera Data Box-enheter](#appendix-split-data-across-multiple-data-box-devices) och utför sedan det här steget. 

Om du vill kopiera data från ditt lokala HDFS-arkiv till en Data Box-enhet ställer du in några saker och använder sedan [verktyget DistCp.](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)

Följ dessa steg för att kopiera data via REST-API:erna för Blob/Object storage till databox-enheten. REST API-gränssnittet gör att enheten visas som ett HDFS-arkiv i klustret.

1. Innan du kopierar data via REST, identifiera säkerhet och anslutning primitiver för att ansluta till REST-gränssnittet på Data Box eller Data Box Heavy. Logga in på det lokala webbgränssnittet i Data Box och gå till **Anslut och kopiera** sida. Mot Azure-lagringskontot för din enhet, under **Åtkomstinställningar**, leta upp och välj **REST**.

    ![Sidan "Anslut och kopiera"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. Kopiera slutpunkten för **Blob-tjänsten** och **nyckeln lagringskonto**i dialogrutan Åtkomstlagringskonto och ladda upp data . Utelämna slutpunkten `https://` för blob-tjänsten från blob-tjänsten.

    I det här fallet är `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`slutpunkten: . Den mängd del av URI:n som `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`du ska använda är: . Ett exempel finns i hur du [ansluter till VILA över http](/azure/databox/data-box-deploy-copy-data-via-rest). 

     ![Dialogrutan "Komma åt lagringskonto och ladda upp data"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Lägg till slutpunkten och datarutan eller datarutetunga nod-IP-adressen på `/etc/hosts` varje nod.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    Om du använder någon annan mekanism för DNS bör du se till att slutpunkten för dataruterutan kan matchas.

4. Ställ in `azjars` skalvariabeln `hadoop-azure` på `azure-storage` platsen för jar-filerna och burkfilerna. Du hittar dessa filer under Hadoop-installationskatalogen.

    För att avgöra om dessa filer `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`finns, använd följande kommando: . Ersätt `<hadoop_install_dir>` platshållaren med sökvägen till katalogen där du har installerat Hadoop. Var noga med att använda fullt kvalificerade sökvägar.

    Exempel:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Skapa den lagringsbehållare som du vill använda för datakopiering. Du bör också ange en målkatalog som en del av det här kommandot. Detta kan vara en dummy mål katalog på denna punkt.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Ersätt `<blob_service_endpoint>` platshållaren med namnet på blob-tjänstslutpunkten.

    * Ersätt `<account_key>` platshållaren med åtkomstnyckeln för ditt konto.

    * Ersätt `<container-name>` platshållaren med namnet på behållaren.

    * Ersätt `<destination_directory>` platshållaren med namnet på den katalog som du vill kopiera data till.

6. Kör ett listkommando för att säkerställa att behållaren och katalogen har skapats.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Ersätt `<blob_service_endpoint>` platshållaren med namnet på blob-tjänstslutpunkten.

   * Ersätt `<account_key>` platshållaren med åtkomstnyckeln för ditt konto.

   * Ersätt `<container-name>` platshållaren med namnet på behållaren.

7. Kopiera data från Hadoop HDFS till Data Box Blob-lagring till behållaren som du skapade tidigare. Om katalogen som du kopierar till inte hittas skapas den automatiskt.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Ersätt `<blob_service_endpoint>` platshållaren med namnet på blob-tjänstslutpunkten.

    * Ersätt `<account_key>` platshållaren med åtkomstnyckeln för ditt konto.

    * Ersätt `<container-name>` platshållaren med namnet på behållaren.

    * Ersätt `<exlusion_filelist_file>` platshållaren med namnet på filen som innehåller listan över filuteslutningar.

    * Ersätt `<source_directory>` platshållaren med namnet på katalogen som innehåller de data som du vill kopiera.

    * Ersätt `<destination_directory>` platshållaren med namnet på den katalog som du vill kopiera data till.

    Alternativet `-libjars` används för att `hadoop-azure*.jar` göra `azure-storage*.jar` de och `distcp`de beroende filerna tillgängliga för . Detta kan redan förekomma för vissa kluster.

    I följande exempel `distcp` visas hur kommandot används för att kopiera data.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    Så här förbättrar du kopieringshastigheten:

    * Prova att ändra antalet mappers. (I exemplet `m` ovan används = 4 mappers.)

    * Prova att `distcp` köra flera parallellt.

    * Kom ihåg att stora filer presterar bättre än små filer.

## <a name="ship-the-data-box-to-microsoft"></a>Skicka datarutan till Microsoft

Följ dessa steg för att förbereda och leverera Data Box-enheten till Microsoft.

1. Förbered [först att leverera på din Data Box eller Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).

2. När förberedelsen av enheten är klar hämtar du strukturlistefilerna. Du kommer att använda dessa struktur- eller manifestfiler senare för att verifiera de data som överförs till Azure.

3. Stäng av enheten och ta bort kablarna.

4. Schemalägg en upphämtning med UPS.

    * För Data Box-enheter finns i [Skicka datarutan](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

    * För Data Box Tunga enheter, se [Skicka din Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).

5. När Microsoft tar emot enheten är den ansluten till datacentrets nätverk och data överförs till det lagringskonto som du angav när du gjorde enhetsbeställningen. Kontrollera mot strukturlistefilerna att alla dina data överförs till Azure. 

## <a name="apply-access-permissions-to-files-and-directories-data-lake-storage-gen2-only"></a>Tillämpa åtkomstbehörigheter för filer och kataloger (endast Data Lake Storage Gen2)

Du har redan data i ditt Azure Storage-konto. Nu ska du tillämpa åtkomstbehörigheter för filer och kataloger.

> [!NOTE]
> Det här steget behövs bara om du använder Azure Data Lake Storage Gen2 som datalager. Om du bara använder ett blob-lagringskonto utan hierarkiskt namnområde som datalager kan du hoppa över det här avsnittet.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Skapa ett tjänsthuvudnamn för ditt Azure Data Lake Storage Gen2-konto

Information om hur du skapar ett huvudnamn för tjänsten finns i [Så här: Använd portalen för att skapa ett Azure AD-program och tjänsthuvudnamn som kan komma åt resurser](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

* När du utför stegen i avsnittet [Tilldela programmet till en roll](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) i artikeln ska du tilldela rollen **Storage Blob Data-deltagare** till tjänstens huvudnamn.

* När du utför stegen i avsnittet [Hämta värden för signering i](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) artikeln sparar du program-ID och klienthemliga värden i en textfil. Du kommer att behöva dem snart.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Generera en lista över kopierade filer med deras behörigheter

Kör det här kommandot från det lokala Hadoop-klustret:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Det här kommandot genererar en lista över kopierade filer med deras behörigheter.

> [!NOTE]
> Beroende på antalet filer i HDFS kan det ta lång tid att köra det här kommandot.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Generera en lista över identiteter och mappa dem till Azure Active Directory (ADD) identiteter

1. Ladda `copy-acls.py` ner skriptet. Se [hjälpskripten för hämtningshjälp och konfigurera kantnoden så att de kan köras](#download-helper-scripts) i den här artikeln.

2. Kör det här kommandot för att generera en lista över unika identiteter.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Det här skriptet `id_map.json` genererar en fil med namnet som innehåller de identiteter som du behöver mappa till ADD-baserade identiteter.

3. Öppna `id_map.json` filen i en textredigerare.

4. För varje JSON-objekt som visas `target` i filen uppdaterar du attributet för antingen ett UPN (AAD User Principal Name) eller ObjectId (OID) med lämplig mappad identitet. När du är klar sparar du filen. Du behöver den här filen i nästa steg.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Använda behörigheter för kopierade filer och tillämpa identitetsmappningar

Kör det här kommandot om du vill använda behörigheter för de data som du kopierade till datasjölagringsgenm2-kontot:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Ersätt platshållaren `<storage-account-name>` med namnet på ditt lagringskonto.

* Ersätt `<container-name>` platshållaren med namnet på behållaren.

* Ersätt `<application-id>` platshållarna och `<client-secret>` med program-ID:n och klienthemligheten som du samlade in när du skapade tjänstens huvudnamn.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Tillägg: Dela data mellan flera Data Box-enheter

Innan du flyttar dina data till en Data Box-enhet måste du hämta några hjälpskript, se till att dina data är organiserade så att de får plats på en Data Box-enhet och utesluta onödiga filer.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Hämta hjälpskript och konfigurera edge-noden för att köra dem

1. Kör det här kommandot från kanten eller huvudnoden i ditt lokala Hadoop-kluster:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Det här kommandot klonar GitHub-databasen som innehåller hjälpskripten.

2. Kontrollera att [jq-paketet](https://stedolan.github.io/jq/) är installerat på den lokala datorn.

   ```bash
   
   sudo apt-get install jq
   ```

3. Installera [python-paketet Begär.](https://2.python-requests.org/en/master/)

   ```bash
   
   pip install requests
   ```

4. Ange körningsbehörigheter för de skript som krävs.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Se till att dina data är organiserade så att de får plats på en Data Box-enhet

Om storleken på dina data överstiger storleken på en enskild Data Box-enhet kan du dela upp filer i grupper som du kan lagra på flera Data Box-enheter.

Om dina data inte överskrider storleken på en singe Data Box-enhet kan du gå vidare till nästa avsnitt.

1. Med förhöjda behörigheter `generate-file-list` kör du skriptet som du hämtade genom att följa vägledningen i föregående avsnitt.

   Här är en beskrivning av kommandoparametrarna:

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

2. Kopiera de genererade fillistorna till HDFS så att de är tillgängliga för [DistCp-jobbet.](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Utesluta onödiga filer

Du måste utesluta vissa kataloger från DisCp-jobbet. Uteslut till exempel kataloger som innehåller tillståndsinformation som håller klustret igång.

Skapa en fil som anger listan över kataloger som du vill utesluta i det lokala Hadoop-klustret där du planerar att initiera DistCp-jobbet.

Här är ett exempel:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur Data Lake Storage Gen2 fungerar med HDInsight-kluster. Se [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
