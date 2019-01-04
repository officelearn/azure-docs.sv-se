---
title: Hue med Hadoop i HDInsight Linux-baserade kluster – Azure
description: Lär dig mer om att installera Hue i HDInsight-kluster och använda tunnel för att dirigera begäranden till nyans. Använda Hue att bläddra i lagring och köra Hive- eller Pig.
keywords: Hue hadoop
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: hrasheed
ms.openlocfilehash: e0e25a9a1e70575d862374da0fb3097408b002e7
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714635"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Installera och använda Hue på HDInsight Hadoop-kluster

Lär dig mer om att installera Hue i HDInsight-kluster och använda tunnel för att dirigera begäranden till nyans.

> [!IMPORTANT]  
> Stegen i det här dokumentet kräver ett HDInsight-kluster som använder Linux. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="what-is-hue"></a>Vad är Hue?
Hue är en samling webbprogram som används för att interagera med ett Apache Hadoop-kluster. Du kan använda Hue för att bläddra i lagring som är associerade med ett Hadoop-kluster (när det gäller HDInsight-kluster WASB), kör Hive-jobb och Pig-skript och så vidare. Följande komponenter är tillgängliga med Hue installationer på ett HDInsight Hadoop-kluster.

* Bivax Hive-redigeraren
* Apache Pig
* Metaarkiv manager
* Apache Oozie
* FileBrowser (som pratar med WASB-behållare som standard)
* Jobbwebbläsare

