---
title: Installera och använda Giraph på Hadoop-kluster i HDInsight - Azure | Microsoft Docs
description: Lär dig hur du anpassar HDInsight-kluster med Giraph och hur du använder Giraph.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 77a1d0e0-55de-4e61-98a0-060914fb7ca0
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: df7c5e175ecfd2eca2b1db7eb4ab056bb1588dda
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="install-and-use-giraph-on-windows-based-hdinsight-clusters"></a>Installera och använda Giraph på Windows-baserade HDInsight-kluster

Lär dig hur du anpassar Windows-baserat HDInsight-kluster med Giraph med skriptåtgärder och hur du använder Giraph för att bearbeta stora diagram. Information om hur du använder Giraph med ett Linux-baserade kluster finns i [installera Giraph på HDInsight Hadoop-kluster (Linux)](hdinsight-hadoop-giraph-install-linux.md).

> [!IMPORTANT]
> Stegen i det här dokumentet fungerar endast med Windows-baserade HDInsight-kluster. HDInsight är endast tillgängligt i Windows för versioner som är lägre än HDInsight 3.4. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Information om hur du installerar Giraph på en Linux-baserade HDInsight-kluster finns i [installera Giraph på HDInsight Hadoop-kluster (Linux)](hdinsight-hadoop-giraph-install-linux.md).


Du kan installera Giraph på någon typ av kluster (Hadoop, Storm, HBase, Spark) på Azure HDInsight med hjälp av *skriptåtgärd*. Ett exempelskript för att installera Giraph på ett HDInsight-kluster är tillgänglig från en skrivskyddad Azure storage blob [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). Exempelskriptet fungerar bara med HDInsight-kluster av version 3.1. Mer information om HDInsight-kluster-versioner finns [HDInsight-kluster-versioner](hdinsight-component-versioning.md).

**Relaterade artiklar**

