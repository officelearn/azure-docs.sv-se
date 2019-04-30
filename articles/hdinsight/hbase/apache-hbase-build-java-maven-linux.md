---
title: Använda Apache Maven för att skapa en Java-HBase-klient för Azure HDInsight
description: Lär dig använda Apache Maven för att skapa ett Java-baserade Apache HBase-program och sedan distribuera den till HBase på Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seodec18
ms.topic: conceptual
ms.date: 04/16/2019
ms.openlocfilehash: a4c601e81390efa3bb53a6f07225bb6e939bc9bb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114418"
---
# <a name="build-java-applications-for-apache-hbase"></a>Skapa Java-program för Apache HBase

Lär dig hur du skapar en [Apache HBase](https://hbase.apache.org/) program i Java. Använd sedan programmet med HBase i Azure HDInsight.

Stegen i det här dokumentet används [Apache Maven](https://maven.apache.org/) att skapa och bygga projektet. Maven är en programvara för projekthantering och förståelsen verktyg som hjälper dig att skapa programvara, dokumentation och rapporter för Java-projekt.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Ett Apache HBase-kluster på HDInsight. Se [Kom igång med Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

* [Java Developer Kit (JDK) version 8](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) korrekt [installerat](https://maven.apache.org/install.html) enligt Apache.  Maven är ett projekt buildsystemet för Java-projekt.

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Om du använder PowerShell, måste den [AZ modulen](https://docs.microsoft.com/powershell/azure/overview).

* En textredigerare. Den här artikeln använder Microsoft Notepad.

> [!IMPORTANT]  
> Azure PowerShell-cmdlets [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) och [Get-AzHDInsightJobOutput](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjoboutput) för närvarande fungerar inte när [säker överföring](../../storage/common/storage-require-secure-transfer.md) är aktiverat för lagringskontot .

## <a name="test-environment"></a>Testmiljö
Den miljö som ska användas för den här artikeln har en dator som kör Windows 10.  Kommandona utfördes i en kommandotolk och de olika filerna har redigerats med anteckningar. Ändra för din miljö.

Ange kommandona nedan för att skapa en arbetsmiljö från en kommandotolk:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Skapa ett Maven-projekt

1. Ange följande kommando för att skapa ett Maven-projekt med namnet **hbaseapp**:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    cd hbaseapp
    mkdir conf
    ```

    Det här kommandot skapar en katalog med namnet `hbaseapp` på den aktuella platsen som innehåller ett grundläggande Maven-projekt. Det andra kommandot ändrar arbetskatalogen till `hbaseapp`. Tredje kommandot skapar en ny katalog `conf`, som kommer att användas senare. Den `hbaseapp` katalogen innehåller följande objekt:

    * `pom.xml`:  Projektet Object Model ([POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) innehåller information om och konfiguration information som används för att skapa projektet.
    * `src\main\java\com\microsoft\examples`: Innehåller din programkod.
    * `src\test\java\com\microsoft\examples`: Innehåller tester för ditt program.

2. Ta bort genererad exempelkoden. Ta bort genererad test- och programfiler `AppTest.java`, och `App.java` genom att ange nedanstående kommandon:

    ```cmd
    DEL src\main\java\com\microsoft\examples\App.java
    DEL src\test\java\com\microsoft\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Uppdatera projekt-objektmodell

Fullständiga referenser i pom.xml-filen finns https://maven.apache.org/pom.html.  Öppna `pom.xml` genom att ange kommandot nedan:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Lägga till beroenden

I `pom.xml`, Lägg till följande text i den `<dependencies>` avsnittet:

```xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-client</artifactId>
    <version>1.1.2</version>
</dependency>
<dependency>
    <groupId>org.apache.phoenix</groupId>
    <artifactId>phoenix-core</artifactId>
    <version>4.14.1-HBase-1.1</version>
</dependency>
```  

Det här avsnittet anger att projektet måste **hbase-client** och **phoenix kärnor** komponenter. Vid kompilering laddas dessa beroenden från Maven-centrallagret standard. Du kan använda den [Maven-sökning för centrala lagringsplatsen](https://search.maven.org/artifact/org.apache.hbase/hbase-client/1.1.2/jar) mer information om det här beroendet.

> [!IMPORTANT]  
> Versionsnumret för hbase-klienten måste matcha versionen av Apache HBase som medföljer HDInsight-kluster. Använd följande tabell för att hitta rätt versionsnumret.

| HDInsight-kluster av version | Apache HBase-version som ska användas |
| --- | --- |
| 3.6 | 1.1.2 |
| 4.0 | 2.0.0 |

Läs mer om HDInsight-versioner och komponenter, [vilka är de olika Apache Hadoop-komponenterna med HDInsight](../hdinsight-component-versioning.md).

### <a name="build-configuration"></a>Skapa konfiguration

Maven-plugin-program kan du anpassa build-faser av projektet. Det här avsnittet används för att lägga till plugin-program, resurser och andra build-konfigurationsalternativ.

Lägg till följande kod till den `pom.xml` fil, och spara och stäng filen. Den här texten måste ingå i den `<project>...</project>` taggar i filen, till exempel mellan `</dependencies>` och `</project>`.

```xml
<build>
    <sourceDirectory>src</sourceDirectory>
    <resources>
    <resource>
        <directory>${basedir}/conf</directory>
        <filtering>false</filtering>
        <includes>
        <include>hbase-site.xml</include>
        </includes>
    </resource>
    </resources>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.0</version>
        <configuration>
            <source>1.8</source>
            <target>1.8</target>
        </configuration>
        </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
        </configuration>
        <executions>
        <execution>
            <phase>package</phase>
            <goals>
            <goal>shade</goal>
            </goals>
        </execution>
        </executions>
    </plugin>
    </plugins>
</build>
```

Det här avsnittet konfigurerar en resurs (`conf/hbase-site.xml`) som innehåller konfigurationsinformation för HBase.

> [!NOTE]  
> Du kan också ange konfigurationsvärden via kod. Visa kommentarerna i den `CreateTable` exempel.

Det här avsnittet konfigurerar också den [plugin-programmet för Apache Maven-kompilatorn](https://maven.apache.org/plugins/maven-compiler-plugin/) och [Apache Maven nyans plugin-programmet](https://maven.apache.org/plugins/maven-shade-plugin/). Kompilatorn plugin-programmet används för att kompilera topologin. Nyans plugin-programmet används för att förhindra dubbletter av licens JAR-paket som skapats med Maven. Det här plugin-programmet används för att förhindra att ett ”duplicerade licensfiler” fel vid körning i HDInsight-klustret. Med hjälp av maven-nyans-plugin-programmet med den `ApacheLicenseResourceTransformer` implementering förhindrar felet.

Maven-nyans-plugin-programmet ger också en uber JAR-filen som innehåller alla beroenden som krävs av programmet.

### <a name="download-the-hbase-sitexml"></a>Ladda ned hbase-site.xml

Använd följande kommando för att kopiera HBase-konfigurationen från HBase-kluster till den `conf` directory. Ersätt `CLUSTERNAME` med ditt HDInsight-klusternamn och ange sedan kommandot:

```cmd
scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
```

## <a name="create-the-application"></a>Skapa programmet

### <a name="implement-a-createtable-class"></a>Implementera en CreateTable-klass

Ange kommandot nedan för att skapa och öppna en ny fil `CreateTable.java`. Välj **Ja** på meddelandet för att skapa en ny fil.

```cmd
notepad src\main\java\com\microsoft\examples\CreateTable.java
```

Kopiera och klistra in java-koden nedan i den nya filen. Stäng filen.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;
import org.apache.hadoop.hbase.HTableDescriptor;
import org.apache.hadoop.hbase.TableName;
import org.apache.hadoop.hbase.HColumnDescriptor;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.util.Bytes;

public class CreateTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Example of setting zookeeper values for HDInsight
    // in code instead of an hbase-site.xml file
    //
    // config.set("hbase.zookeeper.quorum",
    //            "zookeepernode0,zookeepernode1,zookeepernode2");
    //config.set("hbase.zookeeper.property.clientPort", "2181");
    //config.set("hbase.cluster.distributed", "true");
    //
    //NOTE: Actual zookeeper host names can be found using Ambari:
    //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"

    //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
    config.set("zookeeper.znode.parent","/hbase-unsecure");

    // create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // create the table...
    HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
    // ... with two column families
    tableDescriptor.addFamily(new HColumnDescriptor("name"));
    tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
    admin.createTable(tableDescriptor);

    // define some people
    String[][] people = {
        { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
        { "2", "Franklin", "Holtz", "franklin@contoso.com" },
        { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
        { "4", "Rae", "Schroeder", "rae@contoso.com" },
        { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
        { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

    HTable table = new HTable(config, "people");

    // Add each person to the table
    //   Use the `name` column family for the name
    //   Use the `contactinfo` column family for the email
    for (int i = 0; i< people.length; i++) {
        Put person = new Put(Bytes.toBytes(people[i][0]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
        person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
        table.put(person);
    }
    // flush commits and close the table
    table.flushCommits();
    table.close();
    }
}
```

Den här koden är den `CreateTable` klass, vilket skapar en tabell med namnet `people` och fyller den med vissa fördefinierade användare.

### <a name="implement-a-searchbyemail-class"></a>Implementera en SearchByEmail-klass

Ange kommandot nedan för att skapa och öppna en ny fil `SearchByEmail.java`. Välj **Ja** på meddelandet för att skapa en ny fil.

```cmd
notepad src\main\java\com\microsoft\examples\SearchByEmail.java
```

Kopiera och klistra in java-koden nedan i den nya filen. Stäng filen.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.client.ResultScanner;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.filter.RegexStringComparator;
import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
import org.apache.hadoop.hbase.util.Bytes;
import org.apache.hadoop.util.GenericOptionsParser;

public class SearchByEmail {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Use GenericOptionsParser to get only the parameters to the class
    // and not all the parameters passed (when using WebHCat for example)
    String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
    if (otherArgs.length != 1) {
        System.out.println("usage: [regular expression]");
        System.exit(-1);
    }

    // Open the table
    HTable table = new HTable(config, "people");

    // Define the family and qualifiers to be used
    byte[] contactFamily = Bytes.toBytes("contactinfo");
    byte[] emailQualifier = Bytes.toBytes("email");
    byte[] nameFamily = Bytes.toBytes("name");
    byte[] firstNameQualifier = Bytes.toBytes("first");
    byte[] lastNameQualifier = Bytes.toBytes("last");

    // Create a regex filter
    RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
    // Attach the regex filter to a filter
    //   for the email column
    SingleColumnValueFilter filter = new SingleColumnValueFilter(
        contactFamily,
        emailQualifier,
        CompareOp.EQUAL,
        emailFilter
    );

    // Create a scan and set the filter
    Scan scan = new Scan();
    scan.setFilter(filter);

    // Get the results
    ResultScanner results = table.getScanner(scan);
    // Iterate over results and print  values
    for (Result result : results ) {
        String id = new String(result.getRow());
        byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
        String firstName = new String(firstNameObj);
        byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
        String lastName = new String(lastNameObj);
        System.out.println(firstName + " " + lastName + " - ID: " + id);
        byte[] emailObj = result.getValue(contactFamily, emailQualifier);
        String email = new String(emailObj);
        System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
    }
    results.close();
    table.close();
    }
}
```

Den `SearchByEmail` klassen kan användas för att fråga efter rader genom e-postadress. Eftersom den använder ett reguljärt uttryck filter kan du ange en sträng eller ett reguljärt uttryck när du använder klassen.

### <a name="implement-a-deletetable-class"></a>Implementera en DeleteTable-klass

Ange kommandot nedan för att skapa och öppna en ny fil `DeleteTable.java`. Välj **Ja** på meddelandet för att skapa en ny fil.

```cmd
notepad src\main\java\com\microsoft\examples\DeleteTable.java
```

Kopiera och klistra in java-koden nedan i den nya filen. Stäng filen.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;

public class DeleteTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // Disable, and then delete the table
    admin.disableTable("people");
    admin.deleteTable("people");
    }
}
```

Den `DeleteTable` klass rensar HBase-tabeller som skapats i det här exemplet genom att inaktivera och släppa tabellen som skapats av den `CreateTable` klass.

## <a name="build-and-package-the-application"></a>Bygga och paketera programmet

1. Från den `hbaseapp` directory, Använd följande kommando för att skapa ett JAR-filen som innehåller programmet:

    ```cmd
    mvn clean package
    ```

    Det här kommandot skapar och paketerar programmet till en .jar-fil.

2. När kommandot har slutförts, den `hbaseapp/target` katalogen innehåller en fil med namnet `hbaseapp-1.0-SNAPSHOT.jar`.

   > [!NOTE]  
   > Den `hbaseapp-1.0-SNAPSHOT.jar` filen är en uber JAR-filen. Den innehåller alla beroenden som krävs för att köra programmet.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Ladda upp JAR och köra jobb (SSH)

I följande anvisningar används `scp` att kopiera den JAR-filen till den primära huvudnoden av din Apache HBase på HDInsight-kluster. Den `ssh` sedan används för att ansluta till klustret och köra exemplet direkt på huvudnoden.

1. Ladda upp jar till klustret. Ersätt `CLUSTERNAME` med ditt HDInsight-klustret namn och ange sedan följande kommando:

    ```cmd
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

2. Ansluta till HBase-kluster. Ersätt `CLUSTERNAME` med ditt HDInsight-klustret namn och ange sedan följande kommando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

 3. Om du vill skapa en HBase-tabell med hjälp av Java-program, använder du följande kommando i dina öppna ssh anslutning:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    Det här kommandot skapar en HBase-tabell med namnet **personer**, som fylls i med data.

4. För att söka efter e-postadresser som lagras i tabellen, använder du följande kommando:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    Du får följande resultat:

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

5. Om du vill ta bort tabellen använder du följande kommando:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>Ladda upp JAR och köra jobb (PowerShell)

Följande steg använder du Azure PowerShell [AZ modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) att ladda upp JAR till standardlagring för Apache HBase-kluster. HDInsight-cmdlet: ar används sedan för att köra exemplen via fjärranslutning.

1. När du installerar och konfigurerar AZ-modulen kan du skapa en fil med namnet `hbase-runner.psm1`. Använd följande text som filens innehåll:

   ```powershell
    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.CreateTable"
    -clusterName "MyHDInsightCluster"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "contoso.com"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "^r" -showErr
    #>

    function Start-HBaseExample {
    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
    #The class to run
    [Parameter(Mandatory = $true)]
    [String]$className,

    #The name of the HDInsight cluster
    [Parameter(Mandatory = $true)]
    [String]$clusterName,

    #Only used when using SearchByEmail
    [Parameter(Mandatory = $false)]
    [String]$emailRegex,

    #Use if you want to see stderr output
    [Parameter(Mandatory = $false)]
    [Switch]$showErr
    )

    Set-StrictMode -Version 3

    # Is the Azure module installed?
    FindAzure

    # Get the login for the HDInsight cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # The JAR
    $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

    # The job definition
    $jobDefinition = New-AzHDInsightMapReduceJobDefinition `
        -JarFile $jarFile `
        -ClassName $className `
        -Arguments $emailRegex

    # Get the job output
    $job = Start-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
    if($showErr)
    {
    Write-Host "STDERR"
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds `
                -DisplayOutputType StandardError
    }
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds
    }

    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    -Container "MyContainer"
    #>

    function Add-HDInsightFile {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
            #The path to the local file.
            [Parameter(Mandatory = $true)]
            [String]$localPath,

            #The destination path and file name, relative to the root of the container.
            [Parameter(Mandatory = $true)]
            [String]$destinationPath,

            #The name of the HDInsight cluster
            [Parameter(Mandatory = $true)]
            [String]$clusterName,

            #If specified, overwrites existing files without prompting
            [Parameter(Mandatory = $false)]
            [Switch]$force
        )

        Set-StrictMode -Version 3

        # Is the Azure module installed?
        FindAzure

        # Get authentication for the cluster
        $creds=Get-Credential

        # Does the local path exist?
        if (-not (Test-Path $localPath))
        {
            throw "Source path '$localPath' does not exist."
        }

        # Get the primary storage container
        $storage = GetStorage -clusterName $clusterName

        # Upload file to storage, overwriting existing files if -force was used.
        Set-AzStorageBlobContent -File $localPath `
            -Blob $destinationPath `
            -force:$force `
            -Container $storage.container `
            -Context $storage.context
    }

    function FindAzure {
        # Is there an active Azure subscription?
        $sub = Get-AzSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Connect-AzAccount
        }
    }

    function GetStorage {
        param(
            [Parameter(Mandatory = $true)]
            [String]$clusterName
        )
        $hdi = Get-AzHDInsightCluster -ClusterName $clusterName
        # Does the cluster exist?
        if (!$hdi)
        {
            throw "HDInsight cluster '$clusterName' does not exist."
        }
        # Create a return object for context & container
        $return = @{}
        $storageAccounts = @{}

        # Get storage information
        $resourceGroup = $hdi.ResourceGroup
        $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
        $container=$hdi.DefaultStorageContainer
        $storageAccountKey=(Get-AzStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Get the resource group, in case we need that
        $return.resourceGroup = $resourceGroup
        # Get the storage context, as we can't depend
        # on using the default storage context
        $return.context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        # Get the container, so we know where to
        # find/store blobs
        $return.container = $container
        # Return storage accounts to support finding all accounts for
        # a cluster
        $return.storageAccount = $storageAccountName
        $return.storageAccountKey = $storageAccountKey

        return $return
    }
    # Only export the verb-phrase things
    export-modulemember *-*
   ```

    Den här filen innehåller två moduler:

   * **Lägg till HDInsightFile** – används för att ladda upp filer till klustret
   * **Start-HBaseExample** – används för att köra de klasser som skapades tidigare

2. Spara den `hbase-runner.psm1` fil i den `hbaseapp` directory.

3. Registrera moduler med Azure PowerShell. Öppna ett nytt Azure PowerShell-fönster och redigera kommandot nedan genom att ersätta `CLUSTERNAME` med namnet på klustret. Ange sedan följande kommandon:

    ```powershell
    cd C:\HDI\hbaseapp
    $myCluster = "CLUSTERNAME"
    Import-Module .\hbase-runner.psm1
    ```

4. Använd följande kommando för att ladda upp den `hbaseapp-1.0-SNAPSHOT.jar` till ditt kluster.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName $myCluster
    ```

    När du uppmanas, anger du klustrets inloggningsnamn (admin) och lösenord. Kommandot laddar upp den `hbaseapp-1.0-SNAPSHOT.jar` till den `example/jars` plats i den primära lagringen för klustret.

5. Skapa en tabell med de `hbaseapp`, använder du följande kommando:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName $myCluster
    ```

    När du uppmanas, anger du klustrets inloggningsnamn (admin) och lösenord.

    Det här kommandot skapar en tabell med namnet **personer** i HBase på HDInsight-klustret. Det här kommandot visar inte några utdata i konsolfönstret.

6. Om du vill söka efter poster i tabellen, använder du följande kommando:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName $myCluster -emailRegex contoso.com
    ```

    När du uppmanas, anger du klustrets inloggningsnamn (admin) och lösenord.

    Detta kommando använder de `SearchByEmail` klassen för att söka efter alla rader där den `contactinformation` kolumnserie och `email` kolumnen innehåller strängen `contoso.com`. Du bör få följande resultat:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Med hjälp av **fabrikam.com** för den `-emailRegex` värde returnerar de användare som har **fabrikam.com** i postfältet e-. Du kan också använda reguljära uttryck söktermen. Till exempel **^ r** Returnerar e-postadresser som börjar med bokstaven ”r”.

7. Om du vill ta bort tabellen använder du följande kommando:

    ```PowerShell
    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName $myCluster
    ```

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Inga resultat eller oväntade resultat när du använder Start-HBaseExample

Använd den `-showErr` parameter för att visa standardfel (STDERR) som skapas när du kör jobbet.

## <a name="next-steps"></a>Nästa steg

[Lär dig hur du använder SQuirreL SQL med Apache HBase](apache-hbase-phoenix-squirrel-linux.md)
