---
title: "Använd R i HDInsight för att anpassa kluster - Azure | Microsoft Docs"
description: "Lär dig att installera R med skriptåtgärder och använda R i HDInsight-kluster."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: be851270-afa5-4af0-a69e-2d343a4deeb7
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 5b9b793d49217acd9f0c6c518596a7afb5600d69
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>Installera och använda R på HDInsight Hadoop-kluster

Lär dig hur du anpassar Windows-baserade HDInsight-kluster med R med skriptåtgärder och hur du använder R på HDInsight-kluster. Den [HDInsight erbjudande](https://azure.microsoft.com/pricing/details/hdinsight/) innehåller R-Server som en del av ditt HDInsight-kluster. På så sätt kan R-skript för att köra distribuerade beräkningar med MapReduce och Spark. Mer information finns i [Komma igång med R Server på HDInsight](hdinsight-hadoop-r-server-get-started.md). Information om hur du använder R med ett Linux-baserade kluster finns i [installerar och använder R på HDinsight Hadoop-kluster (Linux)](hdinsight-hadoop-r-scripts-linux.md).

Du kan installera R på någon typ av kluster (Hadoop, Storm, HBase, Spark) på Azure HDInsight med hjälp av *skriptåtgärd*. Ett exempelskript för att installera R på ett HDInsight-kluster är tillgänglig från en skrivskyddad Azure storage blob [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

**Relaterade artiklar**

* [Installera och använda R på HDinsight Hadoop-kluster (Linux)](hdinsight-hadoop-r-scripts-linux.md)
* [Skapa Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md): allmän information om hur du skapar HDInsight-kluster
* [Anpassa HDInsight-kluster med skriptåtgärder][hdinsight-cluster-customize]: allmän information om hur du anpassar HDInsight-kluster med skriptåtgärder
* [Utveckla skriptåtgärd skript för HDInsight](hdinsight-hadoop-script-actions.md)

## <a name="what-is-r"></a>Vad är R?
Den <a href="http://www.r-project.org/" target="_blank">R-projekt för statistisk databehandling</a> är en öppen källa och -miljö för statistisk databehandling. R innehåller hundratals inbyggda statistiska funktioner och sin egen programmeringsspråk som kombinerar aspekter av funktions- och objektorienterad programmering. Det ger också omfattande grafiskt funktioner. R är den önskade Programmeringsmiljön mest professional statistiker och forskare i en mängd olika fält.

R är kompatibelt med Azure Blob Storage (WASB) så att data som lagras det bearbetas med R på HDInsight.  

## <a name="install-r"></a>Installera R
En [exempel på skript](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1) installera R på ett HDInsight kluster är tillgängliga från en skrivskyddad blobb i Azure Storage. Det här avsnittet innehåller instruktioner om hur du använder exempelskriptet när du skapar klustret med hjälp av Azure-portalen.

> [!NOTE]
> Exempelskript som introducerades med HDInsight-kluster av version 3.1. Mer information om HDInsight-kluster-versioner finns [HDInsight-kluster-versioner](hdinsight-component-versioning.md).
>
>

1. När du skapar ett HDInsight-kluster från portalen klickar du på **valfri konfiguration**, och klicka sedan på **skriptåtgärder**.
2. På den **skriptåtgärder** anger du följande värden:

    ![Använd skriptåtgärder för att anpassa ett kluster](./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "Använd skriptåtgärder att anpassa ett kluster")

    <table border='1'>
        <tr><th>Egenskap</th><th>Värde</th></tr>
        <tr><td>Namn</td>
            <td>Ange ett namn för skriptåtgärder, till exempel <b>installera R</b>.</td></tr>
        <tr><td>Skript-URI</td>
            <td>Ange URI till det skript som anropas för att anpassa klustret, till exempel <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i></td></tr>
        <tr><td>Nodtyp</td>
            <td>Ange de noder som anpassning skriptet körs. Du kan välja <b>alla noder</b>, <b>huvudnoder endast</b>, eller <b>arbetarnoder</b> endast.
        <tr><td>Parametrar</td>
            <td>Ange parametrar, om det krävs av skriptet. Skript för att installera R kräver dock inte parametrar, så du kan lämna den tom.</td></tr>
    </table>

    Du kan lägga till fler än en skriptåtgärd för att installera flera komponenter i klustret. Klicka på kryssmarkeringen för att starta crating klustret när du har lagt till skripten.

Du kan också använda skriptet för att installera R på HDInsight med hjälp av Azure PowerShell eller HDInsight .NET SDK. Anvisningar för de här procedurerna finns senare i den här artikeln.

## <a name="run-r-scripts"></a>R-skript körs
Det här avsnittet beskrivs hur du kör ett R-skript på Hadoop-kluster med HDInsight.

1. **Upprätta en anslutning till fjärrskrivbord till klustret**: från den portalen aktivera Fjärrskrivbord för det kluster som du skapat med R installerat och Anslut till klustret. Instruktioner finns i [Anslut till HDInsight-kluster med RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
2. **Öppna konsolen R**: R installationen placerar en länk till R-konsolen på skrivbordet för huvudnoden. Klicka på den för att öppna konsolen R.
3. **Kör skriptet R**: R-skript kan köras direkt från R-konsolen genom att klistra in, markera den och trycka på RETUR. Här är ett enkelt exempelskript som genererar talen 1 till 100 och multiplicerar dem med 2.

        library(rmr2)
        library(rhdfs)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
        from.dfs(calc)

De två första raderna anropa RHadoop bibliotek som installeras med R. Den sista raden skriver ut resultat till konsolen. Resultatet bör se ut så här:

    [1,]  1 2
    [2,]  2 4
    .
    .
    .
    [98,]  98 196
    [99,]  99 198
    [100,] 100 200


## <a name="install-r-using-aure-powershell"></a>Installera R med Aure PowerShell
Se [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  Exemplet visar hur du installerar Spark med hjälp av Azure PowerShell. Du måste anpassa skript om du vill använda [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

## <a name="install-r-using-net-sdk"></a>Installera R med .NET SDK
Se [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). Exemplet visar hur du installerar Spark med .NET SDK. Du måste anpassa skript om du vill använda [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps11).

## <a name="see-also"></a>Se även
* [Installera och använda R på HDinsight Hadoop-kluster (Linux)](hdinsight-hadoop-r-scripts-linux.md)
* [Skapa Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md): allmän information om hur du skapar HDInsight-kluster
* [Anpassa HDInsight-kluster med skriptåtgärder][hdinsight-cluster-customize]: allmän information om hur du anpassar HDInsight-kluster med skriptåtgärder
* [Utveckla skriptåtgärd skript för HDInsight](hdinsight-hadoop-script-actions.md)
* [Installera och använda Spark på HDInsight-kluster][hdinsight-install-spark]: skriptåtgärder exempel om hur du installerar Spark
* [Installera Giraph på HDInsight-kluster](hdinsight-hadoop-giraph-install.md): skriptåtgärder exempel om hur du installerar Giraph
* [Installera Solr på HDInsight-kluster](hdinsight-hadoop-solr-install-linux.md): skriptåtgärder exempel om hur du installerar Solr.

[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-apache-spark-jupyter-spark-sql.md
