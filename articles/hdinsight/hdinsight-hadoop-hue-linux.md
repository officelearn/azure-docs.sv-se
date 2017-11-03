---
title: Hue med Hadoop i HDInsight Linux-baserade kluster - Azure | Microsoft Docs
description: "Lär dig mer om att installera Hue i HDInsight-kluster och använder tunneltrafik för att omdirigera begäranden till Hue. Använd Hue Bläddra lagring och köra Hive- eller Pig."
keywords: Hue hadoop
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 9e57fcca-e26c-479d-a745-7b80a9290447
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: bf68e9af7592d5abe5a4c9aba8c5061819cbc97a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Installera och använda Hue på HDInsight Hadoop-kluster

Lär dig mer om att installera Hue i HDInsight-kluster och använder tunneltrafik för att omdirigera begäranden till Hue.

> [!IMPORTANT]
> Stegen i det här dokumentet kräver ett HDInsight-kluster som använder Linux. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="what-is-hue"></a>Vad är Hue?
Hue är en uppsättning webbprogram som används för att interagera med ett Hadoop-kluster. Du kan använda Hue för att söka lagring som associerats med Hadoop-kluster (när HDInsight-kluster WASB), kör Hive-jobb och Pig-skript och så vidare. Följande komponenter är tillgängliga med Hue installationer på ett HDInsight Hadoop-kluster.

* Bivax Hive-redigeraren
* Pig
* Metastore manager
* Oozie
* FileBrowser (som kommunicerar WASB standardbehållaren)
* Jobbet webbläsare

