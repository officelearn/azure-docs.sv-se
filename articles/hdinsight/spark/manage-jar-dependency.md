---
title: Hantera JAR-beroenden - Azure HDInsight
description: I den här artikeln beskrivs metodtips för hantering av JAR-beroenden (Java Archive) för HDInsight-program.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: da3387dd9846847f7643ded43c8cbff8ed8b166e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77135738"
---
# <a name="jar-dependency-management-best-practices"></a>Metodtips för HANTERING AV JAR-beroendehantering

Komponenter som är installerade på HDInsight-kluster har beroenden av tredjepartsbibliotek. Vanligtvis refereras en specifik version av vanliga moduler som Guava av dessa inbyggda komponenter. När du skickar ett program med dess beroenden kan det orsaka en konflikt mellan olika versioner av samma modul. Om komponentversionen som du refererar till i klasssökvägen först kan inbyggda komponenter skapa undantag på grund av versionsinkompatibilitet. Men om inbyggda komponenter injicerar sina beroenden på classpath först, `NoSuchMethod`kan ditt program kasta fel som .

För att undvika versionskonflikt bör du överväga att skugga dina programberoenden.

## <a name="what-does-package-shading-mean"></a>Vad betyder paketskuggning?
Skuggning är ett sätt att inkludera och byta namn på beroenden. Klasser och skrivs om berörda bytekod och resurser för att skapa en privat kopia av dina beroenden.

## <a name="how-to-shade-a-package"></a>Hur skugga ett paket?

### <a name="use-uber-jar"></a>Använd uber-burk
Uber-jar är en enda burkfil som innehåller både programburken och dess beroenden. Beroendena i Uber-jar är som standard inte skuggade. I vissa fall kan detta medföra versionskonflikt om andra komponenter eller program refererar till en annan version av dessa bibliotek. För att undvika detta kan du skapa en Uber-Jar-fil med några (eller alla) av beroendena skuggade.

### <a name="shade-package-using-maven"></a>Skugga paket med Maven
Maven kan bygga program skrivna både i Java och Scala. Maven-skugga-plugin kan hjälpa dig att skapa en skuggad uber-burk enkelt.

Exemplet nedan visar `pom.xml` en fil som har uppdaterats för att skugga ett paket med maven-shade-plugin.  XML-avsnittet `<relocation>…</relocation>` flyttar klasser `com.google.guava` från `com.google.shaded.guava` paket till paket genom att flytta motsvarande JAR-filposter och skriva om den berörda bytekoden.

När `pom.xml`du har `mvn package` ändrat kan du köra för att bygga den skuggade uber-burken.

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>Skugga paket med SBT
SBT är också ett byggverktyg för Scala och Java. SBT har inte en skugga plugin som maven-skugga-plugin. Du kan `build.sbt` ändra filen till skuggpaket. 

Om du till `com.google.guava`exempel vill skugga kan `build.sbt` du lägga till kommandot nedan i filen:

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

Sedan kan `sbt clean` du `sbt assembly` köra och bygga den skuggade jar-filen. 

## <a name="next-steps"></a>Nästa steg

* [Använda HDInsight IntelliJ-verktyg](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox)

* [Skapa ett Scala Maven-program för Spark i IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-create-standalone-application)
