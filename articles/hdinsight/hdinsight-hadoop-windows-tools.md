---
title: "Använda en Windows-dator med Hadoop i HDInsight - Azure | Microsoft Docs"
description: "Arbeta från en Windows-dator i Hadoop på HDInsight. Hantera och fråga kluster med PowerShell-, Visual Studio- och Linux-verktyg. Utveckla stordatalösningar med .NET."
services: hdinsight
keywords: "hadoop på windows, hadoop för windows"
author: cjgronlund
manager: jhubbard
ms.author: cgronlun
ms.date: 05/17/2017
ms.topic: article
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.openlocfilehash: e4f231c1f9b903d6cc7f2b062b30d2a072be8493
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="work-in-the-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Fungera med Hadoop-ekosystemet i HDInsight från en Windows-dator

Läs mer om utveckling och hantering av alternativen på Windows-dator för att arbeta i Hadoop-ekosystemet i HDInsight. 

HDInsight är baserad på Apache Hadoop och Hadoop-komponenter, öppen källkod tekniker som utvecklats på Linux. HDInsight version 3.4 och högre använder Ubuntu Linux-distribution som det underliggande Operativsystemet för klustret. Du kan dock arbeta med HDInsight från en Windows-klienten eller Windows-utvecklingsmiljö.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Använda PowerShell för distribution och hantering av uppgifter
Azure PowerShell är en skriptmiljö som du kan använda för att styra och automatisera distributionen och hanteringsuppgifter i HDInsight från Windows.

Exempel på uppgifter som du kan göra med PowerShell:

* [Skapa kluster med hjälp av PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Köra Hive-frågor med hjälp av PowerShell](hdinsight-hadoop-use-hive-powershell.md)
* [Hantera kluster med PowerShell](hdinsight-administer-use-powershell.md)

Följ stegen för att [installera och konfigurera Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) att hämta den senaste versionen. Om du har skript som behöver ändras för att använda de nya cmdletarna för Azure Resource Manager finns [migrera till Azure Resource Manager-baserade utvecklingsverktyg för HDInsight-kluster](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="utilities-you-can-run-in-a-browser"></a>Verktyg som du kan köra i en webbläsare
Följande verktyg har ett webbgränssnitt som körs i en webbläsare:
* **[Azure Cloud Shell (förhandsgranskning)](https://docs.microsoft.com/azure/cloud-shell/quickstart)**  är en interaktiv, kommandoradsverktyg skal som körs i webbläsaren och från Azure-portalen.
* **[Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md)**  är en hantering och övervakning av verktyg som är tillgängliga i Azure-portalen som kan användas för att hantera olika typer av jobb, som:
    * [Använda Ambari med REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Hive-vyn i Ambari](hdinsight-hadoop-use-hive-ambari-view.md)
    * [Tez-vyn i Ambari](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Data Lake (Hadoop)-verktyg för Visual Studio
Använd Data Lake-verktyg för Visual Studio för att distribuera och hantera Storm-topologier. Data Lake-verktyg installeras också SCP.NET SDK, som gör att du kan utveckla C# Storm-topologier med Visual Studio.

Innan du går vidare i följande exempel [installera och försök Data Lake-verktyg för Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md). 

Exempel på uppgifter som du kan göra med Visual Studio och Data Lake-verktyg för Visual Studio:
* [Distribuera och hantera Storm-topologier från Visual Studio](hdinsight-storm-deploy-monitor-topology-linux.md)
* [Utveckla C#-topologier för Storm med Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md). Bits innehåller exempel mallar för Storm-topologier som du kan ansluta till databaser, till exempel Azure Cosmos-databas och SQL-databas.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio och .NET SDK 

Du kan använda Visual Studio med .NET SDK för att hantera kluster och utveckla stordataprogram. Du kan använda andra IDEs för följande uppgifter, men exempel visas i Visual Studio.

Exempel på uppgifter som du kan göra med .NET SDK i Visual Studio:
* [Skapa kluster och arbeta i HDInsight från .NET Framework-program](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* [Köra Hive-frågor med .NET SDK](hdinsight-hadoop-use-hive-dotnet-sdk.md)
* [Använda C# användardefinierade funktioner med Hive och Pig strömning på Hadoop](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

> Tips om du kör .NET lösningar med Windows-baserade HDInsight-kluster, är det dags att planera en migrering till Linux-baserade kluster. Mer information finns i [migrera .NET lösning för Windows-baserade HDInsight till Linux-baserade HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Intellij IDEA och Eclipse IDE för Spark-kluster
Båda [Intellij IDEA](https://www.jetbrains.com/idea/download) och [Eclipse IDE](https://www.eclipse.org/downloads/) användas för att:
* Utveckla och skicka Scala Spark-program på ett HDInsight Spark-kluster.
* Få åtkomst till Spark-kluster.
* Utveckla och köra ett Scala Spark-program lokalt.

Dessa artiklar visa hur: 
* Intellij IDEA: [skapa Spark-program med hjälp av Azure-verktygen för plugin-programmet Intellij och Scala-SDK.](hdinsight-apache-spark-intellij-tool-plugin.md)
* IDE- eller Scala IDE för Eclipse-solförmörkelse: [skapa Spark-program och Azure-verktygen för Eclipse](hdinsight-apache-spark-eclipse-tool-plugin.md) 


## <a name="notebooks-on-spark-for-data-scientists"></a>Datorer på Spark för datavetare 
Apache Spark-kluster i HDInsight innehåller Zeppelin-anteckningsböcker och kernlar som kan användas med Jupyter-anteckningsböcker. 

* [Lär dig hur du använder kärnor på Spark-kluster med Jupyter-anteckningsböcker för att testa Spark-program](hdinsight-apache-spark-zeppelin-notebook.md)
* [Lär dig använda Zeppelin-anteckningsböcker på Spark-kluster och köra Spark jobb](hdinsight-apache-spark-jupyter-notebook-kernels.md) 


## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Kör Linux-baserat verktyg och tekniker i Windows

Om du stöter på en situation där du måste använda ett verktyg eller en teknik som endast är tillgängliga på Linux, Överväg följande alternativ:

* **Bash (beta) i Windows 10** ger en Linux-undersystemet i Windows. Bash kan du direkt köra Linux verktyg utan att behöva underhålla en dedikerad Linux-installation. [Installera och köra Bash beta på Windows 10](https://msdn.microsoft.com/commandline/wsl/install_guide)
* **Docker för Windows** ger tillgång till många Linux-baserat verktyg och kan köras direkt från Windows. Du kan till exempel använda Docker för att köra klienten Beeline för Hive direkt från Windows. Du kan också använda Docker för att köra en lokal Jupyter-anteckningsbok och fjärransluta till Spark i HDInsight. [Kom igång med Docker för Windows](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](http://mobaxterm.mobatek.net/)**  kan du bläddra grafiskt filsystemet klustret via en SSH-anslutning.

## <a name="next-steps"></a>Nästa steg
Om du har använt arbetar i Linux-baserade kluster finns i följande artiklar:
* [Konfigurera Hadoop, Kafka, Spark eller andra kluster](hdinsight-hadoop-provision-linux-clusters.md)
* [Tips för HDInsight-kluster på Linux](hdinsight-hadoop-linux-information.md)