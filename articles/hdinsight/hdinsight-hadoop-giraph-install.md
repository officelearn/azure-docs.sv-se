---
title: Installera och använda Giraph på Hadoop-kluster i HDInsight - Azure
description: Lär dig hur du anpassar HDInsight-kluster med Giraph och använda Giraph.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2016
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 3ee1cfaa9e5eb08b2fe6ee7d210dcb84a8c39d78
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53715332"
---
# <a name="install-and-use-apache-giraph-on-windows-based-hdinsight-clusters"></a>Installera och använda Apache Giraph på Windows-baserade HDInsight-kluster

Lär dig hur du anpassar Windows-baserat HDInsight-kluster med Apache Giraph med skriptåtgärd och använda Giraph för att bearbeta storskaliga diagram. Information om att använda Giraph med en Linux-baserat kluster finns i [installera Apache Giraph på HDInsight Hadoop-kluster (Linux)](hdinsight-hadoop-giraph-install-linux.md).

> [!IMPORTANT]  
> Stegen i det här dokumentet fungerar bara med Windows-baserade HDInsight-kluster. HDInsight är endast tillgänglig på Windows för versioner lägre än HDInsight 3.4. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Information om hur du installerar Giraph på ett Linux-baserade HDInsight-kluster finns i [installera Apache Giraph på HDInsight Hadoop-kluster (Linux)](hdinsight-hadoop-giraph-install-linux.md).


Du kan installera Giraph på någon typ av kluster (Hadoop-, Storm, HBase, Spark) i Azure HDInsight med hjälp av *skriptåtgärd*. Ett exempelskript för att installera Giraph på ett HDInsight-kluster finns i en skrivskyddad Azure storage blob [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). Exempelskriptet fungerar bara med HDInsight-kluster av version 3.1. Läs mer på HDInsight-klusterversioner [HDInsight-klusterversioner](hdinsight-component-versioning.md).

**Relaterade artiklar**

