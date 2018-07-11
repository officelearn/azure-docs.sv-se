---
title: Använd skriptåtgärder för att installera Solr på Hadoop-kluster – Azure | Microsoft Docs
description: Lär dig mer om att anpassa HDInsight-kluster med Solr med skriptåtgärd.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b1e6f338-8ac1-4b38-bbb5-2f7388b9de3b
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 6445bcdc361bcda3beb6abcb6196fecc9c6c0024
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952883"
---
# <a name="install-and-use-solr-on-windows-based-hdinsight-clusters"></a>Installera och använda Solr i Windows-baserade HDInsight-kluster

Lär dig hur du anpassar Windows-baserade HDInsight-kluster med Solr med skriptåtgärd och använda Solr kan söka efter data.

> [!IMPORTANT]
> Stegen i det här dokumentet fungerar bara med Windows-baserade HDInsight-kluster. HDInsight är endast tillgänglig på Windows för versioner lägre än HDInsight 3.4. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Information om att använda Solr med en Linux-baserat kluster finns i [installera och använda Solr på HDinsight Hadoop-kluster (Linux)](hdinsight-hadoop-solr-install-linux.md).


Du kan installera Solr på någon typ av kluster (Hadoop-, Storm, HBase, Spark) i Azure HDInsight med hjälp av *skriptåtgärd*. Ett exempelskript för att installera Solr på ett HDInsight-kluster finns i en skrivskyddad Azure storage blob [ https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

Exempelskriptet fungerar bara med HDInsight-kluster av version 3.1. Läs mer på HDInsight-klusterversioner [HDInsight-klusterversioner](hdinsight-component-versioning.md).

Exempelskript som används i det här avsnittet skapar du ett Windows-baserade Solr-kluster med en viss konfiguration. Om du vill konfigurera Solr klustret med olika samlingar, fragment, scheman, repliker och annat måste du ändra skriptet och Solr binärfiler i enlighet med detta.

**Relaterade artiklar**

* [Installera och använda Solr på HDinsight Hadoop-kluster (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [Skapa Hadoop-kluster i HDInsight](hdinsight-provision-clusters.md): allmän information om hur du skapar HDInsight-kluster.
* [Anpassa HDInsight-kluster med skriptåtgärd][hdinsight-cluster-customize]: allmän information om hur du anpassar HDInsight-kluster med skriptåtgärd.
* [Utveckla skriptåtgärder skript för HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-solr"></a>Vad är Solr?
<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> är en enterprise search plattform som möjliggör kraftfull fulltextsökning på data. Hadoop kan lagra och hantera stora mängder data, ger Apache Solr sökfunktioner för att snabbt hämta data.

## <a name="install-solr-using-portal"></a>Installera Solr med hjälp av portalen
1. Börja skapa ett kluster med hjälp av den **skapa anpassade** enligt beskrivningen på [skapa Hadoop-kluster i HDInsight](hdinsight-provision-clusters.md).
2. På den **skriptåtgärder** sidan i guiden klickar du på **lägga till skriptåtgärd** att ge information om skriptåtgärd, enligt nedan:

    ![Använda skriptåtgärder för att anpassa ett kluster](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Använd skriptåtgärder för att anpassa ett kluster")

    <table border='1'>
        <tr><th>Egenskap </th><th>Värde</th></tr>
        <tr><td>Namn</td>
            <td>Ange ett namn för skriptåtgärden. Till exempel <b>installera Solr</b>.</td></tr>
        <tr><td>Skript-URI</td>
            <td>Ange det ID: T URI (Uniform Resource) till det skript som anropas om du vill anpassa klustret. Exempel: <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
        <tr><td>Nodtyp</td>
            <td>Ange de noder som anpassning skriptet körs. Du kan välja <b>alla noder</b>, <b>huvudnoder endast</b>, eller <b>arbetsnoder endast</b>.
        <tr><td>Parametrar</td>
            <td>Ange parametrar, om det krävs av skriptet. Skript för att installera Solr kräver inte några parametrar, så du kan lämna det tomt.</td></tr>
    </table>

    Du kan lägga till fler än en skriptåtgärd för att installera flera komponenter i klustret. När du har lagt till skripten, klicka på bockmarkeringen för att skapa klustret.

## <a name="use-solr"></a>Använd Solr
Du måste börja med att indexera Solr med vissa datafiler. Du kan sedan använda Solr för att köra sökfrågor på indexerade data. Utför följande steg om du vill använda Solr i ett HDInsight-kluster:

1. **Använda Remote Desktop Protocol (RDP) fjärråtkomst till HDInsight-kluster med Solr installerat**. Aktivera Fjärrskrivbord för klustret du skapade med Solr installerad och sedan fjärransluta till klustret från Azure-portalen. Anvisningar finns i [Anslut till HDInsight-kluster med RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
2. **Index Solr genom att överföra datafiler**. När du indexera Solr placera dokument som du kan behöva söka på. Indexera Solr, använda RDP fjärråtkomst till klustret, navigera på skrivbordet, öppna Hadoop-kommandoraden och navigera till **C:\apps\dist\solr-4.7.2\example\exampledocs**. Kör följande kommando:

        java -jar post.jar solr.xml monitor.xml

    Följande utdata visas på konsolen:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    Verktyget post.jar indexerar Solr med två exempeldokument **solr.xml** och **monitor.xml**. Verktyget post.jar och exempeldokument är tillgängliga med Solr installation.
3. **Använda Solr instrumentpanelen för att söka i de indexerade dokumenten**. Öppna Internet Explorer i RDP-session till HDInsight-kluster och starta instrumentpanelen för Solr på **http://headnodehost:8983/solr/#/**. I den vänstra rutan, från den **Core väljare** listrutan, väljer **collection1**, och inom det, klickar du på **fråga**. Ange följande värden för att välja och returnera alla dokument i Solr, exempelvis:

   * I den **q** text anger  **\*:**\*. Detta returnerar alla dokument som indexeras i Solr. Du kan ange den strängen här om du vill söka efter en specifik sträng i dokumenten.
   * I den **wt** text väljer utdataformat. Standardvärdet är **json**. Klicka på **frågan**.

     ![Använda skriptåtgärder för att anpassa ett kluster](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "köra en fråga på Solr instrumentpanel")

     Resultatet returnerar två dokumenten som vi använde för indexering Solr. Utdata liknar följande:

           "response": {
               "numFound": 2,
               "start": 0,
               "maxScore": 1,
               "docs": [
                 {
                   "id": "SOLR1000",
                   "name": "Solr, the Enterprise Search Server",
                   "manu": "Apache Software Foundation",
                   "cat": [
                     "software",
                     "search"
                   ],
                   "features": [
                     "Advanced Full-Text Search Capabilities using Lucene",
                     "Optimized for High Volume Web Traffic",
                     "Standards Based Open Interfaces - XML and HTTP",
                     "Comprehensive HTML Administration Interfaces",
                     "Scalability - Efficient Replication to other Solr Search Servers",
                     "Flexible and Adaptable with XML configuration and Schema",
                     "Good unicode support: héllo (hello with an accent over the e)"
                   ],
                   "price": 0,
                   "price_c": "0,USD",
                   "popularity": 10,
                   "inStock": true,
                   "incubationdate_dt": "2006-01-17T00:00:00Z",
                   "_version_": 1486960636996878300
                 },
                 {
                   "id": "3007WFP",
                   "name": "Dell Widescreen UltraSharp 3007WFP",
                   "manu": "Dell, Inc.",
                   "manu_id_s": "dell",
                   "cat": [
                     "electronics and computer1"
                   ],
                   "features": [
                     "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                   ],
                   "includes": "USB cable",
                   "weight": 401.6,
                   "price": 2199,
                   "price_c": "2199,USD",
                   "popularity": 6,
                   "inStock": true,
                   "store": "43.17614,-90.57341",
                   "_version_": 1486960637584081000
                 }
               ]
             }
4. **Rekommenderat: Säkerhetskopiera indexerade data från Solr till Azure Blob storage som är kopplat till HDInsight-klustret**. Som en bra idé, bör du säkerhetskopiera indexerade data från Solr klusternoder till Azure Blob storage. Utför följande steg för att göra det:

   1. Öppna Internet Explorer från RDP-session och peka på följande URL:

           http://localhost:8983/solr/replication?command=backup

       Du bör se ett svar som detta:
            
      ```xml
      <?xml version="1.0" encoding="UTF-8"?>
          <response>
             <lst name="responseHeader">
               <int name="status">0</int>
               <int name="QTime">9</int>
             </lst>
            <str name="status">OK</str>
          </response>
      ```
      
   2. I fjärrsessionen, går du till {SOLR_HOME}\{samling} \data. För klustret skapas via exempelskriptet, bör detta vara `C:\apps\dist\solr-4.7.2\example\solr\collection1\data`. På den här platsen bör du se en mapp för ögonblicksbilder som skapats med ett namn som liknar **ögonblicksbild.* tidsstämpel ***.
   
   3. ZIP-mappen för ögonblicksbilder och överför den till Azure Blob storage. Gå till platsen för mappen för ögonblicksbilder med hjälp av följande kommando från kommandoraden Hadoop:

      ```
      hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data
      ```

   Det här kommandot kopierar ögonblicksbilden till /example/data/under behållaren i standard Storage-konto som är kopplat till klustret.

## <a name="install-solr-using-aure-powershell"></a>Installera Solr med Aure PowerShell
Se [anpassa HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  Exemplet visar hur du installerar Spark med Azure PowerShell. Du behöver anpassa skript om du vill använda [ https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="install-solr-using-net-sdk"></a>Installera Solr med .NET SDK
Se [anpassa HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). Exemplet visar hur du installerar Spark med .NET SDK. Du behöver anpassa skript om du vill använda [ https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="see-also"></a>Se också
* [Installera och använda Solr på HDinsight Hadoop-kluster (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [Skapa Hadoop-kluster i HDInsight](hdinsight-provision-clusters.md): allmän information om hur du skapar HDInsight-kluster.
* [Anpassa HDInsight-kluster med skriptåtgärd][hdinsight-cluster-customize]: allmän information om hur du anpassar HDInsight-kluster med skriptåtgärd.
* [Utveckla skriptåtgärder skript för HDInsight](hdinsight-hadoop-script-actions.md).
* [Installera och använda Spark på HDInsight-kluster][hdinsight-install-spark]: skriptåtgärd exempel om hur du installerar Spark.
* [Installera Giraph på HDInsight-kluster](hdinsight-hadoop-giraph-install.md): skriptåtgärd exempel om att installera Giraph.

[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
