---
title: Använda en Windows-dator med Hadoop på HDInsight - Azure
description: Arbeta från en Windows-dator i Hadoop på HDInsight. Hantera och fråga kluster med PowerShell-, Visual Studio- och Linux-verktyg. Utveckla stordatalösningar med .NET.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/20/2019
ms.openlocfilehash: 3ec50acc693452fe73d929effcea98b12fc5ff8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933939"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Arbeta i Apache Hadoop-ekosystemet på HDInsight från en Windows-dator

Lär dig mer om utvecklings- och hanteringsalternativ på Windows-datorn för att arbeta i Apache Hadoop-ekosystemet på HDInsight.

HDInsight är baserad på Apache Hadoop och Hadoop komponenter, öppen källkod teknik som utvecklats på Linux. HDInsight version 3.4 och högre använder Ubuntu Linux-distributionen som underliggande operativsystem för klustret. Du kan dock arbeta med HDInsight från en Windows-klient eller Windows-utvecklingsmiljö.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Använda PowerShell för driftsättnings- och hanteringsuppgifter

Azure PowerShell är en skriptmiljö som du kan använda för att styra och automatisera distributions- och hanteringsuppgifter i HDInsight från Windows.

Exempel på uppgifter som du kan göra med PowerShell:

* [Skapa kluster med PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).
* [Kör Apache Hive-frågor med PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).
* [Hantera kluster med PowerShell](hdinsight-administer-use-powershell.md).

Följ stegen för att [installera och konfigurera Azure Powershell](https://docs.microsoft.com/powershell/azure/install-az-ps) för att få den senaste versionen.

## <a name="utilities-you-can-run-in-a-browser"></a>Verktyg som du kan köra i en webbläsare

Följande verktyg har ett webbgränssnitt som körs i en webbläsare:
* **[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)** är ett interaktivt kommandoradsskal som körs i din webbläsare och inifrån Azure-portalen.

* **[Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md)** är ett hanterings- och övervakningsverktyg som är tillgängligt i Azure-portalen och som kan användas för att hantera olika typer av jobb, till exempel:
    * [Använd Apache Ambari med REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Apache Hive Visa i Apache Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Apache Tez Visa i Apache Ambari](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Data Lake (Hadoop) Verktyg för Visual Studio

Använd DataSjöverktyg för Visual Studio för att distribuera och hantera Storm-topologier. Data Lake Tools installerar också SCP.NET SDK, som låter dig utveckla C# Storm topologier med Visual Studio.

Innan du går till följande exempel [installerar och provar du DataSjöverktyg för Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Exempel på uppgifter som du kan göra med Visual Studio och DataSjöverktyg för Visual Studio:
* [Distribuera och hantera Storm-topologier från Visual Studio](storm/apache-storm-deploy-monitor-topology-linux.md)
* [Utveckla C# topologier för Storm med Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md). Bitarna innehåller exempelmallar för Storm-topologier som du kan ansluta till databaser, till exempel Azure Cosmos DB och SQL Database.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio och .NET SDK

Du kan använda Visual Studio med .NET SDK för att hantera kluster och utveckla stordataprogram. Du kan använda andra ID:er för följande uppgifter, men exempel visas i Visual Studio.

Exempel på uppgifter som du kan göra med .NET SDK i Visual Studio:
* [Azure HDInsight SDK för .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).
* [Kör Apache Hive-frågor med .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).
* [Använd C# användardefinierade funktioner med Apache Hive och Apache Pig streaming på Apache Hadoop](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Intellij IDEA och Eclipse IDE för Spark kluster

Både [Intellij IDEA](https://www.jetbrains.com/idea/download) och [Eclipse IDE](https://www.eclipse.org/downloads/) kan användas för att:
* Utveckla och skicka in ett Scala Spark-program på ett HDInsight Spark-kluster.
* Åtkomst till Spark-klusterresurser.
* Utveckla och kör ett Scala Spark-program lokalt.

Dessa artiklar visar hur:
* Intellij IDEA: [Skapa Apache Spark-program med Azure Toolkit för Intellij plug-in och Scala SDK.](spark/apache-spark-intellij-tool-plugin.md)
* Eclipse IDE eller Scala IDE för Eclipse: [Skapa Apache Spark-program och Azure Toolkit för Eclipse](spark/apache-spark-eclipse-tool-plugin.md)

## <a name="notebooks-on-spark-for-data-scientists"></a>Anteckningsböcker på Spark för datavetare

Apache Spark kluster i HDInsight inkluderar Apache Zeppelin bärbara datorer och kärnor som kan användas med Jupyter bärbara datorer.

* [Lär dig hur du använder kärnor på Apache Spark-kluster med Jupyter-anteckningsböcker för att testa Spark-program](spark/apache-spark-zeppelin-notebook.md)
* [Lär dig hur du använder Apache Zeppelin-anteckningsböcker på Apache Spark-kluster för att köra Spark-jobb](spark/apache-spark-jupyter-notebook-kernels.md)

## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Kör Linux-baserade verktyg och tekniker i Windows

Om du stöter på en situation där du måste använda ett verktyg eller en teknik som bara är tillgänglig på Linux bör du överväga följande alternativ:

* **Bash på Ubuntu på Windows 10** ger ett Linux-delsystem på Windows. Bash kan du direkt köra Linux verktyg utan att behöva upprätthålla en dedikerad Linux-installation. Mer ominstallationssteg finns i [Installationsguiden för Windows Undersystem för Linux för Windows 10.](https://docs.microsoft.com/windows/wsl/install-win10)  Andra [Unix-skal](https://www.gnu.org/software/bash/) fungerar också.
* **Docker för Windows** ger tillgång till många Linux-baserade verktyg och kan köras direkt från Windows. Du kan till exempel använda Docker för att köra Beeline-klienten för Hive direkt från Windows. Du kan också använda Docker för att köra en lokal Jupyter-anteckningsbok och fjärransluta till Spark på HDInsight. [Komma igång med Docker för Windows](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](https://mobaxterm.mobatek.net/)** kan du grafiskt bläddra i klusterfilsystemet över en SSH-anslutning.

## <a name="cross-platform-tools"></a>Plattformsoberoende verktyg

Kommandoradsgränssnittet för Azure (CLI) är Microsofts plattformsoberoende kommandoradsmiljö för att hantera Azure-resurser.  Mer information finns i [AZURE Command-Line Interface (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="next-steps"></a>Nästa steg

Om du inte har använt linux-baserade kluster tidigare läser du följande artiklar:
* [Konfigurera Apache Hadoop, Apache Kafka, Apache Spark eller andra kluster](hdinsight-hadoop-provision-linux-clusters.md)
* [Tips för HDInsight-kluster på Linux](hdinsight-hadoop-linux-information.md)
