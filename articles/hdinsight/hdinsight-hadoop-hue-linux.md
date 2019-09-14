---
title: Nyans med Hadoop på HDInsight Linux-baserade kluster – Azure
description: Lär dig hur du installerar nyans i HDInsight-kluster och använder tunnlar för att dirigera begär anden till nyans. Använd nyans för att bläddra i lagring och köra Hive eller gris.
keywords: nyans Hadoop
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: hrasheed
ms.openlocfilehash: 67f338b583ef428b8dd04e859a5204fd708ce434
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962017"
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
> Anpassade komponenter får kommersiellt rimlig support för att hjälpa dig att ytterligare felsöka problemet. Detta kan resultera i att lösa problemet eller be dig att engagera tillgängliga kanaler för tekniken med öppen källkod där djupgående expertis för tekniken hittas. Det finns till exempel många community-platser som kan användas, t. ex.: [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Apache-projekt har även projekt webbplatser [https://apache.org](https://apache.org)på, till exempel: [Hadoop](https://hadoop.apache.org/).
>
>

## <a name="install-hue-using-script-actions"></a>Installera nyans med skript åtgärder

Skriptet för att installera nyans i ett Linux-baserat HDInsight-kluster är tillgängligt https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh på. Du kan använda det här skriptet för att installera nyanser i kluster med antingen Azure Storage blobbar (WASB) eller Azure Data Lake Storage som standard lagring.

Det här avsnittet innehåller anvisningar om hur du använder skriptet när du konfigurerar klustret med hjälp av Azure Portal.

> [!NOTE]  
> Azure PowerShell kan du använda Azures klassiska CLI, HDInsight .NET SDK eller Azure Resource Manager mallar även för att tillämpa skript åtgärder. Du kan också använda skript åtgärder för att redan köra kluster. Mer information finns i [Anpassa HDInsight-kluster med skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Starta etableringen av ett kluster med hjälp av stegen i [Etablera HDInsight-kluster på Linux](hdinsight-hadoop-provision-linux-clusters.md), men Slutför inte etablering.

   > [!NOTE]  
   > Om du vill installera nyans i HDInsight-kluster är den rekommenderade huvudnoden-storleken minst A4 (8 kärnor, 14 GB minne).
   >
   >
2. På bladet **valfri konfiguration** väljer du **skript åtgärder**och anger den information som visas nedan:

    ![Ange skript åtgärds parametrar för nyans](./media/hdinsight-hadoop-hue-linux/hdi-hue-script-action.png "Ange skript åtgärds parametrar för nyans")

   * **NAMN**: Ange ett eget namn för skript åtgärden.
   * **SKRIPT-URI**: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
   * **HUVUD**: Markera det här alternativet.
   * **ARBETARE**: Låt den vara tom.
   * **ZOOKEEPER**: Låt den vara tom.
   * **PARAMETRAR**: Låt den vara tom.
3. Klicka på knappen **Välj** i slutet av **skript åtgärderna**för att spara konfigurationen. Använd slutligen knappen **Välj** längst ned i det **valfria konfigurations** bladet för att spara den valfria konfigurations informationen.
4. Fortsätt att etablera klustret enligt beskrivningen i [Etablera HDInsight-kluster i Linux](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="use-hue-with-hdinsight-clusters"></a>Använda nyans med HDInsight-kluster

SSH-tunnlar är det enda sättet att få åtkomst till en nyans i klustret när den körs. Tunnel trafik via SSH gör att trafiken går direkt till huvudnoden i klustret där nyansen körs. När klustret har slutfört etableringen använder du följande steg för att använda nyans i ett HDInsight Linux-kluster.

> [!NOTE]  
> Vi rekommenderar att du använder Firefox webbläsare för att följa anvisningarna nedan.
>
>

1. Använd informationen i [använda SSH-tunnlar för att komma åt Apache Ambari Web UI, ResourceManager, JobHistory, NameNode, Oozie och andra webb gränssnitt](hdinsight-linux-ambari-ssh-tunnel.md) för att skapa en SSH-tunnel från klient systemet till HDInsight-klustret, och konfigurera sedan webbläsaren att använda SSH-tunnel som proxy.

2. När du har skapat en SSH-tunnel och konfigurerat webbläsaren för att dirigera trafik genom den, måste du hitta värd namnet för den primära huvudnoden. Du kan göra detta genom att ansluta till klustret med SSH på port 22. Till exempel, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` där **användar** namn är ditt SSH-användarnamn och **kluster** namn är namnet på klustret.

    Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

3. När du är ansluten använder du följande kommando för att hämta det fullständigt kvalificerade domän namnet för den primära huvudnoden:

        hostname -f

    Detta kommer att returnera ett namn som liknar följande:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Detta är värd namnet för den primära huvudnoden där nyans webbplatsen finns.
4. Använd webbläsaren för att öppna nyans portalen på http:\//hostname: 8888. Ersätt HOSTNAME med det namn som du fick i föregående steg.

   > [!NOTE]  
   > När du loggar in för första gången uppmanas du att skapa ett konto för att logga in på nyans portalen. De autentiseringsuppgifter som du anger här kommer att begränsas till portalen och är inte relaterade till de autentiseringsuppgifter för administratörer eller SSH-användare som du angav när klustret etableras.
   >
   >

    ![Logga in på nyans-portalen](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Ange autentiseringsuppgifter för nyans-portalen")

### <a name="run-a-hive-query"></a>Köra en Hive-fråga
1. Klicka på **Frågeredigeraren**på nyans-portalen och klicka sedan på **Hive** för att öppna Hive-redigeraren.

    ![Använd Hive](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Använd Hive")
2. På fliken **Assist** , under **databas**, bör du se **hivesampletable**. Det här är en exempel tabell som levereras med alla Hadoop-kluster i HDInsight. Ange en exempel fråga i den högra rutan och se utdata på fliken **resultat** i fönstret nedan, som visas i skärmdumpen.

    ![Kör Hive-fråga](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Kör Hive-fråga")

    Du kan också använda fliken **diagram** för att visa en visuell representation av resultatet.

### <a name="browse-the-cluster-storage"></a>Bläddra i kluster lagringen
1. Från nyans-portalen klickar du på **fil läsare** i det övre högra hörnet på Meny raden.
2. Som standard öppnas fil läsaren i **/User/myuser** -katalogen. Klicka på snedstrecket direkt före användar katalogen i sökvägen för att gå till roten för den Azure Storage-behållare som är associerad med klustret.

    ![Använd fil webbläsare](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "Använd fil webbläsare")
3. Högerklicka på en fil eller mapp för att se tillgängliga åtgärder. Använd knappen **Ladda upp** i det högra hörnet för att ladda upp filer till den aktuella katalogen. Använd knappen **nytt** för att skapa nya filer eller kataloger.

> [!NOTE]  
> Filen för nyans-filen kan bara visa innehållet i standard behållaren som är associerad med HDInsight-klustret. Eventuella ytterligare lagrings konton/behållare som du kanske har associerat med klustret kommer inte att vara tillgängliga via fil läsaren. Dock är de ytterligare behållare som är kopplade till klustret alltid tillgängliga för Hive-jobben. Om du till exempel anger kommandot `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` i Hive-redigeraren kan du även se innehållet i ytterligare behållare. I det här kommandot är **newcontainer** inte standard behållaren som är associerad med ett kluster.
>
>

## <a name="important-considerations"></a>Viktiga överväganden
1. Skriptet som används för att installera nyans installerar bara det på den primära huvudnoden i klustret.

2. Under installationen startas flera Hadoop-tjänster (HDFS, garn, MR2, Oozie) för att uppdatera konfigurationen. När skriptet har slutfört installationen av nyans kan det ta lite tid för andra Hadoop-tjänster att starta. Detta kan påverka prestandan för nyanser från början. När alla tjänster har startats är nyansen helt funktionella.
3. Nyansen förstår inte Apache Tez-jobb, vilket är den aktuella standardinställningen för Hive. Om du vill använda MapReduce som Hive-körnings motor uppdaterar du skriptet så att det använder följande kommando i skriptet:

        set hive.execution.engine=mr;

4. Med Linux-kluster kan du ha ett scenario där dina tjänster körs på den primära huvudnoden medan Resource Manager kan köras på den sekundära. Ett sådant scenario kan resultera i fel (visas nedan) när du använder nyans för att visa information om jobb som körs i klustret. Du kan dock Visa jobb informationen när jobbet har slutförts.

   ![Fel i nyans Portal](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Fel i nyans Portal")

   Detta beror på ett känt problem. Som en lösning kan du ändra Ambari så att den aktiva Resource Manager också körs på den primära huvudnoden.
5. Nyansen förstår WebHDFS medan HDInsight-kluster använder Azure Storage `wasb://`med hjälp av. Det anpassade skriptet som används med skript åtgärd installerar därför WebWasb, som är en WebHDFS-kompatibel tjänst för att kommunicera med WASB. Så även om nyans portalen säger HDFS på platser (t. ex. När du flyttar musen över **fil läsaren**) ska den tolkas som WASB.

## <a name="next-steps"></a>Nästa steg
* [Installera Apache Giraph på HDInsight-kluster](hdinsight-hadoop-giraph-install-linux.md). Använd kluster anpassning för att installera Giraph på HDInsight Hadoop-kluster. Med Giraph kan du utföra diagram bearbetning med Hadoop och det kan användas med Azure HDInsight.
* [Installera R på HDInsight-kluster](hdinsight-hadoop-r-scripts-linux.md). Installera R på HDInsight Hadoop-kluster med hjälp av kluster anpassning. R är ett språk och en miljö med öppen källkod för statistik bearbetning. Det ger hundratals inbyggda statistiska funktioner och ett eget programmeringsspråk som kombinerar aspekter av funktionell och objektorienterad programmering. Den innehåller också omfattande grafiska funktioner.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
