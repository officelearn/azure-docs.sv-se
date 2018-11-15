---
title: Köra anpassade MapReduce-program – Azure HDInsight
description: När och hur du kan köra anpassade MapReduce-program i HDInsight.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: ashishth
ms.openlocfilehash: ccc30d336542622048cc28b991d5ecf616133c5a
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633826"
---
# <a name="run-custom-mapreduce-programs"></a>Köra anpassade MapReduce-program

Apache Hadoop-baserade stordata system, till exempel HDInsight aktivera databearbetning med hjälp av en mängd olika verktyg och tekniker. I följande tabell beskrivs de huvudsakliga fördelar och överväganden för vart och ett.

| Mekanism för fråga | Fördelar | Överväganden |
| --- | --- | --- |
| **Apache Hive med HiveQL** | <ul><li>En utmärkt lösning för satsvis bearbetning och analys av stora mängder data som inte kan ändras för sammanfatta data, och på begäran frågor. Den använder en välbekant SQL-liknande syntax.</li><li>Den kan användas för att producera beständiga datatabeller som enkelt kan partitioneras och indexeras.</li><li>Du kan skapa flera externa tabeller och vyer över samma data.</li><li>Det stöder en enkel data warehouse-implementering med omfattande funktioner för skalbarhet och feltolerans för datalagring och bearbetning.</li></ul> | <ul><li>Det krävs källdata till ha åtminstone vissa identifierbar struktur.</li><li>Det är inte lämpligt för förfrågningar i realtid och uppdateringar för raden. Det är bäst för batch-jobb över stora mängder data.</li><li>Det kanske inte kan utföra vissa typer av komplexa uppgifter.</li></ul> |
| **Apache Pig med Pig Latin** | <ul><li>En utmärkt lösning för att manipulera data som du anger, sammanslagning och filtrering datauppsättningar, tillämpar funktioner på poster eller grupper av poster, och för omstrukturering av data genom att definiera kolumner, gruppering värden eller genom att omvandla kolumner till rader.</li><li>Det kan använda en arbetsflödesbaserad metod som en sekvens med åtgärder på data.</li></ul> | <ul><li>SQL-användare kan hitta Pig Latin är mindre vanliga och svårare att använda än HiveQL.</li><li>Standardutdata är vanligtvis en textfil och det kan vara svårare att använda med visualiseringsverktyg, till exempel Excel. Du kommer vanligtvis lager med en Hive-tabell över utdata.</li></ul> |
| **Anpassad karta/minska** | <ul><li>Det ger fullständig kontroll över kartan och minska faser och körning.</li><li>Det gör att frågor optimeras för att få maximala prestanda från klustret eller för att minimera belastningen på servrarna och nätverk.</li><li>Komponenterna kan skrivas i ett antal välkända språk.</li></ul> | <ul><li>Det är svårare än att använda Pig- eller Hive eftersom du måste skapa din egen karta och minska komponenter.</li><li>Det är svårare att implementera processer som behöver ansluta till datauppsättningar.</li><li>Även om det finns Testramverk, är felsökning kod mer komplex än en normal programmet eftersom koden körs som ett batchjobb kontrolleras av jobbschemat Hadoop.</li></ul> |
| **Apache HCatalog** | <ul><li>Den avlägsnar sökvägsinformationen lagring, vilket gör administration och ta bort behovet av att veta var data lagras.</li><li>Det gör att meddelanden om händelser, till exempel datatillgänglighet, vilket gör att andra verktyg som Oozie att upptäcka när åtgärder har inträffat.</li><li>Den visar en relationell vy av data, inklusive partitionerade efter nyckel, och gör det enkelt att komma åt data.</li></ul> | <ul><li>Det stöder RCFile, CSV, text, JSON-text, SequenceFile och ORC-filformat som standard, men du kan behöva skriva en anpassad SerDe för andra format.</li><li>HCatalog är inte trådsäker.</li><li>Det finns vissa begränsningar för-datatyper för kolumner när du använder HCatalog-inläsaren i Pig-skript. Mer information finns i [HCatLoader datatyper](http://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) i Apache HCatalog-dokumentationen.</li></ul> |

Normalt använder du de enklaste metoderna som ger de resultat som du behöver. Exempelvis kan du nå dessa resultat med hjälp av bara Hive, men för mer komplicerade scenarier du kan behöva använda Pig, eller även skriva din egen kartan och minska komponenter. Du kan också bestämma när du experimentera med Hive och Pig, som anpassade mappar och minska komponenter kan ge bättre prestanda genom att du kan finjustera och optimera bearbetningen.

## <a name="custom-mapreduce-components"></a>Anpassad karta/minska komponenter

Kartan/minska kod består av två separata funktioner som implementerats som **kartan** och **minska** komponenter. Den **kartan** komponenten körs parallellt på flera klusternoder varje nod tillämpa mappningen på nodens egna delmängd av data. Den **minska** komponenten sorterar och sammanfattar resultaten från alla funktioner i kartan. Mer information om dessa två komponenter finns i [använda MapReduce i Hadoop på HDInsight](hdinsight-use-mapreduce.md).

I de flesta HDInsight transaktionsbearbetning är det enklare och mer effektivt att använda en högre nivå abstraktion, till exempel Pig- eller Hive. Du kan också skapa anpassade kartan och minska komponenter för användning i Hive-skript för att utföra mer avancerad bearbetning.

Anpassad karta/minska komponenter är vanligen skrivna i Java. Hadoop-tillhandahåller ett strömmande gränssnitt som gör också att komponenter som ska användas som har utvecklats på andra språk som C#, F#, Visual Basic, Python och JavaScript.

* En genomgång om hur du utvecklar anpassade Java MapReduce-program finns i [utveckla Java MapReduce-program för Hadoop på HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).
* Ett exempel med hjälp av Python finns i [utveckla Python-strömningsprogram MapReduce för HDInsight](apache-hadoop-streaming-python.md).

Överväg att skapa din egen kartan och minska komponenter för följande villkor:

* Du måste behandla data som är helt Ostrukturerade genom att dela upp data och använder anpassad logik för att hämta strukturerad information från den.
* Du vill utföra komplexa uppgifter som är svåra (eller omöjliga) att uttrycka i Pig- eller Hive-utan att behöva använda till att skapa en UDF. Du kan behöva använda en extern geokodningstjänst för att konvertera latitud och longitud koordinater eller IP-adresser i källdata till geografiska namn.
* Du vill använda din befintliga .NET, Python och JavaScript-kod i kartan/minska komponenter med hjälp av den Hadoop-strömmande gränssnitt.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Ladda upp och köra anpassade MapReduce-program

De vanligaste MapReduce-program skrivna i Java och kompileras till en jar-fil.

1. När du har utvecklat, kompileras och testas MapReduce-program, använder du den `scp` kommando för att ladda upp din jar-filen till huvudnoden.

    ```bash
    scp mycustomprogram.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Ersätt **användarnamn** med SSH-användarkontot för klustret. Ersätt **CLUSTERNAME** med klustrets namn. Om du använder ett lösenord för att skydda SSH-kontot, uppmanas du att ange lösenordet. Om du använder ett certifikat, kan du behöva använda den `-i` parametern för att ange filen för privat nyckel.

2. Ansluta till klustret med [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Köra MapReduce-program via YARN från SSH-sessionen.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    Det här kommandot skickar MapReduce-jobbet till YARN. Indatafilen är `/example/data/sample.log`, och katalogen är `/example/data/logoutput`. Indatafilen och eventuella utdatafiler sparas till standardlagringen för klustret.

## <a name="next-steps"></a>Nästa steg

* [Använda C# med MapReduce med Hadoop i HDInsight för direktuppspelning](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Utveckla Java MapReduce-program för Hadoop på HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Utveckla Python-strömmande MapReduce-program för HDInsight](apache-hadoop-streaming-python.md)
* [Använd Azure Toolkit för Eclipse för att skapa Spark-program för ett HDInsight-kluster](../spark/apache-spark-eclipse-tool-plugin.md)
* [Använd användardefinierade Python funktioner (UDF) med Hive och Pig i HDInsight](python-udf-hdinsight.md)
