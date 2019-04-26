---
title: Java-användardefinierade funktion (UDF med Apache Hive i HDInsight - Azure)
description: Lär dig hur du skapar en Java-baserade användardefinierade funktion (UDF) som fungerar med Apache Hive. Det här exemplet UDF konverterar en tabell med textsträngar till gemener.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 8d4f8efe-2f01-4a61-8619-651e873c7982
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 03/21/2019
ms.date: 04/29/2019
ms.author: v-yiso
ms.openlocfilehash: b8417fe4c15259a7fd485254cf9edd2c8c082e92
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60537640"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Använda en Java UDF med Apache Hive i HDInsight

Lär dig hur du skapar en Java-baserade användardefinierade funktion (UDF) som fungerar med Apache Hive. Java-UDF i det här exemplet konverterar en tabell med textsträngar till alla gemena tecken.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Ett Hadoop-kluster på HDInsight. Se [Kom igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).
* [Java Developer Kit (JDK) version 8](https://aka.ms/azure-jdks)
* [Apache Maven](https://maven.apache.org/download.cgi) korrekt [installerat](https://maven.apache.org/install.html) enligt Apache.  Maven är ett projekt buildsystemet för Java-projekt.
* Den [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) för ditt kluster primär lagring. Detta skulle vara wasb: / / för Azure Storage, abfs: / / för Azure Data Lake Storage Gen2 eller adl: / / för Azure Data Lake Storage Gen1. Om säker överföring har aktiverats för Azure Storage eller Data Lake Storage Gen2, URI: N blir wasbs: / / eller abfss: / /, respektive Se även [säker överföring](../../storage/common/storage-require-secure-transfer.md).

* En textredigerare eller IDE för Java

    > [!IMPORTANT]  
    > Om du skapar en Python-filer på en Windows-klient, måste du använda ett redigeringsprogram som använder LF som en raden slutar. Om du inte är säker på om din Redigerare använder LF eller CRLF se den [felsökning](#troubleshooting) avsnitt för anvisningar för att ta bort CR-tecken.

## <a name="test-environment"></a>Testmiljö
Den miljö som ska användas för den här artikeln har en dator som kör Windows 10.  Kommandona utfördes i en kommandotolk och de olika filerna har redigerats med anteckningar. Ändra för din miljö.

Ange kommandona nedan för att skapa en arbetsmiljö från en kommandotolk:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Skapa ett exempel Java UDF

1. Skapa ett nytt Maven-projekt genom att ange följande kommando:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    Det här kommandot skapar en katalog med namnet `exampleudf`, som innehåller Maven-projekt.

2. När projektet har skapats, ta bort den `exampleudf/src/test` katalog som skapades som en del av projektet genom att ange följande kommando:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. Öppna `pom.xml` genom att ange kommandot nedan:

    ```cmd
    notepad pom.xml
    ```

    Ersätt den befintliga `<dependencies>` posten med följande XML:

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    De här posterna ange versionen av Hadoop och Hive som medföljer HDInsight 3.6. Du hittar information om vilka versioner av Hadoop och Hive med HDInsight från den [versionshantering för HDInsight](../hdinsight-component-versioning.md) dokumentet.

    Lägg till en `<build>` avsnittet före den `</project>` rad i slutet av filen. Det här avsnittet bör innehålla följande XML:

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.6  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.1</version>
                <configuration>
                    <!-- Keep us from getting a can't overwrite file error -->
                    <transformers>
                        <transformer
                                implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
                    <filters>
                        <filter>
                            <artifact>*:*</artifact>
                            <excludes>
                                <exclude>META-INF/*.SF</exclude>
                                <exclude>META-INF/*.DSA</exclude>
                                <exclude>META-INF/*.RSA</exclude>
                            </excludes>
                        </filter>
                    </filters>
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

    De här posterna definiera hur du skapar projektet. Mer specifikt versionen av Java som projektet använder och hur du skapar en uberjar för distribution till klustret.

    Spara filen när ändringarna har gjorts.

4. Ange kommandot nedan för att skapa och öppna en ny fil `ExampleUDF.java`:

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Kopiera och klistra in java-koden nedan i den nya filen. Stäng filen.

    ```java
    package com.microsoft.examples;

    import org.apache.hadoop.hive.ql.exec.Description;
    import org.apache.hadoop.hive.ql.exec.UDF;
    import org.apache.hadoop.io.*;

    // Description of the UDF
    @Description(
        name="ExampleUDF",
        value="returns a lower case version of the input string.",
        extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
    )
    public class ExampleUDF extends UDF {
        // Accept a string input
        public String evaluate(String input) {
            // If the value is null, return a null
            if(input == null)
                return null;
            // Lowercase the input string and return it
            return input.toLowerCase();
        }
    }
    ```

    Den här koden implementerar en UDF som tar emot ett strängvärde och returnerar en gemen version av strängen.

## <a name="build-and-install-the-udf"></a>Skapa och installera en användardefinierad funktion

I nedanstående kommandon ersätter `sshuser` med faktiska användarnamn om det är olika. Ersätt `mycluster` med faktiska klustrets namn.

1. Kompilera och paketera en användardefinierad funktion genom att ange följande kommando:

    ```cmd
    mvn compile package
    ```

    Det här kommandot skapar och paketerar UDF-filen till den `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` filen.

2. Använd den `scp` kommando för att kopiera filen till HDInsight-kluster genom att ange följande kommando:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight
    ```

3. Anslut till klustret med SSH genom att ange följande kommando:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.cn
    ```

4. Kopiera jar-filen till HDInsight storage från öppna SSH-sessionen.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Använd UDF från Hive

1. Starta Beeline klienten från SSH-sessionen genom att ange följande kommando:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Det här kommandot förutsätter att du använder standardinställningarna för **admin** för inloggningskontot för klustret.

2. När du når den `jdbc:hive2://localhost:10001/>` uppmanar, anger du följande för att lägga till en användardefinierad funktion i Hive och gör den tillgänglig som en funktion.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. Använda en användardefinierad funktion för att konvertera värden som hämtats från en tabell till gemen strängar.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    Den här frågan väljer tillståndet från tabellen, konvertera strängen till lägre för användningsfall och visa dem tillsammans med namnet på oförändrade. Utdata ser ut som följande text:

        +---------------+---------------+--+
        |  exampleudf   |     state     |
        +---------------+---------------+--+
        | california    | California    |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | utah          | Utah          |
        | utah          | Utah          |
        | colorado      | Colorado      |
        +---------------+---------------+--+

## <a name="troubleshooting"></a>Felsökning

När du kör hive-jobb, kan det uppstå ett fel som liknar följande text:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Det här problemet kan ha orsakats av radbrytningar i Python-filen. Många Windows-redigerare som standard använder CRLF som raden slutar, men Linux-program vanligtvis förväntar sig LF.

Du kan använda följande PowerShell-uttryck för att ta bort CR tecken innan du laddar upp filen till HDInsight:

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>Nästa steg

Andra sätt att arbeta med Hive, se [använda Apache Hive med HDInsight](hdinsight-use-hive.md).

Mer information om Hive User-Defined funktioner finns i [Apache Hive operatorer och användardefinierade funktioner](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) delen av Hive-wiki på apache.org.
