---
title: "Använda .NET med Hadoop-MapReduce på Linux-baserade HDInsight - Azure | Microsoft Docs"
description: "Lär dig hur du använder .NET-program för strömning MapReduce på Linux-baserade HDInsight."
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/04/2017
ms.author: larryfr
ms.openlocfilehash: df931d0f76498506cfc946501e4d385d0dfead80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Migrera .NET lösningar för Windows-baserade HDInsight på Linux-baserat HDInsight

Linux-baserade HDInsight-kluster Använd [Mono (https://mono-project.com)](https://mono-project.com) att köra .NET-program. Mono kan du använda .NET-komponenter, till exempel MapReduce program med Linux-baserade HDInsight. Lär dig hur du migrerar .NET-lösningar som skapats för Windows-baserade HDInsight-kluster att arbeta med Mono på Linux-baserade HDInsight i det här dokumentet.

## <a name="mono-compatibility-with-net"></a>Monoljud kompatibilitet med .NET

Monoljud version 4.2.1 ingår i HDInsight version 3.5. Mer information om versionen av Mono som ingår i HDInsight finns [HDInsight komponenten versioner](hdinsight-component-versioning.md). Om du vill installera en viss version av Mono den [installera eller uppdatera Mono](hdinsight-hadoop-install-mono.md) dokumentet.

Mer information om kompatibilitet mellan Mono och .NET finns i [monoljud kompatibilitet (http://www.mono-project.com/docs/about-mono/compatibility/)](http://www.mono-project.com/docs/about-mono/compatibility/) dokumentet.

> [!IMPORTANT]
> SCP.NET framework är kompatibel med Mono. Mer information om hur du använder SCP.NET med Mono finns [använda Visual Studio för att utveckla C#-topologier för Apache Storm på HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Automatisk överföring analys

Den [.NET Portability Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) kan användas för att generera en rapport över inkompatibilitet mellan programmet och Mono. Använd följande steg för att konfigurera analyzer för att kontrollera att programmet monoljud portability:

1. Installera den [.NET Portability Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). Välj versionen av Visual Studio för att använda under installationen.

2. Visual Studio 2015, Välj __analysera__ > __Portability Analyzer inställningar__, och se till att __4.5__ checkas in den __Mono__ avsnitt.

    ![4.5 incheckad monoljud avsnittet analyzer-inställningar](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Välj __OK__ att spara konfigurationen.

3. Välj __analysera__ > __analysera sammansättningen Portability__. Välj den samling som innehåller din lösning och välj sedan __öppna__ att starta analys.

4. När analysen är klar, välja __analysera__ > __visa analysrapporter__. I __Portability analysresultat__väljer __öppna rapporten__ att öppna en rapport.

    ![Dialogrutan för portabilitet analyzer resultat](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]
> Analyzer kan inte fånga alla problem med din lösning. Till exempel en sökväg till filen för `c:\temp\file.txt` anses vara OK om Mono körs på Windows. Samma sökväg är inte giltig på en Linux-plattformen.

## <a name="manual-portability-analysis"></a>Manuell överföring analys

Utföra en manuell granskning av din kod med hjälp av informationen i den [programmet Portability (http://www.mono-project.com/docs/getting-started/application-portability/)](http://www.mono-project.com/docs/getting-started/application-portability/) dokumentet.

## <a name="modify-and-build"></a>Ändra och skapa

Du kan fortsätta att använda Visual Studio för att skapa dina .NET-lösningar för HDInsight. Dock måste du kontrollera att projektet är konfigurerad för att använda .NET Framework 4.5.

## <a name="deploy-and-test"></a>Distribuera och testa

När du har ändrat din lösning med hjälp av rekommendationerna från .NET Portability Analyzer eller en manuell analys, måste du testa den med HDInsight. Testa lösningen på ett Linux-baserade HDInsight-kluster kan du få problem som behöver åtgärdas. Vi rekommenderar att du aktiverar ytterligare loggning i ditt program testar det.

Mer information om hur du använder loggar finns i följande dokument:

* [Få åtkomst till YARN-programloggar i Linux-baserat HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Nästa steg

* [Använda C# med MapReduce på HDInsight](hdinsight-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Använda C# användardefinierade funktioner med Hive och Pig](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Utveckla C#-topologier för Storm på HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md)