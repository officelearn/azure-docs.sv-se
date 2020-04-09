---
title: Nyans med Hadoop på HDInsight Linux-baserade kluster - Azure
description: Lär dig hur du installerar Hue på HDInsight-kluster och använder tunnel för att dirigera begäranden till Hue. Använd Hue för att bläddra i lagring och köra Hive eller Pig.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/31/2020
ms.openlocfilehash: dea7e8d5679c8c5a14d6a4253b8a4b36343e6ed8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887103"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Installera och använda Hue på HDInsight Hadoop-kluster

Lär dig hur du installerar Hue på HDInsight-kluster och använder tunnel för att dirigera begäranden till Hue.

## <a name="what-is-hue"></a>Vad är Hue?

Hue är en uppsättning webbprogram som används för att interagera med ett Apache Hadoop-kluster. Du kan använda Hue för att bläddra i lagringen som är associerad med ett Hadoop-kluster (WASB, när det gäller HDInsight-kluster), köra Hive-jobb och Pig-skript och så vidare. Följande komponenter är tillgängliga med Hue-installationer på ett HDInsight Hadoop-kluster.

* Bivax Hive Redaktör
* Apache gris
* Metastore-chef
* Apache Oozie
* FileBrowser (som talar till WASB standard behållare)
* Jobb webbläsare

