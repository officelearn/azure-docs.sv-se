---
title: Installera program från tredje part på Azure HDInsight
description: Lär dig hur du installerar Hadoop-program från tredje part i Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: hrasheed
ms.openlocfilehash: b916cbf690544d5887569e052a0456ca952140c8
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409542"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Installera från tredje part Apache Hadoop-program på Azure HDInsight

Lär dig hur du installerar en tredjeparts- [Apache Hadoop](https://hadoop.apache.org/) på Azure HDInsight. Anvisningar om hur du installerar ett eget program finns i [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).

Ett HDInsight-program är ett program som användarna kan installera på ett HDInsight-kluster. Dessa program kan utvecklas av Microsoft, oberoende programvaruleverantörer och av dig själv.  

I följande lista visas de publicerade programmen:

|Tillämpningsprogram |Kluster-typer | Beskrivning |
|---|---|---|
|[AtScale Intelligence-plattform](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/atscale.atscale) |Hadoop |AtScale har visat ditt HDInsight-kluster i en skalbar OLAP-servern, så att du kan fråga miljarder rader med data interaktivt med hjälp av BI-verktyg du redan vet, äger och gillar – från Microsoft Excel, Power BI, Tableau Software till QlikView. |
|[CDAP för HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cask.cdap-for-hdinsight) |Hbase |CDAP är den första enhetliga plattformen för big data som går det snabbare tid till värde för Hadoop och möjliggör för IT att tillhandahålla självbetjäning data. Öppen och utökningsbar, CDAP tar bort hinder för innovation. Krav: 4 region noder i min D3 v2. |
|[Datameer](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/datameer.datameer) |Hadoop |Datameers självbetjäning skalbar plattform för att förbereda, utforska och styr dina data för analys går det snabbare att omvandla komplexa flerkällig data till värdefull information i business ready, leverera snabbare, smartare insikter på en företagsnivå. |
|[Dataiku DSS på HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |Dataiku DSS på en enterprise data science-plattform som gör att datavetare och dataanalytiker samarbetar för att designa och köra den nya data produkter och tjänster mer effektivt omvandla rådata till effektfulla förutsägelser. |
|[WANdisco Fusion HDI App](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop, Spark,HBase,Storm,Kafka |Som data förblir konsekventa i en distribuerad miljö är en utmaning för åtgärder av massiva data. WANdisco Fusion, en plattform för företagsklass, löser detta problem genom att aktivera konsekvens för Ostrukturerade data i alla miljöer. |
|[H2O SparklingWater för HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O Sparkling Water stöder följande distribuerade algoritmer: GLM, Naïve Bayes, distribuerade slumpmässiga skog, Gradient Boosting-dator, djupa Neurala nätverk, Deep learning K-means, PCA, generaliserad låg rangordning modeller, Avvikelseidentifiering, Autoencoders. |
|[Striims för realtidsdata Integration med HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/striim.hdinsightintegration) |Hadoop,HBase,Storm,Spark,Kafka |Striims (uttalas ”stream”) är en slutpunkt till slutpunkt dataintegrering + intelligence-plattform för direktuppspelning, aktivera kontinuerlig datainmatning, bearbetning och analyser av olika dataströmmar. |
|[Jumbune Enterprise-Accelerating BigData Analytics](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |På en hög nivå hjälper Jumbune företag med 1. Påskynda Tez bygger MapReduce och Spark-motorn Hive, Java, Scala arbetsbelastningens prestanda. 2. Proaktiv Hadoop-kluster som övervakning, 3. Etablerar datakvaliteten hantering på distribuerat filsystem. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/kyligence.kyligence) |Hadoop, HBase, Spark |Drivs av Apache kylin med och kan Kyligence Enterprise BI på Stordata. Som en enterprise OLAP-motorn på Hadoop och ger Kyligence Enterprise affärsanalytiker arkitekturen BI på Hadoop med branschstandard data warehouse och BI-metod. |
|[Spark-jobbserver för KNIME Spark-Executor](https://azuremarketplace.microsoft.com/marketplace/apps/knime.spark-job-server-for-knime-spark-executor) |Spark |Spark-jobbserver för KNIME Spark-Executor används för att ansluta analysplattform KNIME till HDInsight-kluster. |
|[Stjärnexplosion Presto för Azure HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/starburst.starburst-presto) |Hadoop |Presto är en snabb och skalbar distribuerad SQL-frågemotor. Byggt för uppdelning av lagring och beräkning, är Presto perfekt för att fråga efter data i Azure Data Lake Storage, Azure Blob Storage, SQL och NoSQL-databaser och andra datakällor. |
|[StreamSets Data Collector för HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop,HBase,Spark,Kafka |StreamSets Data Collector är en enkel, kraftfull motor som strömmar data i realtid. Använda för datainsamling för att dirigera och bearbeta data i din dataströmmar. Den levereras med en 30-dagars utvärderingslicens. |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/trifacta.tr01) |Hadoop, Spark,HBase |Trifacta Wrangler Enterprise för HDInsight har stöd för företagsomfattande data experimenteringsfunktioner för alla skalor av data. Kostnaden för att köra Trifacta på Azure är en kombination av prenumerationskostnader för Trifacta plus Azure-infrastrukturkostnader för de virtuella datorerna. |
|[Unifi dataplattform](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/unifi-software.unifi-data-platform) |Hadoop, HBase, Storm, Spark |Unifi Data Platform är en sömlöst integrerad uppsättning med självbetjänad verktyg som utformats för att underlätta den inkrementella intäkten för affärsanvändare att datautmaningarna, minska kostnaderna och komplexiteten i driften. |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |Unravel Data appen för HDInsight Spark-kluster. |
|[Vattenlinjen AI-drivna datakatalog](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/waterline_data.waterline_data) |Spark |Vattenlinjen katalogiserar organiserar och reglerar data med hjälp av AI till automatisk märkning data med termer. Vattenlinjens företag literate catalog är en kritisk, lyckades-komponent för självbetjäning analys, efterlevnad och styrning och IT-hantering initiativ. |

I artikelns instruktioner används Azure-portalen. Du kan också exportera Azure Resource Manager-mallen från portalen eller skaffa en kopia av Resource Manager-mallen från leverantörer och distribuera mallen med hjälp av Azure PowerShell och Azure klassiskt CLI.  Se [skapa Apache Hadoop-kluster på HDInsight med hjälp av Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Förutsättningar
Om du vill installera HDInsight-program i ett befintligt HDInsight-kluster måste du ha ett HDInsight-kluster. Om du vill skapa ett läser du [Skapa kluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Du kan även installera HDInsight-program när du skapar ett HDInsight-kluster.

## <a name="install-applications-to-existing-clusters"></a>Installera program i befintliga kluster
I följande procedur beskriver vi hur du installerar HDInsight-program i ett befintligt HDInsight-kluster.

**Installera ett HDInsight-program**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I den vänstra menyn navigerar du till **alla tjänster** > **Analytics** > **HDInsight-kluster**.
3. Välj ett HDInsight-kluster i listan.  Om du inte har något måste du skapa ett först.  Mer information finns i [Skapa kluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Under den **inställningar** kategori, väljer **program**. Du kan se en lista över installerade program i huvudfönstret. 
   
    ![HDInsight-program – meny på portalen](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Välj **+ Lägg till** på menyn. Du kan se en lista över tillgängliga program.  Om **+ Lägg till** är nedtonat, att innebär att det inte finns några program för den här versionen av HDInsight-klustret.
   
    ![HDInsight-program – tillgängliga program](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Välj något av de tillgängliga programmen och följ sedan anvisningarna för att acceptera de juridiska villkoren.

Du kan se installationsstatus meddelandena på portalen (Välj på klockikonen överst i portalen). När programmet har installerats kan visas programmet på listan över installerade appar.

## <a name="install-applications-during-cluster-creation"></a>Installera program när du skapar ett kluster
Du kan välja att installera HDInsight-program när du skapar ett kluster. Under processen installeras HDInsight-program när klustret har skapats och är i körläge. Om du vill installera program när klustret skapas med hjälp av Azure portal ska du använda den **anpassade** alternativet istället för standardvärdet **Snabbregistrering** alternativet.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Visa en lista med installerade HDInsight-appar och deras egenskaper
På portalen kan du visa en lista över de installerade HDInsight-programmen för ett kluster och egenskaperna för varje installerat program.

**Lista HDInsight-program och deras egenskaper**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I den vänstra menyn navigerar du till **alla tjänster** > **Analytics** > **HDInsight-kluster**.
3. Välj ett HDInsight-kluster i listan.
4. Under den **inställningar** kategori, väljer **program**. Du kan se en lista över installerade program i huvudfönstret. 
   
    ![HDInsight-program – installerade appar](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Välj en av de installerade programmen att visa egenskaperna. Egenskapslistor:

    |Egenskap  | Beskrivning |
    |---|---|
    |Appnamn |Programnamnet. |
    |Status |Programmets status. |
    |Webbsida |URL till webbprogrammet som du har distribuerat till gränsnoden. Autentiseringsuppgifterna är samma som HTTP-autentiseringsuppgifterna som du har konfigurerat för klustret. |
    |SSH-slutpunkt |Du kan använda SSH för att ansluta till gränsnoden. SSH-autentiseringsuppgifterna är samma som SSH-autentiseringsuppgifterna som du har konfigurerat för klustret. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight). |
    |Beskrivning | Beskrivning av programmet. |

6. Om du vill ta bort ett program, högerklickar du på programmet och klicka sedan på **ta bort** på snabbmenyn.

## <a name="connect-to-the-edge-node"></a>Ansluta till gränsnoden
Du kan ansluta till gränsnoden med HTTP och SSH. Du kan hitta information om slutpunkten på [portalen](#list-installed-hdinsight-apps-and-properties). Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

Autentiseringsuppgifterna för HTTP-slutpunkten är HTTP-autentiseringsuppgifterna som du har konfigurerat för HDInsight-klustret. Autentiseringsuppgifterna för SSH-slutpunkten är SSH-autentiseringsuppgifterna som du har konfigurerat för HDInsight-klustret.

## <a name="troubleshoot"></a>Felsöka
Läs [Felsöka installationen](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Nästa steg
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md): Lär dig hur du distribuerar ett Opublicerat HDInsight-program till HDInsight.
* [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): Lär dig hur du publicerar dina anpassade HDInsight-program på Azure Marketplace.
* [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx): Lär dig hur du definierar HDInsight-program.
* [Anpassa Linux-baserade HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md): information om hur du använder skriptåtgärd till att installera fler program.
* [Skapa Linux-baserade Apache Hadoop-kluster i HDInsight med hjälp av Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Lär dig hur du anropar Resource Manager-mallar för att skapa HDInsight-kluster.
* [Använda tomma edge-noder i HDInsight](hdinsight-apps-use-edge-node.md): Information om hur du använder en tom edge-nod för att få åtkomst till HDInsight-kluster, testa HDInsight-program och vara värd för HDInsight-program.