* [Installera Apache Giraph på HDInsight Hadoop-kluster (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [Skapa Apache Hadoop-kluster i HDInsight](hdinsight-provision-clusters.md): allmän information om hur du skapar HDInsight-kluster.
* [Anpassa HDInsight-kluster med skriptåtgärd][hdinsight-cluster-customize]: allmän information om hur du anpassar HDInsight-kluster med skriptåtgärd.
* [Utveckla skriptåtgärder skript för HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-giraph"></a>Vad är Giraph?
<a href="https://giraph.apache.org/" target="_blank">Apache Giraph</a> kan du utföra diagrambearbetning med hjälp av Hadoop och kan användas med Azure HDInsight. Diagram modell relationer mellan objekt, till exempel anslutningar mellan routrar på ett stort nätverk som Internet eller relationer mellan personer på sociala nätverk (kallas ibland ett socialt diagram). Bearbeta diagram gör att du att resonera kring relationerna mellan objekt i ett diagram som:

* Identifiera potentiella vänner utifrån dina aktuella relationer.
* Identifiera den kortaste vägen mellan två datorer i ett nätverk.
* Beräkna den sida rangordningen för webbsidor.

## <a name="install-giraph-using-portal"></a>Installera Giraph med hjälp av portalen
1. Börja skapa ett kluster med hjälp av den **skapa anpassade** enligt beskrivningen på [skapa Hadoop-kluster i HDInsight](hdinsight-provision-clusters.md).
2. På den **skriptåtgärder** sidan i guiden klickar du på **lägga till skriptåtgärd** att ge information om skriptåtgärd, enligt nedan:

    ![Använda skriptåtgärder för att anpassa ett kluster](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "Använd skriptåtgärder för att anpassa ett kluster")

    <table border='1'>
        <tr><th>Egenskap </th><th>Värde</th></tr>
        <tr><td>Namn</td>
            <td>Ange ett namn för skriptåtgärden. Till exempel <b>installera Giraph</b>.</td></tr>
        <tr><td>Skript-URI</td>
            <td>Ange det ID: T URI (Uniform Resource) till det skript som anropas om du vill anpassa klustret. Exempel: <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
        <tr><td>Nodtyp</td>
            <td>Ange de noder som anpassning skriptet körs. Du kan välja <b>alla noder</b>, <b>huvudnoder endast</b>, eller <b>arbetsnoder endast</b>.
        <tr><td>Parametrar</td>
            <td>Ange parametrar, om det krävs av skriptet. Skript för att installera Giraph kräver inte några parametrar, så du kan lämna det tomt.</td></tr>
    </table>

    Du kan lägga till fler än en skriptåtgärd för att installera flera komponenter i klustret. När du har lagt till skripten, klicka på bockmarkeringen för att skapa klustret.

## <a name="use-giraph"></a>Använd Giraph
Vi använder SimpleShortestPathsComputation-exempel för att demonstrera grundläggande <a href = "https://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> implementering för att hitta den kortaste vägen mellan objekt i ett diagram. Använd följande steg för att ladda upp exempeldata och exempel jar, köra ett jobb med hjälp av exemplet SimpleShortestPathsComputation och visa sedan resultaten.

1. Ladda upp en exempeldatafil till Azure Blob storage. Skapa en ny fil med namnet på den lokala arbetsstationen **tiny_graph.txt**. Den bör innehålla följande rader:

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Ladda upp filen tiny_graph.txt till den primära lagringen för ditt HDInsight-kluster. Anvisningar för hur du överför data finns i [överföra data för Apache Hadoop-jobb i HDInsight](hdinsight-upload-data.md).

    Den här informationen beskriver en relation mellan objekt i en riktad graf i formatet [källa\_-id, källa\_värde, [[dest\_id], [edge\_värde],...]]. Varje rad motsvarar en relation mellan en **källa\_id** objekt och en eller flera **dest\_id** objekt. Den **edge\_värdet** (eller vikt) kan betraktas som styrkan eller avståndet från anslutningen mellan **source_id** och **dest\_id**.

    Utdragen, och använder värdet (eller vikt) som avståndet mellan objekten kan informationen som beskrivs ovan kan se ut så här:

    ![tiny_graph.txt ritas som cirklar med varierande avståndet mellan rader](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)
2. Kör exemplet SimpleShortestPathsComputation. Använd följande Azure PowerShell-cmdletar för att köra exemplet med hjälp av tiny_graph.txt-filen som indata.

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

    I exemplet ovan ersätter **clustername** med namnet på ditt HDInsight-kluster med Giraph installerad.
3. Granska resultaten. När jobbet har slutförts resultaten lagras i två utdatafilerna i den **wasb: / / / exempel/out/shotestpaths** mapp. Filerna kallas **del-m-00001** och **del-m-00002**. Utför följande steg för att ladda ned och visa utdata:

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

    Detta skapar den **exempel/output/shortestpaths** katalogstruktur i den aktuella katalogen på din arbetsstation och hämta två utdatafilerna till den platsen.

    Använd den **Cat** cmdlet för att visa innehållet i filerna:

        Cat example/output/shortestpaths/part*

    Utdata bör se ut ungefär så här:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    SimpleShortestPathComputation exempel är svårt att börja med kodade objekt-ID 1 och hitta den kortaste vägen till andra objekt. Så att utdata ska läsas som `destination_id distance`, där avståndet är värdet (eller vikt) av kanter rest mellan objekt-ID 1 och mål-ID.

    Du kan visualisera detta för att kontrollera resultaten av resa kortaste sökvägar mellan ID 1 och alla andra objekt. Observera att den kortaste vägen mellan ID 1 och 4-ID är 5. Det här är totala avståndet mellan <span style="color:orange">ID 1 och 3</span>, och sedan <span style="color:red">ID 3 och 4</span>.

    ![Ritning av objekt som cirklar med kortaste sökvägar mellan](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## <a name="install-giraph-using-aure-powershell"></a>Installera Giraph med Aure PowerShell
Se [anpassa HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  Exemplet visar hur du installerar Apache Spark med Azure PowerShell. Du behöver anpassa skript om du vill använda [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="install-giraph-using-net-sdk"></a>Installera Giraph med .NET SDK
Se [anpassa HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). Exemplet visar hur du installerar Spark med .NET SDK. Du behöver anpassa skript om du vill använda [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="see-also"></a>Se också
* [Installera Apache Giraph på HDInsight Hadoop-kluster (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [Skapa Apache Hadoop-kluster i HDInsight](hdinsight-provision-clusters.md): allmän information om hur du skapar HDInsight-kluster.
* [Anpassa HDInsight-kluster med skriptåtgärd][hdinsight-cluster-customize]: allmän information om hur du anpassar HDInsight-kluster med skriptåtgärd.
* [Utveckla skriptåtgärder skript för HDInsight](hdinsight-hadoop-script-actions.md).
* [Installera och använda Apache Spark på HDInsight-kluster][hdinsight-install-spark]: Skriptexempel för en åtgärd om hur du installerar Spark.
* [Installera Apache Solr på HDInsight-kluster](hdinsight-hadoop-solr-install.md): Skriptexempel för en åtgärd om att installera Solr.

[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: https://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
