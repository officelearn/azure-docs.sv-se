---
title: Använd Apache Maven för att skapa en Java HBase-klient för Azure HDInsight
description: Lär dig hur du använder Apache Maven för att skapa ett Java-baserat Apache HBase-program och distribuera det sedan till HBase på Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seodec18
ms.date: 12/24/2019
ms.openlocfilehash: 3e9b23ce450e45dfedcee8b20e09b1c2b52b6e68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495795"
---
# <a name="build-java-applications-for-apache-hbase"></a>Skapa Java-program för Apache HBase

Lär dig hur du skapar ett [Apache HBase-program](https://hbase.apache.org/) i Java. Använd sedan programmet med HBase på Azure HDInsight.

Stegen i det här dokumentet använder [Apache Maven](https://maven.apache.org/) för att skapa och bygga projektet. Maven är en programvara projektledning och förståelse verktyg som låter dig bygga programvara, dokumentation och rapporter för Java-projekt.

## <a name="prerequisites"></a>Krav

* Ett Apache HBase-kluster på HDInsight. Se [Kom igång med Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

* [Java Developer Kit (JDK) version 8](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) korrekt [installerad](https://maven.apache.org/install.html) enligt Apache.  Maven är ett projektbyggsystem för Java-projekt.

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Om du använder PowerShell behöver du [AZ-modulen](https://docs.microsoft.com/powershell/azure/overview).

* En textredigerare. I den här artikeln används Microsoft Notepad.

## <a name="test-environment"></a>Testmiljö

Miljön som användes för den här artikeln var en dator som kör Windows 10.  Kommandona utfördes i en kommandotolk och de olika filerna redigerades med Anteckningar. Ändra i enlighet med detta för din miljö.

Från en kommandotolk anger du kommandona nedan för att skapa en arbetsmiljö:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Skapa ett Maven-projekt

1. Ange följande kommando för att skapa ett Maven-projekt med namnet **hbaseapp:**

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    cd hbaseapp
    mkdir conf
    ```

    Det här kommandot skapar `hbaseapp` en katalog som namnges på den aktuella platsen, som innehåller ett grundläggande Maven-projekt. Det andra kommandot ändrar `hbaseapp`arbetskatalogen till . Det tredje kommandot skapar en `conf`ny katalog, som kommer att användas senare. Katalogen `hbaseapp` innehåller följande objekt:

    * `pom.xml`: Projektobjektmodellen[(POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)innehåller information och konfigurationsinformation som används för att bygga projektet.
    * `src\main\java\com\microsoft\examples`: Innehåller din programkod.
    * `src\test\java\com\microsoft\examples`: Innehåller tester för ditt program.

2. Ta bort den genererade exempelkoden. Ta bort de genererade `AppTest.java`test- och programfilerna och `App.java` genom att ange kommandona nedan:

    ```cmd
    DEL src\main\java\com\microsoft\examples\App.java
    DEL src\test\java\com\microsoft\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Uppdatera projektobjektmodellen

En fullständig referens för filen pom.xml finns i https://maven.apache.org/pom.html.  Öppna `pom.xml` genom att ange kommandot nedan:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Lägga till beroenden

Lägg `pom.xml`till följande text `<dependencies>` i avsnittet i:

```xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-shaded-client</artifactId>
    <version>1.1.2</version>
</dependency>
<dependency>
    <groupId>org.apache.phoenix</groupId>
    <artifactId>phoenix-core</artifactId>
    <version>4.14.1-HBase-1.1</version>
</dependency>
```  

Det här avsnittet anger att projektet behöver **hbase-client-** och **phoenix-core-komponenter.** Vid kompilering hämtas dessa beroenden från standarddatabasen Maven. Du kan använda [Maven Central Repository Search](https://search.maven.org/artifact/org.apache.hbase/hbase-client/1.1.2/jar) för att lära dig mer om det här beroendet.

> [!IMPORTANT]  
> Versionsnumret för hbase-klienten måste matcha den version av Apache HBase som medföljer ditt HDInsight-kluster. Använd följande tabell för att hitta rätt versionsnummer.

| HDInsight-klusterversion | Apache HBase-version att använda |
| --- | --- |
| 3.6 | 1.1.2 |
| 4.0 | 2.0.0 |

Mer information om HDInsight-versioner och komponenter finns i [Vilka är de olika Apache Hadoop-komponenterna som finns med HDInsight](../hdinsight-component-versioning.md).

### <a name="build-configuration"></a>Skapa konfiguration

Maven plug-ins kan du anpassa bygga stadier av projektet. Det här avsnittet används för att lägga till plugin-program, resurser och andra konfigurationsalternativ för versioner.

Lägg till följande `pom.xml` kod i filen och spara och stäng filen. Den här texten `<project>...</project>` måste finnas i taggarna `</dependencies>` i `</project>`filen, till exempel mellan och .

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
                <version>3.8.1</version>
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

I det här avsnittet`conf/hbase-site.xml`konfigureras en resurs ( ) som innehåller konfigurationsinformation för HBase.

> [!NOTE]  
> Du kan också ange konfigurationsvärden via kod. Se kommentarerna i `CreateTable` exemplet.

Detta avsnitt konfigurerar också [Apache Maven Kompilator Plugin](https://maven.apache.org/plugins/maven-compiler-plugin/) och [Apache Maven Shade Plugin](https://maven.apache.org/plugins/maven-shade-plugin/). Plugin-programmet kompilator används för att kompilera topologin. Skärmplug-in används för att förhindra licensdubbning i JAR-paketet som är byggt av Maven. Denna plugin används för att förhindra en "dubblett licensfiler" fel vid körning på HDInsight klustret. Använda maven-skugga-plugin `ApacheLicenseResourceTransformer` med genomförandet förhindrar felet.

Den maven-skugga-plugin producerar också en uber burk som innehåller alla beroenden som krävs av programmet.

### <a name="download-the-hbase-sitexml"></a>Ladda ner hbase-site.xml

Använd följande kommando för att kopiera HBase-konfigurationen `conf` från HBase-klustret till katalogen. Ersätt `CLUSTERNAME` med ditt HDInsight-klusternamn och ange sedan kommandot:

```cmd
scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
```

## <a name="create-the-application"></a>Skapa programmet

### <a name="implement-a-createtable-class"></a>Implementera en CreateTable-klass

Ange kommandot nedan för att skapa `CreateTable.java`och öppna en ny fil . Välj **Ja** vid uppmaningen om du vill skapa en ny fil.

```cmd
notepad src\main\java\com\microsoft\examples\CreateTable.java
```

Kopiera och klistra in java-koden nedan i den nya filen. Stäng sedan filen.

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

Den här `CreateTable` koden är klassen, som `people` skapar en tabell med namnet och fyller i den med vissa fördefinierade användare.

### <a name="implement-a-searchbyemail-class"></a>Implementera en SearchByEmail-klass

Ange kommandot nedan för att skapa `SearchByEmail.java`och öppna en ny fil . Välj **Ja** vid uppmaningen om du vill skapa en ny fil.

```cmd
notepad src\main\java\com\microsoft\examples\SearchByEmail.java
```

Kopiera och klistra in java-koden nedan i den nya filen. Stäng sedan filen.

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

Klassen `SearchByEmail` kan användas för att fråga efter rader via e-postadress. Eftersom ett filter med reguljära uttryck används kan du ange antingen en sträng eller ett reguljärt uttryck när du använder klassen.

### <a name="implement-a-deletetable-class"></a>Implementera en klass deletetable

Ange kommandot nedan för att skapa `DeleteTable.java`och öppna en ny fil . Välj **Ja** vid uppmaningen om du vill skapa en ny fil.

```cmd
notepad src\main\java\com\microsoft\examples\DeleteTable.java
```

Kopiera och klistra in java-koden nedan i den nya filen. Stäng sedan filen.

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

Klassen `DeleteTable` rensar hbase-tabeller som skapats i det här exemplet `CreateTable` genom att inaktivera och släppa tabellen som skapats av klassen.

## <a name="build-and-package-the-application"></a>Skapa och paketera programmet

1. Från `hbaseapp` katalogen använder du följande kommando för att skapa en JAR-fil som innehåller programmet:

    ```cmd
    mvn clean package
    ```

    Det här kommandot skapar och paketerar programmet till en JAR-fil.

2. När kommandot är klart `hbaseapp/target` innehåller katalogen `hbaseapp-1.0-SNAPSHOT.jar`en fil med namnet .

   > [!NOTE]  
   > Filen `hbaseapp-1.0-SNAPSHOT.jar` är en uber burk. Den innehåller alla beroenden som krävs för att köra programmet.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Ladda upp JAR och kör jobb (SSH)

Följande steg `scp` används för att kopiera JAR till den primära huvudnoden för apache HBase i HDInsight-klustret. Kommandot `ssh` används sedan för att ansluta till klustret och köra exemplet direkt på huvudnoden.

1. Ladda upp burken till klustret. Ersätt `CLUSTERNAME` med ditt HDInsight-klusternamn och ange sedan följande kommando:

    ```cmd
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

2. Anslut till HBase-klustret. Ersätt `CLUSTERNAME` med ditt HDInsight-klusternamn och ange sedan följande kommando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Om du vill skapa en HBase-tabell med Java-programmet använder du följande kommando i din öppna ssh-anslutning:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    Det här kommandot skapar en HBase-tabell med namnet **personer**och fyller den med data.

4. Om du vill söka efter e-postadresser som lagras i tabellen använder du följande kommando:

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

## <a name="upload-the-jar-and-run-jobs-powershell"></a>Ladda upp JAR- och kör-jobben (PowerShell)

I följande steg används Azure PowerShell [AZ-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) för att överföra JAR till standardlagringen för Apache HBase-klustret. HDInsight-cmdlets används sedan för att köra exemplen på distans.

1. När du har installerat och konfigurerat AZ-modulen skapar du en fil med namnet `hbase-runner.psm1`. Använd följande text som filens innehåll:

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

   * **Add-HDInsightFile** - används för att ladda upp filer till klustret
   * **Start-HBaseExample** - används för att köra de klasser som skapats tidigare

2. Spara `hbase-runner.psm1` filen i `hbaseapp` katalogen.

3. Registrera modulerna med Azure PowerShell. Öppna ett nytt Azure PowerShell-fönster och `CLUSTERNAME` redigera kommandot nedan genom att ersätta med namnet på klustret. Ange sedan följande kommandon:

    ```powershell
    cd C:\HDI\hbaseapp
    $myCluster = "CLUSTERNAME"
    Import-Module .\hbase-runner.psm1
    ```

4. Använd följande kommando för `hbaseapp-1.0-SNAPSHOT.jar` att ladda upp till klustret.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName $myCluster
    ```

    Ange namnet och lösenordet för klusterinloggning (admin) när du uppmanas att göra det. Kommandot överför `hbaseapp-1.0-SNAPSHOT.jar` `example/jars` platsen i det primära lagringsutrymmet för klustret.

5. Så här skapar `hbaseapp`du en tabell med kommandot :

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName $myCluster
    ```

    Ange namnet och lösenordet för klusterinloggning (admin) när du uppmanas att göra det.

    Det här kommandot skapar en tabell med namnet **personer** i HBase i ditt HDInsight-kluster. Det här kommandot visar inga utdata i konsolfönstret.

6. Om du vill söka efter poster i tabellen använder du följande kommando:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName $myCluster -emailRegex contoso.com
    ```

    Ange namnet och lösenordet för klusterinloggning (admin) när du uppmanas att göra det.

    Det här `SearchByEmail` kommandot använder klassen för att `contactinformation` söka efter `email` alla rader där `contoso.com`kolumnfamiljen och kolumnen innehåller strängen . Du bör få följande resultat:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Om **du** använder fabrikam.com `-emailRegex` för värdet returneras de användare som har **fabrikam.com** i e-postfältet. Du kan också använda reguljära uttryck som sökterm. **^r** returnerar till exempel e-postadresser som börjar med bokstaven 'r'.

7. Om du vill ta bort tabellen använder du följande kommando:

    ```PowerShell
    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName $myCluster
    ```

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Inga resultat eller oväntade resultat när du använder Start-HBaseExample

Använd `-showErr` parametern för att visa standardfelet (STDERR) som produceras när jobbet körs.

## <a name="next-steps"></a>Nästa steg

[Lär dig hur du använder SQLLine med Apache HBase](apache-hbase-query-with-phoenix.md)