* [Installera Giraph på HDInsight Hadoop-kluster (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [Skapa Hadoop-kluster i HDInsight](hdinsight-provision-clusters.md): allmän information om hur du skapar HDInsight-kluster.
* [Anpassa HDInsight-kluster med skriptåtgärder][hdinsight-cluster-customize]: allmän information om hur du anpassar HDInsight-kluster med skriptåtgärder.
* [Utveckla skriptåtgärd skript för HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-giraph"></a>Vad är Giraph?
<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> kan du utföra bearbetning med hjälp av Hadoop och kan användas med Azure HDInsight. Diagram modell relationer mellan objekt, till exempel anslutningar mellan routrar i ett större nätverk som Internet eller relationer mellan personer på sociala nätverk (kallas ibland ett sociala diagram). Diagrammet bearbetning kan du därför om förhållanden mellan objekt i ett diagram som:

* Identifiera potentiella vänner baserat på din aktuella relationer.
* Identifiera den kortaste vägen mellan två datorer i ett nätverk.
* Beräkning av sidan rangordningen för webbsidor.

## <a name="install-giraph-using-portal"></a>Installera Giraph med hjälp av portalen
1. Skapa ett kluster med hjälp av den **skapa anpassade** alternativ, enligt beskrivningen i [skapa Hadoop-kluster i HDInsight](hdinsight-provision-clusters.md).
2. På den **skriptåtgärder** sidan i guiden klickar du på **lägga till skriptåtgärd** att ge information om skriptåtgärd, enligt nedan:

    ![Använd skriptåtgärder för att anpassa ett kluster](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "Använd skriptåtgärder att anpassa ett kluster")

    <table border='1'>
        <tr><th>Egenskap</th><th>Värde</th></tr>
        <tr><td>Namn</td>
            <td>Ange ett namn för skriptåtgärden. Till exempel <b>installera Giraph</b>.</td></tr>
        <tr><td>Skript-URI</td>
            <td>Ange den identifierare URI (Uniform Resource) till det skript som anropas för att anpassa klustret. Till exempel <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
        <tr><td>Nodtyp</td>
            <td>Ange de noder som anpassning skriptet körs. Du kan välja <b>alla noder</b>, <b>huvudnoder endast</b>, eller <b>arbetarnoder endast</b>.
        <tr><td>Parametrar</td>
            <td>Ange parametrar, om det krävs av skriptet. Skript för att installera Giraph kräver inte parametrar, så du kan lämna den tom.</td></tr>
    </table>

    Du kan lägga till fler än en skriptåtgärd för att installera flera komponenter i klustret. När du har lagt till skripten, klicka på bockmarkeringen om du vill skapa klustret.

## <a name="use-giraph"></a>Använd Giraph
Vi använder SimpleShortestPathsComputation-exempel för att demonstrera grundläggande <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> implementering för att hitta den kortaste sökvägen mellan objekt i ett diagram. Använd följande steg för att ladda upp exempeldata och exempel jar genom att köra ett jobb med hjälp av SimpleShortestPathsComputation exempel och visa sedan resultaten.

1. Överför en exempeldatafil till Azure Blob storage. Skapa en ny fil med namnet på din lokala arbetsstation **tiny_graph.txt**. Den bör innehålla följande rader:

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Överför filen tiny_graph.txt till den primära lagringsplatsen för ditt HDInsight-kluster. Instruktioner om hur du överför data finns [överföra data för Hadoop-jobb i HDInsight](hdinsight-upload-data.md).

    Informationen beskriver en relation mellan objekt i en riktat diagram i formatet [källa\_id, källa\_värde [[målet\_id], [kant\_värde],...]]. Varje rad representerar en relation mellan en **källa\_id** objekt och en eller flera **dest\_id** objekt. Den **kant\_värdet** (eller vikt) kan betraktas som styrkan eller avståndet mellan anslutningen mellan **source_id** och **dest\_id**.

    Dras ut, och använder värdet (eller vikt) som avståndet mellan objekt, informationen som beskrivs ovan kan se ut så här:

    ![tiny_graph.txt ritas som cirklar med rader med olika avståndet mellan](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)
2. Kör SimpleShortestPathsComputation exemplet. Använd följande Azure PowerShell-cmdlets för att köra exemplet med hjälp av filen tiny_graph.txt som indata.

    > [!IMPORTANT]
    > Azure PowerShell-stöd för hantering av HDInsight-resurser med hjälp av Azure Service Manager **är föråldrat** och togs bort den 1 januari 2017. I stegen i det här dokumentet används de nya HDInsight-cmdletarna som fungerar med Azure Resource Manager.
    >
    > Följ stegen i [Installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs) för att installera den senaste versionen av Azure PowerShell. Om du har skript som behöver ändras för att använda de nya cmdletarna som fungerar med Azure Resource Manager, hittar du mer information i [Migrera till Azure Resource Manager-baserade utvecklingsverktyg för HDInsight-kluster](hdinsight-hadoop-development-using-azure-resource-manager.md).

    ```powershell
    $clusterName = "clustername"
    # Giraph examples jar
    $jarFile = "wasb:///example/jars/giraph-examples.jar"
    # Arguments for this job
    $jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
                    "-ca", "mapred.job.tracker=headnodehost:9010",
                    "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
                    "-vip", "wasb:///example/data/tiny_graph.txt",
                    "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
                    "-op",  "wasb:///example/output/shortestpaths",
                    "-w", "2"
    # Create the definition
    $jobDefinition = New-AzureHDInsightMapReduceJobDefinition
        -JarFile $jarFile
        -ClassName "org.apache.giraph.GiraphRunner"
        -Arguments $jobArguments

    # Run the job, write output to the Azure PowerShell window
    $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureHDInsightJob -Job $job
    Write-Host "STDERR"
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput
    ```

    I exemplet ovan ersätter **klusternamn** med namnet på ditt HDInsight-kluster som har Giraph installerad.
3. Granska resultaten. När jobbet har slutförts resultatet kommer att lagras i två utgående filer i den **wasb: / / / exempel/out/shotestpaths** mapp. Filerna kallas **del-m-00001** och **del-m-00002**. Utför följande steg för att hämta och visa utdata:

    ```powershell
    $subscriptionName = "<SubscriptionName>"       # Azure subscription name
    $storageAccountName = "<StorageAccountName>"   # Azure Storage account name
    $containerName = "<ContainerName>"             # Blob storage container name

    # Select the current subscription
    Select-AzureSubscription $subscriptionName

    # Create the Storage account context object
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    # Download the job output to the workstation
    Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
    Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force
    ```

    Detta skapar den **shortestpaths-exempel/utdata** katalogstruktur i aktuell katalog på din arbetsstation och hämta två utdatafilerna till den platsen.

    Använd den **Cat** för att visa innehållet i filerna:

        Cat example/output/shortestpaths/part*

    Resultatet bör likna följande:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    SimpleShortestPathComputation exempel är hårddisken kodat börja med objekt-ID 1 och hitta den kortaste sökvägen till andra objekt. Så att utdata ska läsas som `destination_id distance`, där avstånd är värdet (eller vikt) av kanter förflyttat sig mellan objekt-ID 1 och mål-ID.

    Visualisera detta kan du kontrollera resultatet av reser kortaste sökvägar mellan ID 1 och alla andra objekt. Observera att den kortaste sökvägen mellan ID 1 och 4-ID är 5. Detta är det totala avståndet mellan <span style="color:orange">ID 1 och 3</span>, och sedan <span style="color:red">ID 3 och 4</span>.

    ![Ritning av objekt som cirklar med kortast sökvägar mellan](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## <a name="install-giraph-using-aure-powershell"></a>Installera Giraph med Aure PowerShell
Se [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  Exemplet visar hur du installerar Spark med hjälp av Azure PowerShell. Du måste anpassa skript om du vill använda [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="install-giraph-using-net-sdk"></a>Installera Giraph med .NET SDK
Se [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). Exemplet visar hur du installerar Spark med .NET SDK. Du måste anpassa skript om du vill använda [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="see-also"></a>Se också
* [Installera Giraph på HDInsight Hadoop-kluster (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [Skapa Hadoop-kluster i HDInsight](hdinsight-provision-clusters.md): allmän information om hur du skapar HDInsight-kluster.
* [Anpassa HDInsight-kluster med skriptåtgärder][hdinsight-cluster-customize]: allmän information om hur du anpassar HDInsight-kluster med skriptåtgärder.
* [Utveckla skriptåtgärd skript för HDInsight](hdinsight-hadoop-script-actions.md).
* [Installera och använda Spark på HDInsight-kluster][hdinsight-install-spark]: skriptåtgärder exempel om hur du installerar Spark.
* [Installera R på HDInsight-kluster][hdinsight-install-r]: skriptåtgärder exempel om hur du installerar R.
* [Installera Solr på HDInsight-kluster](hdinsight-hadoop-solr-install.md): skriptåtgärder exempel om hur du installerar Solr.

[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
