---
title: Använda en Windows-dator med Hadoop i HDInsight - Azure
description: Arbeta i en Windows-dator i Hadoop på HDInsight. Hantera och fråga kluster med PowerShell, Visual Studio och Linux-verktyg. Utveckla big data-lösningar med .NET.
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/24/2019
ms.openlocfilehash: 5045c48a00c51a16d37dcf4b7f72f25633f23b3f
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926034"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Arbeta i Apache Hadoop-ekosystemet i HDInsight från en Windows-dator

Läs mer om utveckling och hanteringsalternativ på Windows-dator för att arbeta i Apache Hadoop-ekosystemet i HDInsight. 

HDInsight bygger på Apache Hadoop- och Hadoop-komponenter, tekniker för öppen källkod som utvecklas för Linux. HDInsight version 3.4 och senare använder Ubuntu Linux-distribution som det underliggande Operativsystemet för klustret. Dock kan du arbeta med HDInsight från en Windows-klienten eller Windows-utvecklingsmiljö.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Använda PowerShell för distribution och hantering av uppgifter
Azure PowerShell är en skriptmiljö som du kan använda för att styra och automatisera distributionen och hanteringsuppgifter i HDInsight från Windows.

Exempel på uppgifter som du kan göra med PowerShell:

* [Skapa kluster med hjälp av PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).
* [Kör Apache Hive-frågor med hjälp av PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).
* [Hantera kluster med PowerShell](hdinsight-administer-use-powershell.md).

Följ stegen för att [installera och konfigurera Azure Powershell](https://docs.microsoft.com/powershell/azure/install-az-ps) att hämta den senaste versionen.

## <a name="utilities-you-can-run-in-a-browser"></a>Verktyg som du kan köra i en webbläsare
Följande verktyg har ett webbgränssnitt som körs i en webbläsare:
* **[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)**  är en interaktiv, kommandorads-gränssnitt som körs i webbläsaren och inifrån Azure-portalen.
* **[Apache Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md)**  är en övervaknings- och verktyg som är tillgängliga i Azure portal som kan användas för att hantera olika typer av jobb, som:
    * [Använd Apache Ambari med REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Apache Hive-vyn i Apache Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Apache Tez vyn i Apache Ambari](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Data Lake (Hadoop)-verktyg för Visual Studio
Använda Data Lake Tools för Visual Studio för att distribuera och hantera Storm-topologier. Data Lake-verktyg installeras också SDK för SCP.NET, där du kan utveckla C# Storm-topologier med Visual Studio.

Innan du går till exemplet nedan [installera och testa Data Lake Tools för Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md). 

Exempel på uppgifter som du kan göra med Visual Studio och Data Lake Tools för Visual Studio:
* [Distribuera och hantera Storm-topologier från Visual Studio](storm/apache-storm-deploy-monitor-topology-linux.md)
* [Utveckla C#-topologier för Storm med Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md). Bits innehåller exempel på mallar för Storm-topologier som du kan ansluta till databaser, till exempel Azure Cosmos DB och SQL-databas.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio och .NET SDK 

Du kan använda Visual Studio med .NET SDK för att hantera kluster och utveckla big data-program. Du kan använda andra IDE: er för följande uppgifter, men exempel visas i Visual Studio.

Exempel på uppgifter som du kan göra med .NET SDK i Visual Studio:
* [Skapa kluster och fungerar med HDInsight från .NET Framework-program](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md).
* [Kör Apache Hive-frågor med .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).
* [Använd C# användardefinierade funktioner med Apache Hive och Apache Pig strömning på Apache Hadoop](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Intellij IDEA och Eclipse IDE för Spark-kluster
Båda [Intellij IDEA](https://www.jetbrains.com/idea/download) och [Eclipse IDE](https://www.eclipse.org/downloads/) kan användas för att:
* Utveckla och skicka in ett Scala Spark-program på ett HDInsight Spark-kluster.
* Åtkomst till Spark-klusterresurser.
* Utveckla och kör ett Scala Spark-program lokalt.

Dessa artiklar visar hur: 
* Intellij IDEA: [Skapa Apache Spark-program med hjälp av Azure Toolkit för Intellij plugin-programmet och Scala-SDK.](spark/apache-spark-intellij-tool-plugin.md)
* Eclipse IDE eller Scala IDE för Eclipse: [Skapa Apache Spark-program och Azure Toolkit för Eclipse](spark/apache-spark-eclipse-tool-plugin.md) 


## <a name="notebooks-on-spark-for-data-scientists"></a>Anteckningsböcker på Spark för dataexperter 
Apache Spark-kluster i HDInsight innefattar Apache Zeppelin-anteckningsböcker och kernlar som du kan använda med Jupyter-anteckningsböcker. 

* [Lär dig att använda kernlar i Apache Spark-kluster med Jupyter-anteckningsböcker för att testa Spark-program](spark/apache-spark-zeppelin-notebook.md)
* [Lär dig hur du använder Apache Zeppelin-anteckningsböcker i Apache Spark-kluster för att köra Spark-jobb](spark/apache-spark-jupyter-notebook-kernels.md) 

## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Kör Linux-baserade verktyg och tekniker i Windows

Om det uppstår en situation där du måste använda ett verktyg eller en teknik som endast är tillgängligt i Linux, Överväg följande alternativ:

* **Bash i Ubuntu för Windows 10** ger ett undersystem för Linux på Windows. Bash kan du direkt köra Linux-verktyg utan att behöva underhålla en dedikerad Linux-installation. Se [Windows-undersystem for Linux Installation Guide för Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) för installationssteg.  Andra [Unix gränssnitt](https://www.gnu.org/software/bash/) fungerar också.
* **Docker för Windows** ger åtkomst till många Linux-baserade verktyg och kan köras direkt från Windows. Du kan till exempel använda Docker för att köra klienten Beeline för Hive direkt från Windows. Du kan också använda Docker för att köra en lokal Jupyter notebook och fjärransluta till Spark i HDInsight. [Kom igång med Docker för Windows](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](https://mobaxterm.mobatek.net/)**  kan du bläddra grafiskt filsystemet klustret via en SSH-anslutning.

## <a name="cross-platform-tools"></a>Plattformsoberoende verktyg

Kommandoradsgränssnittet för Azure (CLI) är Microsofts plattformsoberoende kommandoradsmiljö för att hantera Azure-resurser.  Mer information finns i [Azure kommandoradsgränssnitt (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="next-steps"></a>Nästa steg
Om du inte har arbetat i Linux-baserade kluster finns i följande artiklar:
* [Konfigurera Apache Hadoop, Apache Kafka, Apache Spark eller andra kluster](hdinsight-hadoop-provision-linux-clusters.md)
* [Tips för HDInsight-kluster på Linux](hdinsight-hadoop-linux-information.md)
