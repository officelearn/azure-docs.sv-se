---
title: Kör anpassade program för MapReduce - Azure HDInsight | Microsoft Docs
description: När och hur du använder anpassade MapReduce-program i HDInsight.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: ashishth
ms.openlocfilehash: 94d199642b409a2fd087ec1543651031a907d09f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31403040"
---
# <a name="run-custom-mapreduce-programs"></a>Köra anpassade MapReduce-program

System för Hadoop-baserade stordata, till exempel HDInsight aktivera databearbetning med hjälp av en mängd olika verktyg och tekniker. I följande tabell beskrivs de huvudsakliga fördelarna och överväganden för var och en.

| Mekanism för frågan | Fördelar | Överväganden |
| --- | --- | --- |
| **Hive med HiveQL** | <ul><li>En utmärkt lösning för batch-bearbetning och analys av stora mängder data ändras, för sammanfatta data, och på begäran frågor. Den använder en bekant SQL-liknande syntax.</li><li>Den kan användas för att skapa beständiga datatabeller som enkelt kan partitioneras och indexeras.</li><li>Du kan skapa flera externa tabeller och vyer över samma data.</li><li>Det stöder en enkel data warehouse-implementering som ger omfattande skalbar och feltolerans för bearbetning och lagring av data för.</li></ul> | <ul><li>Det krävs källdata ha åtminstone en viss identifierbar struktur.</li><li>Det är inte lämpligt för förfrågningar i realtid och uppdateringar för raden. Det är bäst för batchjobb över stora mängder data.</li><li>Det kanske inte kan utföra vissa typer av komplexa bearbetningsuppgifter.</li></ul> |
| **Pig med Pig Latin** | <ul><li>En utmärkt lösning för att hantera data som du anger, sammanslagning och filtrera datauppsättningar, tillämpa funktioner på poster eller poster, och för omstrukturering av data genom att definiera kolumner, gruppering värden eller genom att konvertera kolumner till rader.</li><li>Det kan använda en arbetsflödesbaserad metod som en sekvens med åtgärder på data.</li></ul> | <ul><li>SQL-användare kan hitta Pig Latin är mindre vanliga och svårare att använda än HiveQL.</li><li>Standardutdata är vanligtvis en textfil och därför kan vara svårare att använda med visualiseringsverktyg som Excel. Du kommer normalt layer en Hive-tabell över utdata.</li></ul> |
| **Anpassade kartan/minska** | <ul><li>Det ger fullständig kontroll över kartan och minska faser och körning.</li><li>Det tillåter frågor kan optimeras för att uppnå högsta prestanda från klustret eller för att minimera belastningen på servrarna och nätverket.</li><li>Komponenterna som kan skrivas i ett antal välkända språk.</li></ul> | <ul><li>Det är svårare än att använda Pig eller Hive eftersom du måste skapa en egen karta och minska komponenter.</li><li>Processer som behöver ansluta till datauppsättningar är svårare att implementera.</li><li>Även om det finns test ramverk är felsökning koden mer komplexa än en normal programmet eftersom koden körs som ett batchjobb under kontroll av jobbschemat Hadoop.</li></ul> |
| **HCatalog** | <ul><li>Den avlägsnar sökväg information om lagring, vilket gör administration och eliminerar behovet av att veta var data lagras.</li><li>Den gör att meddelanden om händelser, till exempel datatillgänglighet, så att andra verktyg, till exempel Oozie att identifiera när åtgärder har inträffat.</li><li>Den visar vyn relationella data, inklusive partitionering av nyckel, och gör det enkelt att åtkomst till data.</li></ul> | <ul><li>Den stöder RCFile, CSV-text, JSON-text, SequenceFile och ORC-filformat som standard, men du kan behöva skriva en anpassad SerDe för andra format.</li><li>HCatalog är inte trådsäker.</li><li>Det finns vissa begränsningar för-datatyper för kolumner när du använder HCatalog inläsaren i Pig-skript. Mer information finns i [HCatLoader datatyper](http://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) i Apache HCatalog-dokumentationen.</li></ul> |

Normalt använder du den enklaste av dessa metoder som kan ge de resultat du behöver. Till exempel du kanske få sådana resultat med hjälp av bara Hive, men för mer komplicerade scenarier du kan behöva använda Pig, eller även skriva egna kartan och minska komponenter. Du kan också bestämma när du experimenterar med Hive och Pig, som anpassade mappar och minska komponenter kan ge bättre prestanda genom att du kan finjustera och optimera bearbetning.

## <a name="custom-mapreduce-components"></a>Anpassade kartan/minska komponenter

Karta/minska koden består av två separata funktioner som implementeras som **kartan** och **minska** komponenter. Den **kartan** komponenten körs parallellt på flera klusternoder varje nod som gäller mappningen för nodens egna delmängden av data. Den **minska** komponenten sorterar och sammanfattar resultaten från funktionerna för kartan. Mer information om dessa två komponenter finns [använda MapReduce i Hadoop på HDInsight](hdinsight-use-mapreduce.md).

I de flesta scenarier med HDInsight bearbetning är det enklare och mer effektivt att använda en högre nivå abstraktion, till exempel Pig eller Hive. Du kan också skapa anpassade karta och minska komponenter för användning i Hive-skript för att utföra mer avancerad bearbetning.

Anpassade kartan/minska komponenter skrivs vanligtvis i Java. Hadoop erbjuder en strömmande gränssnitt som möjliggör också komponenter som ska användas som har utvecklats på andra språk, till exempel C#, F #, Visual Basic, Python och JavaScript.

* En genomgång om hur du utvecklar anpassade MapReduce Java-program finns [MapReduce utveckla Java-program för Hadoop på HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).
* Ett exempel som använder Python finns [utveckla Python strömning MapReduce program för HDInsight](apache-hadoop-streaming-python.md).

Överväg att skapa egna kartan och minska komponenter för följande villkor:

* Du måste bearbeta data som är helt Ostrukturerade genom att dela data och egen kod för att hämta strukturerad information från den.
* Du vill utföra komplicerade uppgifter som är svår (eller omöjligt) att uttrycka i Pig eller Hive utan att du skapar en UDF. Du kan behöva använda en extern geokodning tjänst för att konvertera latitud och longitud koordinater eller IP-adresser i källdata till geografisk platsnamn.
* Du vill använda din befintliga .NET, Python eller JavaScript-kod i kartan/minska komponenter med hjälp av Hadoop streaming gränssnitt.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Ladda upp och köra anpassade MapReduce

De vanligaste MapReduce-program är skriven i Java och kompileras till en jar-fil.

1. När du har utvecklats, kompileras och testat MapReduce-program, använder den `scp` kommando för att överföra jar-filen till headnode.

    ```bash
    scp mycustomprogram.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Ersätt **användarnamn** med SSH-användarkonto för klustret. Ersätt **KLUSTERNAMN** med klustrets namn. Om du har använt ett lösenord för att skydda det SSH-kontot uppmanas du att ange lösenordet. Om du använder ett certifikat, kan du behöva använda de `-i` parametern för att ange fil för privat nyckel.

2. Anslut till klustret med [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Köra programmet MapReduce via YARN från SSH-session.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    Det här kommandot skickar MapReduce-jobb att YARN. Indatafilen är `/example/data/sample.log`, och den angivna katalogen är `/example/data/logoutput`. Indatafilen och utgående filer lagras till standardlagring för klustret.

## <a name="next-steps"></a>Nästa steg

* [Använda C# med MapReduce strömning på Hadoop i HDInsight](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Utveckla Java-MapReduce-program för Hadoop i HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Utveckla Python strömning MapReduce program för HDInsight](apache-hadoop-streaming-python.md)
* [Använda Azure Toolkit för Eclipse för att skapa Spark-program för ett HDInsight-kluster](../spark/apache-spark-eclipse-tool-plugin.md)
* [Använd användardefinierade Python funktioner (UDF) med Hive och Pig i HDInsight](python-udf-hdinsight.md)