> [!WARNING]  
> Komponenter som tillhandahålls med HDInsight-kluster stöds fullt ut och Microsoft Support hjälper till att isolera och lösa problem relaterade till dessa komponenter.
>
> Anpassade komponenter får kommersiellt rimlig support för att hjälpa dig att felsöka problemet ytterligare. Detta kan resultera i att lösa problemet eller där du uppmanas att engagera tillgängliga kanaler för tekniker med öppen källkod som där djup kompetens för den tekniken hittas. Det finns exempelvis många community-webbplatser som kan användas, t.ex: [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ https://stackoverflow.com ](https://stackoverflow.com). Även Apache-projekt har project-webbplatser på [ https://apache.org ](https://apache.org), till exempel: [Hadoop](https://hadoop.apache.org/).
>
>

## <a name="install-hue-using-script-actions"></a>Installera Hue med hjälp av skriptåtgärder

Skript för att installera Hue i ett Linux-baserade HDInsight-kluster finns på https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. Du kan använda det här skriptet för att installera Hue i kluster med Azure Storage BLOB-objekt (WASB) eller Azure Data Lake Storage som standardlagringsutrymme.

Det här avsnittet innehåller anvisningar om hur du använder skriptet när du etablerar kluster med Azure portal.

> [!NOTE]  
> Azure PowerShell, den klassiska Azure-CLI, HDInsight .NET SDK eller Azure Resource Manager-mallar kan också användas för att tillämpa skriptåtgärder. Du kan också använda skriptåtgärder för kluster som körs redan. Mer information finns i [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Börja etablera ett kluster med hjälp av stegen i [etablera HDInsight-kluster på Linux](hdinsight-hadoop-provision-linux-clusters.md), men inte Slutför etableringen.

   > [!NOTE]  
   > Om du vill installera Hue i HDInsight-kluster, den rekommendera huvudnoden storleken är minst A4 (8 kärnor, 14 GB minne).
   >
   >
2. På den **valfri konfiguration** bladet väljer **skriptåtgärder**, och ange information som visas nedan:

    ![Ange skriptet åtgärdsparametrar för Hue](./media/hdinsight-hadoop-hue-linux/hue-script-action.png "tillhandahålla skript åtgärdsparametrar för Hue")

   * **NAMN PÅ**: Ange ett eget namn för skriptåtgärden.
   * **SKRIPT-URI**: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
   * **HEAD**: Markera det här alternativet.
   * **WORKER**: Låt den vara tom.
   * **ZOOKEEPER**: Låt den vara tom.
   * **PARAMETRAR**: Låt den vara tom.
3. Längst ned på den **skriptåtgärder**, använda den **Välj** för att spara konfigurationen. Använd slutligen den **Välj** knappen längst ned på den **valfri konfiguration** bladet för att spara ytterligare konfigurationsinformation.
4. Fortsätta att etablera klustret enligt beskrivningen i [etablera HDInsight-kluster på Linux](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="use-hue-with-hdinsight-clusters"></a>Använda Hue med HDInsight-kluster

SSH-tunnlar är det enda sättet att komma åt Hue i klustret när den körs. Via SSH-tunnel tillåter trafik att gå direkt till huvudnoden i klustret där Hue körs. När klustret är färdigetablerat, kan du använda följande steg för att använda Hue i ett HDInsight Linux-kluster.

> [!NOTE]  
> Vi rekommenderar att du använder webbläsaren Firefox för att följa anvisningarna nedan.
>
>

1. Använd informationen i [använda SSH-tunnlar till Apache Ambari-webbgränssnittet, resurshanteraren, JobHistory, NameNode, Oozie och andra webbgränssnitt](hdinsight-linux-ambari-ssh-tunnel.md) att skapa en SSH-tunnel från ditt klientsystem till HDInsight-klustret och sedan konfigurera din webbläsare du använder SSH-tunnel som en proxy.

2. När du har skapat en SSH-tunnel och konfigurerat din webbläsare med proxy-trafik via det, måste du söka efter namnet på den primära huvudnoden. Du kan göra detta genom att ansluta till klustret med SSH på port 22. Till exempel `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` där **användarnamn** är ditt SSH-användarnamn och **CLUSTERNAME** är namnet på klustret.

    Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

3. När du är ansluten, gör du följande kommando för att hämta det fullständigt kvalificerade domännamnet för den primära huvudnoden:

        hostname -f

    Detta returnerar ett namn liknar följande:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Det här är värdnamnet för den primära huvudnoden där Hue-webbplats finns.
4. Använda webbläsaren för att öppna Hue-portalen på http://HOSTNAME:8888. Ersätt VÄRDDATORNAMN med namnet som du hämtade i föregående steg.

   > [!NOTE]  
   > När du loggar in för första gången uppmanas du att skapa ett konto för att logga in på Hue-portalen. De autentiseringsuppgifter som du anger här begränsas till portalen och inte är relaterade till administratören eller SSH-autentiseringsuppgifterna som du angav när du etablera klustret.
   >
   >

    ![Logga in på Hue-portalen](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "ange autentiseringsuppgifter för Hue-portalen")

### <a name="run-a-hive-query"></a>Köra en Hive-fråga
1. Hue-portalen klickar du på **fråga redigerare**, och klicka sedan på **Hive** att öppna Hive-redigeraren.

    ![Använda Hive](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "använda Hive")
2. På den **hjälpa** fliken, under **databasen**, bör du se **hivesampletable**. Det här är en exempeltabell som medföljer alla Hadoop-kluster i HDInsight. Ange en exempelfråga i den högra rutan och visa utdata på den **resultat** fliken i rutan nedan, som visas i skärmdumpen.

    ![Köra Hive-fråga](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "kör Hive-fråga")

    Du kan också använda den **diagram** fliken för att se en visuell representation av resultatet.

### <a name="browse-the-cluster-storage"></a>Bläddra klusterlagring
1. Hue-portalen klickar du på **filläsare** i det övre högra hörnet av menyraden.
2. Som standard öppnar webbläsaren på den **/användare/myuser** directory. Klicka på snedstreck precis före användarkatalog i sökvägen till går du till roten i Azure storage-behållare som är kopplat till klustret.

    ![Använd filläsare](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "Använd filläsare")
3. Högerklicka på en fil eller mapp för att se de tillgängliga åtgärderna. Använd den **överför** i högra hörnet för att ladda upp filer till den aktuella katalogen. Använd den **New** för att skapa nya filer eller kataloger.

> [!NOTE]  
> Hue-filläsare kan bara visa innehållet i standardbehållaren kopplat till HDInsight-klustret. Eventuella ytterligare lagringsutrymme för konton/behållare som du har associerat med klustret kommer inte att komma åt med hjälp av webbläsaren. Ytterligare behållare som är associerade med klustret kommer dock alltid vara tillgänglig för Hive-jobb. Exempel: Om du har angett kommandot `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` i Hive-redigeraren kan du se innehållet i ytterligare behållare också. I det här kommandot **newcontainer** är inte standardbehållaren som är associerade med ett kluster.
>
>

## <a name="important-considerations"></a>Att tänka på
1. Skriptet som används för att installera Hue installerar den endast på den primära huvudnoden av klustret.

2. Under installationen, flera Hadoop-tjänster (HDFS, YARN, MR2, Oozie) startas om för att uppdatera konfigurationen. När skriptet har slutförts installera Hue, kan det ta lite tid för andra Hadoop-tjänster ska kunna starta. Detta kan påverka Hues prestanda från början. När alla tjänster startas Hue fungerar som den ska.
3. Hue förstår inte Apache Tez-jobb som är aktuella standardvärdet för Hive. Om du vill använda MapReduce som motorn för körning av Hive uppdatera skriptet för att använda följande kommando i skriptet:

        set hive.execution.engine=mr;

4. Du kan ha ett scenario där dina tjänster körs på den primära huvudnoden medan Resource Manager kan köras på sekundärt med Linux-kluster. Sådant scenario kan resultera i fel (se nedan) när du använder Hue för att visa information om jobb som KÖRS i klustret. Du kan dock visa Jobbinformationen när jobbet har slutförts.

   ![Hue-portalen fel](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Hue-portalen fel")

   Detta beror på ett känt problem. Som en lösning kan du ändra Ambari så att active Resource Manager körs även på den primära huvudnoden.
5. Hue förstår WebHDFS medan HDInsight-kluster använder Azure Storage med hjälp av `wasb://`. Så installerar det anpassade skriptet som används med skriptåtgärd WebWasb, som är en tjänst för WebHDFS-kompatibla du WASB. Så även via Hue-portalen säger HDFS på platser (som när du flyttar muspekaren över den **filläsare**), ska tolkas som WASB.

## <a name="next-steps"></a>Nästa steg
* [Installera Apache Giraph på HDInsight-kluster](hdinsight-hadoop-giraph-install-linux.md). Använd kluster anpassning för att installera Giraph på HDInsight Hadoop-kluster. Giraph kan du utföra diagrambearbetning med Hadoop och den kan användas med Azure HDInsight.
* [Installera Apache Solr på HDInsight-kluster](hdinsight-hadoop-solr-install-linux.md). Använd kluster anpassning för att installera Solr på HDInsight Hadoop-kluster. Solr kan du utföra kraftfulla sökningar på lagrade data.
* [Installera R på HDInsight-kluster](hdinsight-hadoop-r-scripts-linux.md). Använd kluster anpassning för att installera R på HDInsight Hadoop-kluster. R är en öppen källkod och -miljö för statistisk databehandling. Den innehåller hundratals inbyggda statistiska funktioner och sin egen programmeringsspråk som kombinerar aspekter av funktions- och objektorienterad programmering. Det ger även omfattande grafiska funktioner.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
