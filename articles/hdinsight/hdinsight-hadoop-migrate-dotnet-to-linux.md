---
title: Använda .NET med Hadoop MapReduce på Linux-baserade HDInsight - Azure
description: Lär dig hur du använder .NET-program för strömning MapReduce på Linux-baserade HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: adab50b7325be96830ee937153d110754cc0b552
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549805"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Migrera .NET-lösningar för Windows-baserade HDInsight till Linux-baserat HDInsight

Linux-baserade HDInsight-kluster används [Mono (https://mono-project.com) ](https://mono-project.com) att köra .NET-program. Mono kan du använda .NET-komponenter som MapReduce-program med Linux-baserade HDInsight. I det här dokumentet lär du dig hur du migrerar .NET-lösningar som skapats för Windows-baserade HDInsight-kluster att arbeta med Mono på Linux-baserade HDInsight.

## <a name="mono-compatibility-with-net"></a>Mono-kompatibilitet med .NET

Mono version 4.2.1 medföljer HDInsight version 3.6. Läs mer på vilken version av Mono som medföljer HDInsight [HDInsight komponenten versioner](hdinsight-component-versioning.md).

Mer information om kompatibilitet mellan Mono och .NET finns i den [Mono-kompatibilitet (https://www.mono-project.com/docs/about-mono/compatibility/) ](https://www.mono-project.com/docs/about-mono/compatibility/) dokumentet.

> [!IMPORTANT]  
> SCP.NET-ramverket är kompatibel med Mono. Läs mer om hur du använder SCP.NET med Mono [Använd Visual Studio för att utveckla C#-topologier för Apache Storm på HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Automatiserad portabilitet analys

Den [.NET portabilitet Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) kan användas för att generera en rapport över inkompatibiliteter mellan programmet och Mono. Använd följande steg för att konfigurera analyzer för att kontrollera ditt program för Mono portabilitet:

1. Installera den [.NET portabilitet Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). Välj versionen av Visual Studio för att använda under installationen.

2. Från Visual Studio 2015 väljer __analysera__ > __portabilitet Analyzer inställningar__, och se till att __4.5__ har checkats in den __Mono__ avsnittet.

    ![4.5 incheckad Mono avsnittet analyzer-inställningar](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Välj __OK__ att spara konfigurationen.

3. Välj __analysera__ > __analysera sammansättningen portabilitet__. Välj den samling som innehåller din lösning och välj sedan __öppna__ att starta analys.

4. När analysen är klar, väljer __analysera__ > __visa analysrapporter__. I __portabilitet analysresultat__väljer __Öppna rapport__ att öppna en rapport.

    ![Portabilitet analyzer resultat dialogrutan](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]  
> Analysatorn identifierar inte alla problem med din lösning. Till exempel en sökväg för `c:\temp\file.txt` anses vara OK om Mono körs på Windows. Samma sökväg är inte giltig på en Linux-plattformen.

## <a name="manual-portability-analysis"></a>Manuell portabilitet analys

Utföra en manuell granskning av din kod med hjälp av informationen i den [Programportabilitet (https://www.mono-project.com/docs/getting-started/application-portability/) ](https://www.mono-project.com/docs/getting-started/application-portability/) dokumentet.

## <a name="modify-and-build"></a>Ändra och skapa

Du kan fortsätta att använda Visual Studio för att skapa dina .NET-lösningar för HDInsight. Du måste dock se till att projektet är konfigurerad för att använda .NET Framework 4.5.

## <a name="deploy-and-test"></a>Distribuera och testa

När du har ändrat din lösning med rekommendationerna från .NET portabilitet Analyzer eller från en manuell analys kan testa du den med HDInsight. Testa lösningen på ett Linux-baserade HDInsight-kluster kan avslöja diskret problem som behöver korrigeras. Vi rekommenderar att du aktivera ytterligare loggning i programmet testar det.

Mer information om hur du använder loggar finns i följande dokument:

* [Åtkomst Apache Hadoop YARN-programloggar på Linux-baserat HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Nästa steg

* [Använda C# med MapReduce på HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Använd C# användardefinierade funktioner med Apache Hive och Apache Pig](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Utveckla C# topologier för Apache Storm på HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