> [!WARNING]  
> Komponenter som medföljer HDInsight-klustret stöds fullt ut och Microsoft Support hjälper till att isolera och lösa problem som är relaterade till dessa komponenter.
>
> Anpassade komponenter får kommersiellt rimligt stöd som hjälper dig att felsöka problemet ytterligare. Detta kan resultera i att lösa problemet eller be dig att engagera tillgängliga kanaler för öppen källkod teknik där djup expertis för den tekniken finns. Till exempel finns det många community webbplatser som kan användas, som: [https://stackoverflow.com](https://stackoverflow.com) [MSDN forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), . Även Apache projekt har [https://apache.org](https://apache.org)projektwebbplatser på , till exempel: [Hadoop](https://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Installera Nyans med skriptåtgärder

Använd informationen i tabellen nedan för skriptåtgärden. Se [Anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md) för specifika instruktioner om hur du använder skriptåtgärder.

> [!NOTE]  
> Om du vill installera Hue på HDInsight-kluster är den rekommenderade headnodstorleken minst A4 (8 kärnor, 14 GB minne).

|Egenskap |Värde |
|---|---|
|Skripttyp:|- Anpassad|
|Namn|Installera nyans|
|Bash skript URI|`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`|
|Nodtyper:|Head|

## <a name="use-hue-with-hdinsight-clusters"></a>Använda Hue med HDInsight-kluster

Du kan bara ha ett användarkonto med Hue i vanliga kluster. Aktivera [Enterprise Security Package](./domain-joined/hdinsight-security-overview.md) i klustret för åtkomst för flera användare. SSH-tunnel är det enda sättet att komma åt Hue i klustret när det körs. Tunnelning via SSH gör det möjligt för trafiken att gå direkt till huvudnoden för klustret där Hue körs. När klustret har etablerats är det att använda följande steg för att använda Hue i ett HDInsight-kluster.

> [!NOTE]  
> Vi rekommenderar att du använder webbläsaren Firefox för att följa instruktionerna nedan.

1. Använd informationen i [Använd SSH-tunnel för att komma åt Apache Ambari webbgränssnitt, ResourceManager, JobHistory, NameNode, Oozie och andra webbgränssnitt](hdinsight-linux-ambari-ssh-tunnel.md) för att skapa en SSH-tunnel från klientsystemet till HDInsight-klustret och sedan konfigurera webbläsaren så att den använder SSH-tunneln som en proxy.

1. Använd kommandot ssh för att ansluta till [klustret.](./hdinsight-hadoop-linux-use-ssh-unix.md) Redigera kommandot nedan genom att ersätta CLUSTERNAME med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. När du har anslutit använder du följande kommando för att hämta det fullständigt kvalificerade domännamnet för den primära headnoden:

    ```bash
    hostname -f
    ```

    Detta returnerar ett namn som liknar följande:

        myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Detta är värdnamnet för den primära headnode där Hue-webbplatsen finns.

1. Använd webbläsaren för att öppna `http://HOSTNAME:8888`Hue-portalen på . Ersätt HOSTNAME med det namn du fick i föregående steg.

   > [!NOTE]  
   > När du loggar in för första gången uppmanas du att skapa ett konto för att logga in på Hue-portalen. De autentiseringsuppgifter som du anger här kommer att begränsas till portalen och är inte relaterade till administratörs- eller SSH-användarautentiseringsuppgifterna som du angav när du etablerar klustret.

    ![HdInsight hue portal inloggningsfönster](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Ange autentiseringsuppgifter för Hue-portalen")

### <a name="run-a-hive-query"></a>Köra en Hive-fråga

1. På Hue-portalen väljer du **Frågeredigerare**och väljer sedan **Hive** för att öppna Hive-redigeraren.

    ![HDInsight hue portal använder hive editor](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Använd Hive")

2. På fliken **Assist,** under **Databas,** bör du se **hivesampletable**. Det här är en exempeltabell som levereras med alla Hadoop-kluster på HDInsight. Ange en exempelfråga i den högra rutan och se utdata på fliken **Resultat** i fönstret nedan, som visas i skärmfångsten.

    ![HDInsight hue portal hive fråga](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Kör Hive-fråga")

    Du kan också använda fliken **Diagram** för att se en visuell representation av resultatet.

### <a name="browse-the-cluster-storage"></a>Bläddra i klusterlagringen

1. Välj **Filbläddrare** längst upp till höger i menyraden på Hue-portalen.
2. Som standard öppnas filbläddraren i katalogen **/user/myuser.** Välj det framåtskurna precis innan användarkatalogen i sökvägen för att gå till roten för Azure-lagringsbehållaren som är associerad med klustret.

    ![HDInsight hue portal fil webbläsare](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "Använda filbläddraren")

3. Högerklicka på en fil eller mapp för att se tillgängliga åtgärder. Använd **knappen Ladda upp** i det högra hörnet för att ladda upp filer till den aktuella katalogen. Använd knappen **Nytt** för att skapa nya filer eller kataloger.

> [!NOTE]  
> Hue-filbläddraren kan bara visa innehållet i standardbehållaren som är associerad med HDInsight-klustret. Ytterligare lagringskonton/behållare som du kan ha associerat med klustret kommer inte att vara tillgängliga med hjälp av filbläddraren. De ytterligare behållare som är associerade med klustret kommer dock alltid att vara tillgängliga för Hive-jobben. Om du till exempel `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` anger kommandot i Hive-redigeraren kan du också se innehållet i ytterligare behållare. I det här kommandot är **nya behållaren** inte standardbehållaren som är associerad med ett kluster.

## <a name="important-considerations"></a>Att tänka på

1. Skriptet som används för att installera Hue installerar det bara på klustrets primära huvudnod.

1. Under installationen startas flera Hadoop-tjänster (HDFS, YARN, MR2, Oozie) om för att uppdatera konfigurationen. När skriptet har installerats kan det ta lite tid innan andra Hadoop-tjänster startar. Detta kan påverka Hues prestanda från början. När alla tjänster har börjat kommer Hue att vara fullt fungerande.

1. Hue förstår inte Apache Tez-jobb, vilket är den aktuella standardinställningen för Hive. Om du vill använda MapReduce som Hive-körningsmotor uppdaterar du skriptet så att du använder följande kommando i skriptet:

         set hive.execution.engine=mr;

1. Med Linux-kluster kan du ha ett scenario där dina tjänster körs på den primära headnoden medan Resource Manager kan köras på sekundärt. Ett sådant scenario kan resultera i fel (visas nedan) när du använder Hue för att visa information om jobb som körs i klustret. Du kan dock visa jobbinformationen när jobbet har slutförts.

   ![Exempelmeddelande om fel i Hue-portalen](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Fel i Hue-portalen")

   Detta beror på ett känt problem. Som en lösning ändrar du Ambari så att den aktiva Resurshanteraren också körs på den primära headnoden.

1. Hue förstår WebHDFS medan HDInsight-kluster `wasbs://`använder Azure Storage med . Så installerar det anpassade skriptet som används med skriptåtgärd WebWasb, som är en WebHDFS-kompatibel tjänst för att prata med WASB. Så även om Hue-portalen säger HDFS på platser (som när du flyttar musen över **filbläddraren),** bör den tolkas som WASB.

## <a name="next-steps"></a>Nästa steg

[Installera R på HDInsight-kluster](hdinsight-hadoop-r-scripts-linux.md). Använd klusteranpassning för att installera R på HDInsight Hadoop-kluster. R är ett språk och miljö med öppen källkod för statistisk databehandling. Det ger hundratals inbyggda statistiska funktioner och sitt eget programmeringsspråk som kombinerar aspekter av funktionell och objektorienterad programmering. Det ger också omfattande grafiska funktioner.
