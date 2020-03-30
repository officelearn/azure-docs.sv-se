---
title: Använd .NET med Hadoop MapReduce på Linux-baserad HDInsight - Azure
description: Lär dig hur du använder .NET-program för streaming MapReduce på Linux-baserade HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 4b402975a151d26e8f335c07930274c156ac52fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272375"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Migrera .NET-lösningar för Windows-baserade HDInsight till Linux-baserade HDInsight

Linux-baserade HDInsight-kluster använder [Mono (https://mono-project.com) ](https://mono-project.com) för att köra .NET-program. Mono kan du använda .NET komponenter såsom MapReduce applikationer med Linux-baserade HDInsight. I det här dokumentet kan du lära dig hur du migrerar .NET-lösningar som skapats för Windows-baserade HDInsight-kluster för att arbeta med Mono på Linux-baserad HDInsight.

## <a name="mono-compatibility-with-net"></a>Monokompatibilitet med .NET

Mono version 4.2.1 ingår i HDInsight version 3.6. Mer information om versionen av Mono som ingår i HDInsight finns i [HDInsight-komponentversioner](hdinsight-component-versioning.md).

Mer information om kompatibilitet mellan Mono och .NET finns i [monokompatibilitetenhttps://www.mono-project.com/docs/about-mono/compatibility/) (dokumentet.](https://www.mono-project.com/docs/about-mono/compatibility/)

> [!IMPORTANT]  
> Det SCP.NET ramverket är kompatibelt med Mono. Mer information om hur du använder SCP.NET med Mono finns i [Använda Visual Studio för att utveckla C#-topologier för Apache Storm på HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Automatiserad portabilitetsanalys

[.NET Portability Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) kan användas för att generera en rapport om inkompatibiliteter mellan ditt program och Mono. Använd följande steg för att konfigurera analysatorn så att programmet kan kontrolleras för monoportabilitet:

1. Installera [.NET Portability Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). Under installationen väljer du den version av Visual Studio som ska användas.

2. Från Visual Studio 2015 väljer du __Analysera__ > __inställningar för portabilitetsanalysator__och se till att __4.5__ är markerat i avsnittet __Mono.__

    ![4.5 kontrollerat i Mono-sektionen för analysatorinställningarna](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Välj __OK__ för att spara konfigurationen.

3. Välj __Analysera__ > __Analysera monteringsportbarhet__. Välj den sammansättning som innehåller lösningen och välj sedan __Öppna__ för att påbörja analysen.

4. När analysen är klar väljer du__Analysrapporter för__ __Analysera__ > vy . I __Portabilitetsanalysresultat__väljer du __Öppna rapport__ för att öppna en rapport.

    ![Dialogrutan Resultatdialog för portabilitetsanalysator](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]  
> Analysatorn kan inte fånga alla problem med din lösning. En filsökväg för `c:\temp\file.txt` anses till exempel VARA OK om Mono körs i Windows. Samma sökväg är inte giltig på en Linux-plattform.

## <a name="manual-portability-analysis"></a>Manuell portabilitetsanalys

Utför en manuell granskning av koden med hjälp av informationen i [dokumentet Förhttps://www.mono-project.com/docs/getting-started/application-portability/) programportabilitet (](https://www.mono-project.com/docs/getting-started/application-portability/)

## <a name="modify-and-build"></a>Ändra och skapa

Du kan fortsätta att använda Visual Studio för att skapa dina .NET-lösningar för HDInsight. Du måste dock se till att projektet är konfigurerat för att använda .NET Framework 4.5.

## <a name="deploy-and-test"></a>Distribuera och testa

När du har ändrat din lösning med hjälp av rekommendationerna från .NET Portability Analyzer eller från en manuell analys måste du testa den med HDInsight. Testa lösningen på en Linux-baserad HDInsight kluster kan avslöja subtila problem som måste korrigeras. Vi rekommenderar att du aktiverar ytterligare inloggning i ditt program när du testar det.

Mer information om hur du kommer åt loggar finns i följande dokument:

* [Få tillgång till Apache Hadoop YARN-programloggar på Linux-baserade HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Nästa steg

* [Använd C# med MapReduce på HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Använd C# användardefinierade funktioner med Apache Hive och Apache Pig](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Utveckla C# topologier för Apache Storm på HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
