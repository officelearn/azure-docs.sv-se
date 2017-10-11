---
title: "Skapa ett HBase för Java-program för Windows-baserade Azure HDInsight | Microsoft Docs"
description: "Lär dig hur du använder Apache Maven för att skapa ett Java-baserad Apache HBase-program och sedan distribuera den till en Windows-baserad Azure HDInsight-kluster."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7f4a4e02-45ab-40dd-842b-3ec034f256c9
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 59c9af5a91b107e68a676f02fe5a936f955b22fa
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="use-maven-to-build-java-applications-that-use-hbase-with-windows-based-hdinsight-hadoop"></a>Använd Maven för att skapa Java-program som använder HBase med Windows-baserade HDInsight (Hadoop)
Lär dig hur du skapar och skapa en [Apache HBase](http://hbase.apache.org/) program i Java med hjälp av Apache Maven. Sedan använda programmet Azure HDInsight (Hadoop).

[Maven](http://maven.apache.org/) är ett projekt hantering och förståelse programverktyg som hjälper dig att skapa programvara, dokumentation och rapporter för Java-projekt. Du lär dig hur du skapar ett grundläggande Java-program som som skapar frågor, och tar bort en HBase-tabell i ett Azure HDInsight-kluster i den här artikeln.

> [!IMPORTANT]
> Stegen i det här dokumentet kräver ett HDInsight-kluster som använder Windows. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="requirements"></a>Krav
* [Java platform JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 eller senare
* [Maven 3.](http://maven.apache.org/)
* Ett Windows-baserade HDInsight-kluster med HBase

    > [!NOTE]
    > Stegen i det här dokumentet har testats med HDInsight-kluster version 3.2 och 3.3. De standardvärden som i exemplen är för ett 3.3 HDInsight-kluster.

## <a name="create-the-project"></a>Skapa projektet
1. Från kommandoraden i din utvecklingsmiljö, ändra kataloger till platsen där du vill skapa projektet, till exempel `cd code\hdinsight`.
2. Använd den **mvn** kommandot, som installeras med Maven för att generera scaffold-teknik för projektet.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Det här kommandot skapar en katalog på den aktuella platsen med namnet som angetts av den **artefakt-ID** parameter (**hbaseapp** i det här exemplet.) Den här katalogen innehåller följande:

   * **pom.XML**: I projektet Object Model ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) innehåller information och konfiguration information som används för att skapa projektet.
   * **SRC**: den katalog som innehåller den **main\java\com\microsoft\examples** directory, där du ska skapa programmet.
3. Ta bort den **src\test\java\com\microsoft\examples\apptest.java** filen eftersom den inte används i det här exemplet.

## <a name="update-the-project-object-model"></a>Uppdatera projekt-objektmodell
1. Redigera den **pom.xml** och Lägg till följande kod inne i `<dependencies>` avsnitt:

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    Det här avsnittet visar Maven att projektet kräver **hbase-klient** version **1.1.2**. Vid kompileringen hämtas detta beroende från standard Maven-databasen. Du kan använda den [Maven centrala lagringsplatsen Sök](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) lära dig mer om detta beroende.

   > [!IMPORTANT]
   > Versionsnumret måste matcha versionen av HBase som ingår i ditt HDInsight-kluster. Använd följande tabell för att hitta rätt versionsnumret.
   >
   >

   | HDInsight-kluster av version | HBase-version som ska användas |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3 |1.1.2 |

    Mer information om HDInsight-versioner och komponenter finns [vilka är de olika Hadoop-komponenterna med HDInsight](hdinsight-component-versioning.md).
2. Om du använder ett 3.3 HDInsight-kluster måste du också lägga till följande för att den `<dependencies>` avsnitt:

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>

    Phoenix-kärnkomponenterna, som används av Hbase-version läses in detta beroende 1.1.x.
3. Lägg till följande kod i den **pom.xml** fil. Det här avsnittet måste finnas i den `<project>...</project>` taggar i filen, till exempel mellan `</dependencies>` och `</project>`.

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

    Den `<resources>` avsnittet konfigureras en resurs (**conf\hbase site.xml**) som innehåller konfigurationsinformation för HBase.

   > [!NOTE]
   > Du kan också ange konfigurationsvärden via kod. Visa kommentarerna i den **CreateTable** för hur du gör följande exempel.
   >
   >

    Detta `<plugins>` avsnittet konfigureras den [Maven-kompilatorn Plugin](http://maven.apache.org/plugins/maven-compiler-plugin/) och [Maven skugga Plugin](http://maven.apache.org/plugins/maven-shade-plugin/). Kompilatorn plugin-programmet används för att kompilera topologin. Skugga plugin-programmet används för att förhindra licens dubbletter av JAR-paketet som skapas genom Maven. Det används beror på att duplicerade licensfiler orsakar ett fel vid körning i HDInsight-klustret. Med hjälp av maven-skugga-plugin-programmet med den `ApacheLicenseResourceTransformer` implementering förhindrar det här felet.

    Maven-skugga-plugin-programmet ger också en uber jar (eller fat jar) som innehåller alla beroenden som krävs för programmet.
4. Spara den **pom.xml** fil.
5. Skapa en ny katalog med namnet **conf** i den **hbaseapp** directory. I den **conf** directory, skapa en fil med namnet **hbase-site.xml**. Använd följande som innehåll i filen:

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

    Den här filen används för att läsa in HBase-konfiguration för ett HDInsight-kluster.

   > [!NOTE]
   > Detta är en minimal hbase-site.xml-fil och den innehåller utan minimikrav för HDInsight-klustret.

6. Spara den **hbase-site.xml** fil.

## <a name="create-the-application"></a>Skapa programmet
1. Gå till den **hbaseapp\src\main\java\com\microsoft\examples** katalog och Byt namn på app.java filen till **CreateTable.java**.
2. Öppna den **CreateTable.java** filen och ersätter det befintliga innehållet med följande kod:

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

    Det här är den **CreateTable** -klassen, som skapar en tabell med namnet **personer** och fylla det med vissa fördefinierade användare.
3. Spara den **CreateTable.java** fil.
4. I den **hbaseapp\src\main\java\com\microsoft\examples** directory, skapa en ny fil med namnet **SearchByEmail.java**. Använd följande kod som innehållet i den här filen:

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

    Den **SearchByEmail** klassen kan användas för att söka efter rader av e-postadress. Eftersom den använder ett reguljärt uttryck för filter kan du ange en sträng eller ett reguljärt uttryck när du använder klassen.
5. Spara den **SearchByEmail.java** fil.
6. I den **hbaseapp\src\main\hava\com\microsoft\examples** directory, skapa en ny fil med namnet **DeleteTable.java**. Använd följande kod som innehållet i den här filen:

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

## <a name="build-and-package-the-application"></a>Skapa och paketera programmet
1. Öppna en kommandotolk och ändra sökvägen till den **hbaseapp** directory.
2. Använd följande kommando för att skapa en JAR-fil som innehåller programmet:

        mvn clean package

    Detta rensar alla tidigare build-artefakter, hämtas eventuella beroenden som inte redan har installerats, sedan bygger och paket för programmet.
3. När kommandot har slutförts i **hbaseapp\target** katalogen innehåller en fil med namnet **hbaseapp-1.0-SNAPSHOT.jar**.

   > [!NOTE]
   > Den **hbaseapp-1.0-SNAPSHOT.jar** filen är en uber jar (kallas ibland för en fat jar) som innehåller alla beroenden som krävs för att köra programmet.

## <a name="upload-the-jar-file-and-start-a-job"></a>Överför JAR-filen och starta ett jobb
Det finns många sätt att överföra en fil till ditt HDInsight-kluster, enligt beskrivningen i [överföra data för Hadoop-jobb i HDInsight](hdinsight-upload-data.md). Azure PowerShell använder du följande steg.

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

1. Efter att installera och konfigurera Azure PowerShell, skapa en ny fil med namnet **hbase runner.psm1**. Använd följande som innehåll i den här filen:

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
                throw "No active Azure subscription found! If you have a subscription, use the Login-AzureRmAccount cmdlet to login to your subscription."
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

    Denna fil innehåller två moduler:

   * **Lägg till HDInsightFile** – används för att överföra filer till HDInsight
   * **Start-HBaseExample** – används för att köra de klasser som skapades tidigare
2. Spara den **hbase runner.psm1** fil.
3. Öppna ett nytt Azure PowerShell-fönster, ändra kataloger till den **hbaseapp** katalog och kör sedan följande kommando.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    Ändra sökvägen till platsen för den **hbase runner.psm1** filen som skapades tidigare. Detta registrerar modulen för den här Azure PowerShell-sessionen.
4. Använd följande kommando för att överföra den **hbaseapp-1.0-SNAPSHOT.jar** till HDInsight-kluster.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    Ersätt **hdinsightclustername** med namnet på ditt HDInsight-kluster. Kommandot laddar upp den **hbaseapp-1.0-SNAPSHOT.jar** till den **exempel/burkar** plats i den primära lagringsplatsen för ditt HDInsight-kluster.
5. När filerna har överförts, använda följande kod för att skapa en tabell med hjälp av den **hbaseapp**:

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    Ersätt **hdinsightclustername** med namnet på ditt HDInsight-kluster.

    Det här kommandot skapar en ny tabell med namnet **personer** i ditt HDInsight-kluster. Det här kommandot visar inte några utdata i konsolfönstret.
6. Om du vill söka efter i tabellen, använder du följande kommando:

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    Ersätt **hdinsightclustername** med namnet på ditt HDInsight-kluster.

    Detta kommando använder den **SearchByEmail** klassen för att söka efter alla rader där den **contactinformation** kolumnfamilj och **e-post** kolumnen innehåller strängen **contoso.com**. Du bör få följande resultat:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Med hjälp av **fabrikam.com** för den `-emailRegex` värde returnerar de användare som har **fabrikam.com** i fältet e-post. Eftersom den här sökningen implementeras med hjälp av ett reguljärt uttryck baserat filter, du kan också ange reguljära uttryck som **^ r**, vilka returnerar poster där e-postmeddelandet börjar med bokstaven ”r”.

## <a name="delete-the-table"></a>Ta bort tabellen
När du är klar med det här exemplet använder du följande kommando från Azure PowerShell-session för att ta bort den **personer** tabell som används i det här exemplet:

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Ersätt **hdinsightclustername** med namnet på ditt HDInsight-kluster.

## <a name="troubleshooting"></a>Felsökning
### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Inga resultat eller oväntade resultat när du använder Start HBaseExample
Använd den `-showErr` parametern för att visa standardfel (STDERR) som skapas när du kör jobbet.
