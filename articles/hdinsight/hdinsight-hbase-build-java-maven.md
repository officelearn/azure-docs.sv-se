---
title: Skapa ett Java-HBase-program för Windows-baserade Azure HDInsight
description: Lär dig hur du använder Apache Maven för att skapa ett Java-baserade Apache HBase-program och sedan distribuera det till ett Windows-baserade Azure HDInsight-kluster.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: fc2bd03156d6005f1e10252583b4a13451f66dbf
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43092635"
---
# <a name="use-maven-to-build-java-applications-that-use-hbase-with-windows-based-hdinsight-hadoop"></a>Använd Maven för att skapa Java-program som använder HBase med Windows-baserade HDInsight (Hadoop)
Lär dig hur du skapar och bygger en [Apache HBase](http://hbase.apache.org/) program i Java med Apache Maven. Använd sedan programmet med Azure HDInsight (Hadoop).

[Maven](http://maven.apache.org/) är ett projekt hanterings- och förståelsen programverktyg som hjälper dig att skapa programvara, dokumentation och rapporter för Java-projekt. I den här artikeln får du lära dig hur du använder den för att skapa en grundläggande Java-program som skapar, frågor, och tar bort en HBase-tabell i ett Azure HDInsight-kluster.

> [!IMPORTANT]
> Stegen i det här dokumentet kräver ett HDInsight-kluster som använder Windows. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="requirements"></a>Krav
* [Java-plattformen JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 eller senare
* [Maven 3.](http://maven.apache.org/)
* Ett Windows-baserade HDInsight-kluster med HBase

    > [!NOTE]
    > Stegen i det här dokumentet har testats med HDInsight-klusterversioner 3.2 och 3.3. Standardvärdena i exemplen är i ett kluster med HDInsight 3.3.

## <a name="create-the-project"></a>Skapa projektet
1. Från kommandoraden i din utvecklingsmiljö ändrar du katalog till den plats där du vill skapa projektet, till exempel `cd code\hdinsight`.
2. Använd den **mvn** kommando, som installeras med Maven för att generera ställningarna för projektet.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Det här kommandot skapar en katalog i den aktuella platsen med namnet som angetts av den **artifactID** parametern (**hbaseapp** i det här exemplet.) Den här katalogen innehåller följande objekt:

   * **pom.XML**: The Project Object Model ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) innehåller information om och konfiguration information som används för att skapa projektet.
   * **SRC**: den katalog som innehåller den **main\java\com\microsoft\examples** directory, där du ska redigera programmet.
3. Ta bort den **src\test\java\com\microsoft\examples\apptest.java** filen eftersom den inte används i det här exemplet.

## <a name="update-the-project-object-model"></a>Uppdatera projekt-objektmodell
1. Redigera den **pom.xml** filen och Lägg till följande kod i den `<dependencies>` avsnittet:

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    Det här avsnittet visar Maven att projektet kräver **hbase-client** version **1.1.2**. Vid kompilering hämtas det här beroendet från Maven-centrallagret standard. Du kan använda den [Maven-sökning för centrala lagringsplatsen](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) mer information om det här beroendet.

   > [!IMPORTANT]
   > Versionsnumret måste matcha versionen av HBase som medföljer HDInsight-kluster. Använd följande tabell för att hitta rätt versionsnumret.
   >
   >

   | HDInsight-kluster av version | HBase-version som ska användas |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3 |1.1.2 |

    Läs mer om HDInsight-versioner och komponenter, [vilka är de olika Hadoop-komponenterna med HDInsight](hdinsight-component-versioning.md).
2. Om du använder ett 3.3 för HDInsight-kluster, måste du också lägga till följande för att den `<dependencies>` avsnittet:

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>

    Det här beroendet laddas phoenix-kärnkomponenter, som används med Hbase version 1.1.x.
3. Lägg till följande kod till den **pom.xml** fil. Det här avsnittet måste ingå i den `<project>...</project>` taggar i filen, till exempel mellan `</dependencies>` och `</project>`.

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
                <version>3.3</version>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                </configuration>
              </plugin>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.3</version>
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

    Den `<resources>` avsnittet konfigurerar en resurs (**conf\hbase site.xml**) som innehåller konfigurationsinformation för HBase.

   > [!NOTE]
   > Du kan också ange konfigurationsvärden via kod. Visa kommentarerna i den **CreateTable** exemplet som följer att göra detta.
   >
   >

    Detta `<plugins>` avsnittet konfigureras den [Maven-kompilatorn-pluginprogrammet](http://maven.apache.org/plugins/maven-compiler-plugin/) och [Maven nyans-pluginprogrammet](http://maven.apache.org/plugins/maven-shade-plugin/). Kompilatorn plugin-programmet används för att kompilera topologin. Nyans plugin-programmet används för att förhindra dubbletter av licens JAR-paket som skapats med Maven. Anledningen till det här används är att duplicerade licensfiler orsakar ett fel vid körning i HDInsight-klustret. Med hjälp av maven-nyans-plugin-programmet med den `ApacheLicenseResourceTransformer` implementering förhindrar det här felet.

    Maven-nyans-plugin-programmet ger också en uber jar (eller fat jar) som innehåller alla beroenden som krävs av programmet.
4. Spara filen **pom.xml**.
5. Skapa en ny katalog med namnet **conf** i den **hbaseapp** directory. I den **conf** katalogen, skapa en fil med namnet **hbase-site.xml**. Använd följande som innehåll i filen:

        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
          * Copyright 2010 The Apache Software Foundation
          *
          * Licensed to the Apache Software Foundation (ASF) under one
          * or more contributor license agreements.  See the NOTICE file
          * distributed with this work for additional information
          * regarding copyright ownership.  The ASF licenses this file
          * to you under the Apache License, Version 2.0 (the
          * "License"); you may not use this file except in compliance
          * with the License.  You may obtain a copy of the License at
          *
          *     http://www.apache.org/licenses/LICENSE-2.0
          *
          * Unless required by applicable law or agreed to in writing, software
          * distributed under the License is distributed on an "AS IS" BASIS,
          * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
          * See the License for the specific language governing permissions and
          * limitations under the License.
          */
        -->
        <configuration>
          <property>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeeper0,zookeeper1,zookeeper2</value>
          </property>
          <property>
            <name>hbase.zookeeper.property.clientPort</name>
            <value>2181</value>
          </property>
        </configuration>

    Den här filen används för att läsa in HBase-konfigurationen för ett HDInsight-kluster.

   > [!NOTE]
   > Detta är en minimal hbase-site.xml-fil och innehåller utan minimikrav för HDInsight-klustret.

6. Spara den **hbase-site.xml** fil.

## <a name="create-the-application"></a>Skapa programmet
1. Gå till den **hbaseapp\src\main\java\com\microsoft\examples** directory och Byt namn på app.java-filen till **CreateTable.java**.
2. Öppna den **CreateTable.java** och Ersätt det befintliga innehållet med följande kod:

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
            // The following sets the znode root for Linux-based HDInsight
            //config.set("zookeeper.znode.parent","/hbase-unsecure");

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

    Det här är den **CreateTable** klass, vilket skapar en tabell med namnet **personer** och fyller den med vissa fördefinierade användare.
3. Spara den **CreateTable.java** fil.
4. I den **hbaseapp\src\main\java\com\microsoft\examples** katalogen, skapa en ny fil med namnet **SearchByEmail.java**. Använd följande kod som innehållet i den här filen:

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

            // Create a new regex filter
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

    Den **SearchByEmail** klassen kan användas för att fråga efter rader genom e-postadress. Eftersom den använder ett reguljärt uttryck filter kan du ange en sträng eller ett reguljärt uttryck när du använder klassen.
5. Spara den **SearchByEmail.java** fil.
6. I den **hbaseapp\src\main\hava\com\microsoft\examples** katalogen, skapa en ny fil med namnet **DeleteTable.java**. Använd följande kod som innehållet i den här filen:

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

    Den här klassen är för att rensa det här exemplet genom att inaktivera och släppa tabellen som skapats av den **CreateTable** klass.
7. Spara den **DeleteTable.java** fil.

## <a name="build-and-package-the-application"></a>Bygga och paketera programmet
1. Öppna en kommandotolk och ändra sökvägen till den **hbaseapp** directory.
2. Använd följande kommando för att skapa ett JAR-filen som innehåller programmet:

        mvn clean package

    Detta rensar alla tidigare byggartefakter, hämtas eventuella beroenden som inte redan har installerats, sedan skapar och paketerar programmet.
3. När kommandot har slutförts, den **hbaseapp\target** katalogen innehåller en fil med namnet **hbaseapp-1.0-SNAPSHOT.jar**.

   > [!NOTE]
   > Den **hbaseapp-1.0-SNAPSHOT.jar** filen är en uber jar (kallas ibland för en fat jar) som innehåller alla beroenden som krävs för att köra programmet.

## <a name="upload-the-jar-file-and-start-a-job"></a>Ladda upp JAR-filen och starta ett jobb
Det finns många sätt att överföra en fil till ditt HDInsight-kluster, enligt beskrivningen i [överföra data för Hadoop-jobb i HDInsight](hdinsight-upload-data.md). Följande steg använder Azure PowerShell.

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

1. När du installerar och konfigurerar Azure PowerShell, skapa en ny fil med namnet **hbase runner.psm1**. Använd följande som innehåll i den här filen:

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
        $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile $jarFile `
            -ClassName $className `
            -Arguments $emailRegex

        # Get the job output
        $job = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $jobDefinition `
            -HttpCredential $creds
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        if($showErr)
        {
        Write-Host "STDERR"
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -HttpCredential $creds `
                    -DisplayOutputType StandardError
        }
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
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
            Set-AzureStorageBlobContent -File $localPath `
                -Blob $destinationPath `
                -force:$force `
                -Container $storage.container `
                -Context $storage.context
        }

        function FindAzure {
            # Is there an active Azure subscription?
            $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
            if(-not($sub))
            {
                throw "No active Azure subscription found! If you have a subscription, use the Connect-AzureRmAccount cmdlet to login to your subscription."
            }
        }

        function GetStorage {
            param(
                [Parameter(Mandatory = $true)]
                [String]$clusterName
            )
            $hdi = Get-AzureRmHDInsightCluster -ClusterName $clusterName
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
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
            -ResourceGroupName $resourceGroup)[0].Value
            # Get the resource group, in case we need that
            $return.resourceGroup = $resourceGroup
            # Get the storage context, as we can't depend
            # on using the default storage context
            $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
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

    Den här filen innehåller två moduler:

   * **Lägg till HDInsightFile** – används för att ladda upp filer till HDInsight
   * **Start-HBaseExample** – används för att köra de klasser som skapades tidigare
2. Spara den **hbase runner.psm1** fil.
3. Öppna ett nytt Azure PowerShell-fönster, ändrar sökvägen till den **hbaseapp** katalog och kör följande kommando.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    Ändra sökvägen till platsen för den **hbase runner.psm1** filen som skapades tidigare. Detta registrerar modulen för Azure PowerShell-session.
4. Använd följande kommando för att ladda upp den **hbaseapp-1.0-SNAPSHOT.jar** till ditt HDInsight-kluster.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    Ersätt **hdinsightclustername** med namnet på ditt HDInsight-kluster. Kommandot laddar upp den **hbaseapp-1.0-SNAPSHOT.jar** till den **exempel/JAR-filer** plats i den primära lagringen för ditt HDInsight-kluster.
5. När filerna har överförts, Använd följande kod för att skapa en tabell med den **hbaseapp**:

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    Ersätt **hdinsightclustername** med namnet på ditt HDInsight-kluster.

    Det här kommandot skapar en ny tabell med namnet **personer** i ditt HDInsight-kluster. Det här kommandot visar inte några utdata i konsolfönstret.
6. Om du vill söka efter poster i tabellen, använder du följande kommando:

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    Ersätt **hdinsightclustername** med namnet på ditt HDInsight-kluster.

    Detta kommando använder de **SearchByEmail** klassen för att söka efter alla rader där den **contactinformation** kolumnserie och **e-post** kolumnen innehåller strängen **contoso.com**. Du bör få följande resultat:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Med hjälp av **fabrikam.com** för den `-emailRegex` värde returnerar de användare som har **fabrikam.com** i postfältet e-. Eftersom den här sökningen har implementerats med hjälp av ett reguljärt uttryck baserat filter kan du kan också ange reguljära uttryck, till exempel **^ r**, vilket returnerar poster där e-postmeddelandet börjar med bokstaven ”r”.

## <a name="delete-the-table"></a>Ta bort tabellen
När du är klar med exempel, Använd följande kommando från Azure PowerShell-session för att ta bort den **personer** tabell som används i det här exemplet:

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Ersätt **hdinsightclustername** med namnet på ditt HDInsight-kluster.

## <a name="troubleshooting"></a>Felsökning
### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Inga resultat eller oväntade resultat när du använder Start-HBaseExample
Använd den `-showErr` parameter för att visa standardfel (STDERR) som skapas när du kör jobbet.
