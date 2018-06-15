---
title: Java användardefinierad funktion (UDF) med Hive i HDInsight - Azure | Microsoft Docs
description: Lär dig hur du skapar en Java-baserad användardefinierad funktion (UDF) som fungerar med Hive. Det här exemplet UDF konverterar en tabell med textsträngar till gemener.
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
ms.date: 05/16/2018
ms.author: larryfr
ms.openlocfilehash: 00af8ca67af6ba3242c0fee6c50640944768ec4c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200766"
---
# <a name="use-a-java-udf-with-hive-in-hdinsight"></a>Använda en Java UDF med Hive i HDInsight

Lär dig hur du skapar en Java-baserad användardefinierad funktion (UDF) som fungerar med Hive. Java-UDF i det här exemplet konverterar en tabell med textsträngar till alla gemena tecken.

## <a name="requirements"></a>Krav

* Ett HDInsight-kluster 

    > [!IMPORTANT]
    > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

    De flesta stegen i det här dokumentet fungerar för både Windows - och Linux-baserade kluster. Dock är de steg som används för att överföra den kompilerade UDF till klustret och köra den specifika för Linux-baserade kluster. Länkar finns information som kan användas med Windows-baserade kluster.

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 eller senare (eller en motsvarande, till exempel OpenJDK)

* [Apache Maven](http://maven.apache.org/)

* En textredigerare eller IDE för Java

    > [!IMPORTANT]
    > Om du skapar Python-filer på en Windows-klient, måste du använda ett redigeringsprogram som använder LF som en rad avslutades. Om du inte är säker på om redigeringsprogram använder LF eller CRLF finns i [felsökning](#troubleshooting) avsnittet stegvisa instruktioner för att ta bort CR-tecken.

## <a name="create-an-example-java-udf"></a>Skapa ett exempel Java UDF 

1. Använd följande för att skapa ett nytt Maven-projekt från en kommandorad:

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

   > [!NOTE]
   > Om du använder PowerShell, måste du placera citattecken runt parametrarna. Till exempel `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.

    Det här kommandot skapar en katalog med namnet **exampleudf**, som innehåller Maven-projekt.

2. När projektet har skapats kan du ta bort den **exampleudf/src/test** katalogen som har skapats som en del av projektet.

3. Öppna den **exampleudf/pom.xml**, och Ersätt den befintliga `<dependencies>` post med följande XML-filen:

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

    De här posterna ange version för Hadoop och Hive som ingår i HDInsight 3,6. Du kan hitta information om versioner av Hadoop och Hive med HDInsight från den [HDInsight component-versioning](../hdinsight-component-versioning.md) dokumentet.

    Lägg till en `<build>` avsnittet före den `</project>` rad i slutet av filen. Det här avsnittet bör innehålla följande XML-filen:

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
                <version>2.3</version>
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

    Dessa poster definierar hur att skapa projektet. Mer specifikt versionen av Java som projektet använder och hur du skapar en uberjar för distribution till klustret.

    Spara filen när de har ändrats.

4. Byt namn på **exampleudf/src/main/java/com/microsoft/examples/App.java** till **ExampleUDF.java**, och sedan öppna filen i redigeringsprogrammet.

5. Ersätt innehållet i den **ExampleUDF.java** med följande och spara filen.

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

## <a name="build-and-install-the-udf"></a>Skapa och installera en användardefinierad funktion

1. Använd följande kommando för att kompilera och paketera UDF-filen:

    ```bash
    mvn compile package
    ```

    Det här kommandot skapar och paketerar UDF till den `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` filen.

2. Använd den `scp` kommando för att kopiera filen till HDInsight-klustret.

    ```bash
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight
    ```

    Ersätt `myuser` med SSH-användarkonto för klustret. Ersätt `mycluster` med klustrets namn. Om du har använt ett lösenord för att skydda det SSH-kontot uppmanas du att ange lösenordet. Om du använder ett certifikat, kan du behöva använda de `-i` parametern för att ange fil för privat nyckel.

3. Ansluta till klustret med SSH.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

4. Kopiera jar-filen i HDInsight-lagringen från SSH-session.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Använda en användardefinierad funktion från Hive

1. Använd följande för att starta klienten Beeline från SSH-session.

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Det här kommandot förutsätter att du använde standardvärdet **admin** för inloggningskontot för klustret.

2. När du kommer till den `jdbc:hive2://localhost:10001/>` uppmanar, ange följande om du vill lägga till en användardefinierad funktion Hive och exponera som en funktion.

    ```hiveql
    ADD JAR wasb:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

    > [!NOTE]
    > Det här exemplet förutsätter att lagringen Azure standardlagring för klustret. Om klustret använder Data Lake Store i stället ändrar den `wasb:///` värde till `adl:///`.

3. Använda en användardefinierad funktion för att konvertera värden som hämtats från en tabell till gemen strängar.

    ```hiveql
    SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;
    ```

    Den här frågan väljer plattform för enheter (Android, Windows, iOS, etc.) från tabellen, konvertera strängen till lägre fall och visa dem sedan. Utdata liknar följande:

        +----------+--+
        |   _c0    |
        +----------+--+
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        +----------+--+

## <a name="next-steps"></a>Nästa steg

Andra sätt att arbeta med Hive finns [använda Hive med HDInsight](hdinsight-use-hive.md).

Mer information om Hive User-Defined funktioner finns [Hive operatorer och användardefinierade funktioner](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) avsnitt i Hive-wiki på apache.org.