> [!WARNING]
> Komponenter som ingår i HDInsight-kluster stöds fullt ut och Microsoft-supporten hjälper att isolera och lösa problem relaterade till komponenterna.
>
> Anpassade komponenter få kommersiellt rimliga stöd för att hjälpa dig att felsöka problemet ytterligare. Detta kan resultera i att lösa problemet eller där du uppmanas att engagera tillgängliga kanaler för öppen källkod där djup expertis för att teknik finns. Det finns till exempel många community-webbplatser som kan användas, t.ex: [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Apache-projekt har också project-webbplatser [http://apache.org](http://apache.org), till exempel: [Hadoop](http://hadoop.apache.org/).
>
>

## <a name="install-hue-using-script-actions"></a>Installera Hue med hjälp av skriptåtgärder

Skript för att installera Hue på en Linux-baserade HDInsight-kluster är tillgänglig på https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. Du kan använda det här skriptet för att installera Hue i kluster med Azure Storage BLOB (WASB) eller Azure Data Lake Store som standardlagring.

Det här avsnittet innehåller instruktioner om hur du använder skriptet vid etablering av klustret med Azure-portalen.

> [!NOTE]
> Azure PowerShell, Azure CLI, HDInsight .NET SDK eller Azure Resource Manager-mallar kan också användas för att tillämpa skriptåtgärder. Du kan även gälla skriptåtgärder kluster som körs redan. Mer information finns i [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Börja etablera ett kluster med hjälp av stegen i [etablera HDInsight-kluster på Linux](hdinsight-hadoop-provision-linux-clusters.md), men inte slutföra etablering.

   > [!NOTE]
   > Om du vill installera Hue i HDInsight-kluster rekommenderade headnode storleken är minst A4 (8 kärnor, 14 GB minne).
   >
   >
2. På den **valfri konfiguration** bladet väljer **skriptåtgärder**, och ange informationen som visas nedan:

    ![Ange skriptet åtgärdsparametrar för för Hue](./media/hdinsight-hadoop-hue-linux/hue-script-action.png "ange skript åtgärdsparametrar för för Hue")

   * **NAMNET**: Ange ett eget namn för skriptåtgärden.
   * **SKRIPT-URI**: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
   * **HEAD**: Markera det här alternativet
   * **WORKER**: lämnar fältet tomt.
   * **ZOOKEEPER**: lämnar fältet tomt.
   * **PARAMETRARNA**: lämnar fältet tomt.
3. Längst ned i den **skriptåtgärder**, använda den **Välj** för att spara konfigurationen. Använd slutligen den **Välj** knappen längst ned i den **valfri konfiguration** bladet för att spara konfigurationsinformationen för valfria.
4. Fortsätta etablering klustret enligt beskrivningen i [etablera HDInsight-kluster på Linux](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="use-hue-with-hdinsight-clusters"></a>Använda Hue med HDInsight-kluster

SSH-tunnlar är det enda sättet att komma åt Hue i klustret när den körs. Tunneltrafik via SSH tillåter trafik att gå direkt till headnode i klustret där Hue körs. När klustret är färdigetablerat, Använd följande steg för att använda Hue i ett kluster i HDInsight Linux.

> [!NOTE]
> Du rekommenderas att använda Firefox webbläsare för att följa anvisningarna nedan.
>
>

1. Använd informationen i [använder SSH-tunnlar för att komma åt Ambari-webbgränssnittet, resurshanteraren, jobbhistorik, NameNode, Oozie och andra webbgränssnitt](hdinsight-linux-ambari-ssh-tunnel.md) att skapa en SSH-tunnel från klientsystemet till HDInsight-klustret och sedan konfigurera webbläsaren för att använda SSH-tunnel som en proxy.

2. När du har skapat en SSH-tunnel och konfigurerat webbläsaren proxy-trafik via det, måste du söka efter namnet på den primära huvudnoden. Du kan göra detta genom att ansluta till klustret med SSH på port 22. Till exempel `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` där **användarnamn** är SSH-användarnamn och **KLUSTERNAMN** är namnet på klustret.

    Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

3. När du är ansluten, gör du följande kommando för att hämta fullständigt kvalificerade domännamnet för den primära headnode:

        hostname -f

    Detta returnerar ett namn liknar följande:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Detta är värdnamnet på den primära headnode där Hue-webbplatsen finns.
4. Använd webbläsaren för att öppna Hue-portalen på http://HOSTNAME:8888. Ersätt VÄRDNAMN med namnet som du hämtade i föregående steg.

   > [!NOTE]
   > När du loggar in för första gången uppmanas du att skapa ett konto kan logga in på Hue-portalen. De autentiseringsuppgifter som du anger här kommer att vara begränsad till portalen och inte är relaterade till administratören eller SSH-autentiseringsuppgifter som du angav när du etablera klustret.
   >
   >

    ![Logga in på Hue-portalen](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "ange autentiseringsuppgifter för Hue-portalen")

### <a name="run-a-hive-query"></a>Köra en Hive-fråga
1. Hue-portalen klickar du på **frågan redigerare**, och klicka sedan på **Hive** att öppna Hive-redigeraren.

    ![Använda Hive](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "använda Hive")
2. På den **hjälpa** fliken, under **databasen**, bör du se **hivesampletable**. Det här är en exempeltabell som levereras med Hadoop-kluster i HDInsight. Ange en exempelfråga i den högra rutan och se utdata på den **resultat** fliken i rutan nedan, som visas i skärmdumpen.

    ![Köra Hive-fråga](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "kör Hive-fråga")

    Du kan också använda den **diagram** fliken för att se en bild av resultatet.

### <a name="browse-the-cluster-storage"></a>Bläddra i klusterlagringen
1. Hue-portalen klickar du på **Filhanteraren** i det övre högra hörnet på menyraden.
2. Som standard öppnar webbläsaren på den **/användare/myuser** directory. Klicka på snedstreck före användarkatalog i sökvägen till Gå till roten i Azure storage-behållare som är associerade med klustret.

    ![Använd Filhanteraren](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "Använd filläsare")
3. Högerklicka på en fil eller mapp för att se de tillgängliga åtgärderna. Använd den **överför** i högra hörnet för att överföra filer till den aktuella katalogen. Använd den **ny** för att skapa nya filer eller kataloger.

> [!NOTE]
> Hue webbläsaren kan endast visa innehållet i standardbehållaren som är associerade med HDInsight-klustret. Inga ytterligare lagringsutrymme för konton/behållare som kan associeras med klustret blir inte tillgänglig via webbläsaren. Ytterligare behållare som är associerade med klustret kommer dock alltid vara tillgänglig för Hive-jobb. Om du anger kommandot exempelvis `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` i Hive-redigeraren ser du innehållet i ytterligare behållare också. I det här kommandot **newcontainer** är inte standardbehållaren som är kopplade till ett kluster.
>
>

## <a name="important-considerations"></a>Viktiga överväganden
1. Skriptet som används för att installera Hue installerar den endast på den primära headnode i klustret.

2. Under installationen av flera Hadoop-tjänsterna (HDFS, YARN, MR2, Oozie) startas om för att uppdatera konfigurationen. Det kan ta lite tid för andra Hadoop-tjänster att starta när skriptet har slutförts installera Hue. Detta kan påverka Hues prestanda från början. När alla tjänster startas Hue fungerar som den ska.
3. Hue förstår inte Tez jobb som är aktuella standardvärdet för Hive. Om du vill använda MapReduce som motorn för körning av Hive uppdatera skriptet för att använda följande kommando i skriptet:

        set hive.execution.engine=mr;

4. Du kan ha ett scenario där dina tjänster som körs på den primära headnode medan Resource Manager kan köras på sekundärt med Linux-kluster. Ett sådant scenario kan resultera i fel (se nedan) när du använder Hue för att visa information om jobb som KÖRS på klustret. Du kan visa Jobbinformationen när jobbet har slutförts.

   ![Hue-portalen fel](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Hue portal fel")

   Detta beror på ett känt problem. Som en tillfällig lösning kan du ändra Ambari så att active resurshanteraren körs även på den primära headnode.
5. Hue förstår WebHDFS medan HDInsight-kluster använder Azure Storage med `wasb://`. Anpassat skript används med skriptåtgärder installerar så WebWasb som är en tjänst för WebHDFS-kompatibelt du WASB. Så även om Hue-portalen står HDFS platser (t.ex. när du flyttar musen över den **Filhanteraren**), ska tolkas som WASB.

## <a name="next-steps"></a>Nästa steg
* [Installera Giraph på HDInsight-kluster](hdinsight-hadoop-giraph-install-linux.md). Använd anpassning av klustret för att installera Giraph på HDInsight Hadoop-kluster. Giraph kan du utföra diagrammet bearbetning med Hadoop och den kan användas med Azure HDInsight.
* [Installera Solr på HDInsight-kluster](hdinsight-hadoop-solr-install-linux.md). Använd anpassning av klustret för att installera Solr på HDInsight Hadoop-kluster. Solr kan du utföra kraftfulla sökningar på lagrade data.
* [Installera R på HDInsight-kluster](hdinsight-hadoop-r-scripts-linux.md). Använd anpassning av klustret för att installera R på HDInsight Hadoop-kluster. R är ett språk med öppen källkod och miljö för statistisk databehandling. Det ger hundratals inbyggda statistiska funktioner och sin egen programmeringsspråk som kombinerar aspekter av funktions- och objektorienterad programmering. Det ger också omfattande grafiskt funktioner.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
