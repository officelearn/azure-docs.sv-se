---
title: Nyans med Hadoop på HDInsight Linux-baserade kluster – Azure
description: Lär dig hur du installerar nyans i HDInsight-kluster och använder tunnlar för att dirigera begär anden till nyans. Använd nyans för att bläddra i lagring och köra Hive eller gris.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 8a644beede4089133f88b824fd8d34dddec3b15e
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751112"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Installera och använda nyans på HDInsight Hadoop-kluster

Lär dig hur du installerar nyans i HDInsight-kluster och använder tunnlar för att dirigera begär anden till nyans.

## <a name="what-is-hue"></a>Vad är nyans?

Nyans är en uppsättning webb program som används för att interagera med ett Apache Hadoop-kluster. Du kan använda nyans för att bläddra i lagrings utrymmet som är associerat med ett Hadoop-kluster (WASB, när det gäller HDInsight-kluster), köra Hive-jobb och gris-skript och så vidare. Följande komponenter är tillgängliga med nyans installationer på ett HDInsight Hadoop-kluster.

* Beeswax Hive-redigerare
* Apache Pig
* Metaarkiv Manager
* Apache Oozie
* FileBrowser (som pratar med WASB som standard behållare)
* Jobb webbläsare

> [!WARNING]  
> Komponenter som ingår i HDInsight-klustret stöds fullt ut och Microsoft Support hjälper till att isolera och lösa problem som rör dessa komponenter.
>
> Anpassade komponenter får kommersiellt rimlig support för att hjälpa dig att ytterligare felsöka problemet. Detta kan resultera i att lösa problemet eller be dig att engagera tillgängliga kanaler för tekniken med öppen källkod där djupgående expertis för tekniken hittas. Det finns till exempel många community-platser som kan användas, t. ex. [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Apache-projekt har även projekt webbplatser på [https://apache.org](https://apache.org), till exempel: [Hadoop](https://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Installera nyans med skript åtgärder

Använd informationen i tabellen nedan för skript åtgärden. Se [Anpassa HDInsight-kluster med skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md) för att få detaljerade instruktioner om hur du använder skript åtgärder.

> [!NOTE]  
> Om du vill installera nyans i HDInsight-kluster är den rekommenderade huvudnoden-storleken minst A4 (8 kärnor, 14 GB minne).

|Egenskap |Värde |
|---|---|
|Skript typ:|– Anpassad|
|Namn|Installera Hue|
|Bash-skript-URI|`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`|
|Node-typ (er):|Huvud|

## <a name="use-hue-with-hdinsight-clusters"></a>Använda nyans med HDInsight-kluster

SSH-tunnlar är det enda sättet att få åtkomst till en nyans i klustret när den körs. Tunnel trafik via SSH gör att trafiken går direkt till huvudnoden i klustret där nyansen körs. När klustret har slutfört etableringen använder du följande steg för att använda nyans i ett HDInsight-kluster.

> [!NOTE]  
> Vi rekommenderar att du använder Firefox webbläsare för att följa anvisningarna nedan.

1. Använd informationen i [använda SSH-tunnlar för att komma åt Apache Ambari Web UI, ResourceManager, JobHistory, NameNode, Oozie och andra webb gränssnitt](hdinsight-linux-ambari-ssh-tunnel.md) för att skapa en SSH-tunnel från klient systemet till HDInsight-klustret, och konfigurera sedan webbläsaren att använda SSH-tunneln som en proxy.

1. Använd [SSH-kommandot](./hdinsight-hadoop-linux-use-ssh-unix.md) för att ansluta till klustret. Redigera kommandot nedan genom att ersätta kluster namn med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. När du är ansluten använder du följande kommando för att hämta det fullständigt kvalificerade domän namnet för den primära huvudnoden:

    ```bash
    hostname -f
    ```

    Detta kommer att returnera ett namn som liknar följande:

        myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Detta är värd namnet för den primära huvudnoden där nyans webbplatsen finns.

1. Använd webbläsaren för att öppna nyans-portalen på `http://HOSTNAME:8888`. Ersätt HOSTNAME med det namn som du fick i föregående steg.

   > [!NOTE]  
   > När du loggar in för första gången uppmanas du att skapa ett konto för att logga in på nyans portalen. De autentiseringsuppgifter som du anger här kommer att begränsas till portalen och är inte relaterade till de autentiseringsuppgifter för administratörer eller SSH-användare som du angav när klustret etableras.

    ![Inloggnings fönster för HDInsight nyans-portalen](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Ange autentiseringsuppgifter för nyans-portalen")

### <a name="run-a-hive-query"></a>Köra en Hive-fråga

1. Välj **Frågeredigeraren**på nyans-portalen och välj sedan **Hive** för att öppna Hive-redigeraren.

    ![HDInsight nyans-portalen använder Hive-redigeraren](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Använd Hive")

2. På fliken **Assist** , under **databas**, bör du se **hivesampletable**. Det här är en exempel tabell som levereras med alla Hadoop-kluster i HDInsight. Ange en exempel fråga i den högra rutan och se utdata på fliken **resultat** i fönstret nedan, som visas i skärmdumpen.

    ![HDInsight nyans-portalens Hive-fråga](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Kör Hive-fråga")

    Du kan också använda fliken **diagram** för att visa en visuell representation av resultatet.

### <a name="browse-the-cluster-storage"></a>Bläddra i kluster lagringen

1. Från nyans-portalen väljer du **fil läsare** i det övre högra hörnet på Meny raden.
2. Som standard öppnas fil läsaren i **/User/myuser** -katalogen. Välj snedstrecket direkt före användar katalogen i sökvägen för att gå till roten för Azure Storage-behållaren som är associerad med klustret.

    ![Webb läsar filen för HDInsight nyans-portalen](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "Använd fil webbläsare")

3. Högerklicka på en fil eller mapp för att se tillgängliga åtgärder. Använd knappen **Ladda upp** i det högra hörnet för att ladda upp filer till den aktuella katalogen. Använd knappen **nytt** för att skapa nya filer eller kataloger.

> [!NOTE]  
> Filen för nyans-filen kan bara visa innehållet i standard behållaren som är associerad med HDInsight-klustret. Eventuella ytterligare lagrings konton/behållare som du kanske har associerat med klustret kommer inte att vara tillgängliga via fil läsaren. Dock är de ytterligare behållare som är kopplade till klustret alltid tillgängliga för Hive-jobben. Om du till exempel anger kommandot `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` i Hive-redigeraren kan du även se innehållet i ytterligare behållare. I det här kommandot är **newcontainer** inte standard behållaren som är associerad med ett kluster.

## <a name="important-considerations"></a>Viktiga överväganden

1. Skriptet som används för att installera nyans installerar bara det på den primära huvudnoden i klustret.

1. Under installationen startas flera Hadoop-tjänster (HDFS, garn, MR2, Oozie) för att uppdatera konfigurationen. När skriptet har slutfört installationen av nyans kan det ta lite tid för andra Hadoop-tjänster att starta. Detta kan påverka prestandan för nyanser från början. När alla tjänster har startats är nyansen helt funktionella.

1. Nyansen förstår inte Apache Tez-jobb, vilket är den aktuella standardinställningen för Hive. Om du vill använda MapReduce som Hive-körnings motor uppdaterar du skriptet så att det använder följande kommando i skriptet:

        set hive.execution.engine=mr;

1. Med Linux-kluster kan du ha ett scenario där dina tjänster körs på den primära huvudnoden medan Resource Manager kan köras på den sekundära. Ett sådant scenario kan resultera i fel (visas nedan) när du använder nyans för att visa information om jobb som körs i klustret. Du kan dock Visa jobb informationen när jobbet har slutförts.

   ![Fel exempel meddelande för nyans Portal](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Fel i nyans Portal")

   Detta beror på ett känt problem. Som en lösning kan du ändra Ambari så att den aktiva Resource Manager också körs på den primära huvudnoden.

1. Nyansen förstår WebHDFS medan HDInsight-kluster använder Azure Storage att använda `wasbs://`. Det anpassade skriptet som används med skript åtgärd installerar därför WebWasb, som är en WebHDFS-kompatibel tjänst för att kommunicera med WASB. Så även om nyans portalen säger HDFS på platser (t. ex. När du flyttar musen över **fil läsaren**) ska den tolkas som WASB.

## <a name="next-steps"></a>Nästa steg

* [Installera Apache Giraph på HDInsight-kluster](hdinsight-hadoop-giraph-install-linux.md). Använd kluster anpassning för att installera Giraph på HDInsight Hadoop-kluster. Med Giraph kan du utföra diagram bearbetning med Hadoop och det kan användas med Azure HDInsight.

* [Installera R på HDInsight-kluster](hdinsight-hadoop-r-scripts-linux.md). Installera R på HDInsight Hadoop-kluster med hjälp av kluster anpassning. R är ett språk och en miljö med öppen källkod för statistik bearbetning. Det ger hundratals inbyggda statistiska funktioner och ett eget programmeringsspråk som kombinerar aspekter av funktionell och objektorienterad programmering. Den innehåller också omfattande grafiska funktioner.
