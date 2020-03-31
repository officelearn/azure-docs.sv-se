---
title: Kör anpassade MapReduce-program - Azure HDInsight
description: När och hur du kör anpassade Apache MapReduce-program på Azure HDInsight-kluster.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 78623f738285e781cb561a3844db8fbf37226929
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645029"
---
# <a name="run-custom-mapreduce-programs"></a>Köra anpassade MapReduce-program

Apache Hadoop-baserade stordatasystem som HDInsight möjliggör databehandling med hjälp av ett brett utbud av verktyg och tekniker. I följande tabell beskrivs de viktigaste fördelarna och övervägandena för var och en.

| Frågemekanism | Fördelar | Överväganden |
| --- | --- | --- |
| **Apache Hive med HiveQL** | <ul><li>En utmärkt lösning för batchbearbetning och analys av stora mängder oföränderliga data, för datasumrisering och för frågor på begäran. Den använder en välbekant SQL-liknande syntax.</li><li>Den kan användas för att producera beständiga datatabeller som enkelt kan partitioneras och indexeras.</li><li>Flera externa tabeller och vyer kan skapas över samma data.</li><li>Den stöder en enkel implementering av informationslager som ger omfattande utskalnings- och feltoleransfunktioner för datalagring och databearbetning.</li></ul> | <ul><li>Det kräver att källdata har åtminstone någon identifierbar struktur.</li><li>Den är inte lämplig för realtidsfrågor och uppdateringar på radnivå. Det används bäst för batchjobb över stora uppsättningar data.</li><li>Det kanske inte kan utföra vissa typer av komplexa bearbetningsuppgifter.</li></ul> |
| **Apache Pig med Pig Latin** | <ul><li>En utmärkt lösning för att manipulera data som uppsättningar, sammanfoga och filtrera datauppsättningar, tillämpa funktioner på poster eller grupper av poster och för omstrukturering av data genom att definiera kolumner, genom att gruppera värden eller genom att konvertera kolumner till rader.</li><li>Den kan använda en arbetsflödesbaserad metod som en sekvens av åtgärder på data.</li></ul> | <ul><li>SQL-användare kan hitta Pig Latin är mindre bekant och svårare att använda än HiveQL.</li><li>Standardutdata är vanligtvis en textfil och kan därför vara svårare att använda med visualiseringsverktyg som Excel. Vanligtvis lager en Hive-tabell över utdata.</li></ul> |
| **Anpassad karta/minska** | <ul><li>Det ger full kontroll över kartan och minska faser och utförande.</li><li>Det gör att frågor kan optimeras för att uppnå maximal prestanda från klustret, eller för att minimera belastningen på servrarna och nätverket.</li><li>Komponenterna kan skrivas på en rad välkända språk.</li></ul> | <ul><li>Det är svårare än att använda Pig eller Hive eftersom du måste skapa din egen karta och minska komponenter.</li><li>Processer som kräver att sammanfoga uppsättningar med data är svårare att implementera.</li><li>Även om det finns tillgängliga testramverk är felsökningskod mer komplex än ett normalt program eftersom koden körs som ett batch-jobb under kontroll av Hadoop-jobbschemaläggaren.</li></ul> |
| **Apache HCatalog** | <ul><li>Det abstraherar sökvägen detaljer om lagring, vilket gör administrationen lättare och ta bort behovet för användare att veta var data lagras.</li><li>Det möjliggör anmälan av händelser som datatillgänglighet, vilket gör att andra verktyg som Oozie kan upptäcka när åtgärder har inträffat.</li><li>Den visar en relationsvy av data, inklusive partitionering efter nyckel, och gör data lättillgängliga.</li></ul> | <ul><li>Den stöder RCFile, CSV text, JSON text, SequenceFile och ORC filformat som standard, men du kan behöva skriva en anpassad SerDe för andra format.</li><li>HCatalog är inte gängsäkert.</li><li>Det finns vissa begränsningar för datatyperna för kolumner när du använder HCatalog-inläsaren i Pig-skript. Mer information finns i [HCatLoader-datatyper](https://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) i Apache HCatalog-dokumentationen.</li></ul> |

Vanligtvis använder du den enklaste av dessa metoder som kan ge de resultat du behöver. Du kanske till exempel kan uppnå sådana resultat genom att bara använda Hive, men för mer komplexa scenarier kan du behöva använda Pig, eller till och med skriva din egen karta och minska komponenter. Du kan också bestämma, efter att ha experimenterat med Hive eller Pig, att anpassade karta och minska komponenter kan ge bättre prestanda genom att låta dig finjustera och optimera behandlingen.

## <a name="custom-mapreduce-components"></a>Anpassade kart-/reducerar komponenter

Kart/reduceringskod består av två separata funktioner som implementeras som **kart-** och **reduceringskomponenter.** **Kartkomponenten** körs parallellt på flera klusternoder, varje nod som tillämpar mappningen på nodens egen delmängd av data. Den **minska** komponenten sammanställer och sammanfattar resultaten från alla kartfunktioner. Mer information om dessa två komponenter finns [i Använd MapReduce i Hadoop på HDInsight](hdinsight-use-mapreduce.md).

I de flesta HDInsight-bearbetningsscenarier är det enklare och effektivare att använda en abstraktion på högre nivå som Pig eller Hive. Du kan också skapa anpassad karta och minska komponenter för användning i Hive-skript för att utföra mer sofistikerad bearbetning.

Anpassade kart-/reducerar-komponenter skrivs vanligtvis i Java. Hadoop tillhandahåller ett strömmande gränssnitt som också gör att komponenter kan användas som utvecklas på andra språk som C#, F#, Visual Basic, Python och JavaScript.

* En genomgång om hur du utvecklar anpassade Java MapReduce-program finns i [Utveckla Java MapReduce-program för Hadoop på HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

Överväg att skapa en egen karta och minska komponenter för följande villkor:

* Du måste bearbeta data som är helt ostrukturerade genom att tolka data och använda anpassad logik för att hämta strukturerad information från den.
* Du vill utföra komplexa uppgifter som är svåra (eller omöjliga) att uttrycka i Pig eller Hive utan att tillgripa att skapa en UDF. Du kan till exempel behöva använda en extern geokodningstjänst för att konvertera latitud- och longitudkoordinater eller IP-adresser i källdata till geografiska platsnamn.
* Du vill återanvända din befintliga .NET-, Python- eller JavaScript-kod i map/reduce-komponenter med hjälp av Hadoop-direktuppspelningsgränssnittet.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Ladda upp och kör ditt anpassade MapReduce-program

De vanligaste MapReduce-programmen är skrivna i Java och kompilerade till en jar-fil.

1. När du har utvecklat, kompilerat och testat mapreduce-programmet använder du `scp` kommandot för att ladda upp jar-filen till headnode.

    ```cmd
    scp mycustomprogram.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Ersätt CLUSTERNAME med klusternamnet. Om du använde ett lösenord för att skydda SSH-kontot uppmanas du att ange lösenordet. Om du har använt ett certifikat `-i` kan du behöva använda parametern för att ange den privata nyckelfilen.

1. Använd kommandot ssh för att ansluta till [klustret.](../hdinsight-hadoop-linux-use-ssh-unix.md) Redigera kommandot nedan genom att ersätta CLUSTERNAME med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Från SSH-sessionen kör du ditt MapReduce-program via YARN.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    Det här kommandot skickar MapReduce-jobbet till YARN. Indatafilen `/example/data/sample.log`är och utdatakatalogen är `/example/data/logoutput`. Indatafilen och eventuella utdatafiler lagras i standardlagringen för klustret.

## <a name="next-steps"></a>Nästa steg

* [Använd C# med MapReduce streaming på Apache Hadoop i HDInsight](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Utveckla Java MapReduce-program för Apache Hadoop i HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Använd Azure Toolkit för Eclipse för att skapa Apache Spark-program för ett HDInsight-kluster](../spark/apache-spark-eclipse-tool-plugin.md)
* [Använd Python User Defined Functions (UDF) med Apache Hive och Apache Pig i HDInsight](python-udf-hdinsight.md)
