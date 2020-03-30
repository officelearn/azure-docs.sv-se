---
title: Java-användardefinierad funktion (UDF) med Apache Hive Azure HDInsight
description: Lär dig hur du skapar en Java-baserad användardefinierad funktion (UDF) som fungerar med Apache Hive. I det här exemplet konverterar UDF en tabell med textsträngar till gemener.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/20/2019
ms.openlocfilehash: 73a2a612a4eeb4a59f12abf0660fffb092f0547f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327197"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Använd en Java UDF med Apache Hive i HDInsight

Lär dig hur du skapar en Java-baserad användardefinierad funktion (UDF) som fungerar med Apache Hive. Java UDF i det här exemplet konverterar en tabell med textsträngar till gemener.

## <a name="prerequisites"></a>Krav

* Ett Hadoop-kluster på HDInsight. Se [Komma igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).
* [Java Developer Kit (JDK) version 8](https://aka.ms/azure-jdks)
* [Apache Maven](https://maven.apache.org/download.cgi) korrekt [installerad](https://maven.apache.org/install.html) enligt Apache.  Maven är ett projektbyggsystem för Java-projekt.
* [URI-schemat](../hdinsight-hadoop-linux-information.md#URI-and-scheme) för klustrets primära lagring. Detta skulle vara wasb:// för Azure Storage, abfs:// för Azure Data Lake Storage Gen2 eller adl:// för Azure Data Lake Storage Gen1. Om säker överföring är aktiverad för Azure `wasbs://`Storage, skulle URI vara .  Se även [säker överföring](../../storage/common/storage-require-secure-transfer.md).

* En textredigerare eller Java IDE

    > [!IMPORTANT]  
    > Om du skapar Python-filerna på en Windows-klient måste du använda en redigerare som använder LF som en radändning. Om du är osäker på om redigeraren använder LF eller CRLF läser du avsnittet [Felsökning](#troubleshooting) för steg för att ta bort CR-tecknet.

## <a name="test-environment"></a>Testmiljö

Miljön som användes för den här artikeln var en dator som kör Windows 10.  Kommandona utfördes i en kommandotolk och de olika filerna redigerades med Anteckningar. Ändra i enlighet med detta för din miljö.

Från en kommandotolk anger du kommandona nedan för att skapa en arbetsmiljö:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Skapa ett exempel på Java UDF

1. Skapa ett nytt Maven-projekt genom att ange följande kommando:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    Det här kommandot skapar `exampleudf`en katalog med namnet , som innehåller Maven-projektet.

2. När projektet har skapats `exampleudf/src/test` tar du bort katalogen som skapades som en del av projektet genom att ange följande kommando:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. Öppna `pom.xml` genom att ange kommandot nedan:

    ```cmd
    notepad pom.xml
    ```

    Ersätt sedan `<dependencies>` den befintliga posten med följande XML:

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

    Dessa poster anger vilken version av Hadoop och Hive som ingår i HDInsight 3.6. Du kan hitta information om versionerna av Hadoop och Hive som medföljer HDInsight från [HDInsight-komponentversionsdokumentet.](../hdinsight-component-versioning.md)

    Lägg `<build>` till ett `</project>` avsnitt före raden i slutet av filen. Det här avsnittet bör innehålla följande XML:

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

    Dessa poster definierar hur projektet ska byggas. Den version av Java som projektet använder och hur man skapar en uberjar för distribution till klustret.

    Spara filen när ändringarna har gjorts.

4. Ange kommandot nedan för att skapa `ExampleUDF.java`och öppna en ny fil:

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Kopiera och klistra in java-koden nedan i den nya filen. Stäng sedan filen.

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

    Den här koden implementerar en UDF som accepterar ett strängvärde och returnerar en gemen version av strängen.

## <a name="build-and-install-the-udf"></a>Bygga och installera UDF

Ersätt `sshuser` med det faktiska användarnamnet om det är annorlunda i kommandona nedan. Ersätt `mycluster` med det faktiska klusternamnet.

1. Kompilera och paketera UDF genom att ange följande kommando:

    ```cmd
    mvn compile package
    ```

    Det här kommandot bygger och paketerar UDF i `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` filen.

2. Använd `scp` kommandot för att kopiera filen till HDInsight-klustret genom att ange följande kommando:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. Anslut till klustret med SSH genom att ange följande kommando:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. Från den öppna SSH-sessionen kopierar du jar-filen till HDInsight-lagring.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Använd UDF från Hive

1. Starta Beeline-klienten från SSH-sessionen genom att ange följande kommando:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Det här kommandot förutsätter att du använde standardadministratören **för** inloggningskontot för klustret.

2. När du anländer `jdbc:hive2://localhost:10001/>` till prompten anger du följande för att lägga till UDF i Hive och exponera den som en funktion.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. Använd UDF för att konvertera värden som hämtats från en tabell till gemener strängar.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    Den här frågan väljer läget från tabellen, konverterar strängen till gemener och visar dem sedan tillsammans med det oförändrade namnet. Utdata liknar följande text:

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

När du kör hive-jobbet kan du stöta på ett fel som liknar följande text:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Det här problemet kan orsakas av radsluten i Python-filen. Många Windows-redigerare som standard använder CRLF som radslut, men Linux-program förväntar sig vanligtvis LF.

Du kan använda följande PowerShell-satser för att ta bort CR-tecknen innan du laddar upp filen till HDInsight:

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>Nästa steg

Andra sätt att arbeta med Hive finns i [Använda Apache Hive med HDInsight](hdinsight-use-hive.md).

Mer information om Hive-användardefinierade funktioner finns i avsnittet [Apache Hive-operatörer och användardefinierade funktioner](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) på Hive-wikin på apache.org.
