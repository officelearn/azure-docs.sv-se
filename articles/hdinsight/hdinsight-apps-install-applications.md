---
title: Installera program från tredje part på Azure HDInsight
description: Lär dig hur du installerar Apache Hadoop program från tredje part i Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 68bbe22a4a6f7697c2bfab291412aae85efc32e9
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70917011"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Installera från tredje part Apache Hadoop-program på Azure HDInsight

Lär dig hur du installerar en tredjeparts- [Apache Hadoop](https://hadoop.apache.org/) på Azure HDInsight. Anvisningar om hur du installerar ett eget program finns i [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).

Ett HDInsight-program är ett program som användarna kan installera på ett HDInsight-kluster. Dessa program kan utvecklas av Microsoft, oberoende programvaruleverantörer och av dig själv.  

I följande lista visas de publicerade programmen:

|Program |Kluster typ (er) | Beskrivning |
|---|---|---|
|[AtScale intelligence-plattform](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/atscale.atscale) |Hadoop |AtScale vänder ditt HDInsight-kluster till en skalbar OLAP-server, så att du kan fråga miljarder rader med data interaktivt med de BI-verktyg som du redan känner till, äger och gillar – från Microsoft Excel, Power BI, Tableau-programvara till QlikView. |
|[CDAP för HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cask.cdap-for-hdinsight) |HBase |CDAP är den första enhetliga integrerings plattformen för Big data som påskyndar tiden till värde för Hadoop och gör det möjligt att tillhandahålla självbetjänings data. Öppen källkod och utöknings bar CDAP tar bort hinder för innovation. Krav: 4 regionfiler, min D3 v2. |
|[Datameer](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/datameer.datameer) |Hadoop |Datameers självbetjänings skalbara plattform för att förbereda, utforska och hantera dina data för analys påskyndar komplexa data som är klara att användas i värdefull företags information och ger snabbare och smartare insikter i företags skala. |
|[Dataiku DSS på HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |Dataiku DSS i en Enterprise data science-plattform som gör det möjligt för data experter och dataanalytiker att samar beta för att utforma och köra nya data produkter och tjänster mer effektivt, så att rå data blir mer effektiva. |
|[WANdisco fusion HDI-app](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop, Spark, HBase, Storm, Kafka |Att hålla data konsekvent i en distribuerad miljö är en enorm data åtgärds utmaning. WANdisco fusion, en program plattform i företags klass, löser problemet genom att aktivera ostrukturerad data konsekvens i alla miljöer. |
|[H2O SparklingWater för HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O mousserande vatten stöder följande distribuerade algoritmer: GLM, naïve Bayes, distribuerad slumpmässig skog, dator för tonings förstärkning, djup neurala nätverk, djup inlärning, K--, PCA, generaliserade modeller med låg rangordning, avvikelse identifiering, Autoencoders. |
|[Striims för data integrering i real tid till HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/striim.hdinsightintegration) |Hadoop, HBase, Storm, Spark, Kafka |Striims (uttalad "Stream") är en slut punkt till slut punkts data integrering + informations plattform, vilket möjliggör kontinuerlig inmatning, bearbetning och analys av olika data strömmar. |
|[Jumbune Enterprise – Acceleratoring BigData Analytics](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |På en hög nivå hjälper Jumbune företag av, 1. Accelerera tez, MapReduce & Spark Engine-baserad Hive, Java, Scala arbets belastnings prestanda. 2. Proaktiv övervakning av Hadoop-kluster, 3. Upprätta data kvalitets hantering på distribuerat fil system. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/kyligence.kyligence) |Hadoop, HBase, Spark |Kyligence Enterprise drivs av Apache Kylin och gör det möjligt för BI på Big data. Som en Enterprise OLAP-motor på Hadoop ger Kyligence Enterprise en affärsanalytiker som konstruerar BI på Hadoop med data lager och BI-metodik som bransch standard. |
|[Stjärn explosion Presto för Azure HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/starburst.starburst-presto) |Hadoop |Presto är en snabb och skalbar distribuerad SQL-frågemotor. Byggt för uppdelning av lagring och beräkning, är Presto perfekt för att fråga efter data i Azure Data Lake Storage, Azure Blob Storage, SQL och NoSQL-databaser och andra datakällor. |
|[StreamSets data insamlare för HDInsight-moln](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop, HBase, Spark, Kafka |StreamSets data Collector är en lätt, kraftfull motor som strömmar data i real tid. Använd data insamlaren för att dirigera och bearbeta data i dina data strömmar. Den levereras med en 30-dagars utvärderings licens. |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/trifacta.tr01) |Hadoop, Spark, HBase |Trifacta Wrangler Enterprise för HDInsight stöder data datatransformering i hela företaget för alla data skalor. Kostnaden för att köra Trifacta på Azure är en kombination av Trifacta prenumerations kostnader plus Azures infrastruktur kostnader för de virtuella datorerna. |
|[Förenar data plattform](https://unifisoftware.com/platform/) |Hadoop, HBase, Storm, Spark |Den smidiga data plattformen är ett sömlöst integrerat utbud av självbetjänings data verktyg som är utformade för att ge affärs användaren möjlighet att ta itu med data utmaningar som ökar intäkterna, minska kostnaderna eller drifts komplexiteten. |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |Unravel data app för HDInsight Spark-kluster. |
|[Vatten linje AI-driven Data Catalog](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/waterline_data.waterline_data) |Spark |Vatten linje kataloger, ordnar och styr data med hjälp av AI för att automatiskt tagga data med affärs villkor. Vattenlinjens företag literate catalog är en kritisk, lyckades-komponent för självbetjäning analys, efterlevnad och styrning och IT-hantering initiativ. |

I artikelns instruktioner används Azure-portalen. Du kan också exportera Azure Resource Manager-mallen från portalen eller skaffa en kopia av Resource Manager-mallen från leverantörer och distribuera mallen med hjälp av Azure PowerShell och Azure klassiskt CLI.  Se [skapa Apache Hadoop-kluster på HDInsight med hjälp av Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Förutsättningar
Om du vill installera HDInsight-program i ett befintligt HDInsight-kluster måste du ha ett HDInsight-kluster. Om du vill skapa ett läser du [Skapa kluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Du kan även installera HDInsight-program när du skapar ett HDInsight-kluster.

## <a name="install-applications-to-existing-clusters"></a>Installera program i befintliga kluster
I följande procedur beskriver vi hur du installerar HDInsight-program i ett befintligt HDInsight-kluster.

**Installera ett HDInsight-program**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I den vänstra menyn navigerar du till **alla tjänster** > **Analytics** > **HDInsight-kluster**.
3. Välj ett HDInsight-kluster i listan.  Om du inte har något måste du skapa ett först.  Mer information finns i [Skapa kluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Under kategorin **Inställningar** väljer du **program**. Du kan se en lista över installerade program i huvud fönstret. 
   
    ![HDInsight-program – meny på portalen](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Välj **+ Lägg till** på menyn. Du kan se en lista över tillgängliga program.  Om **+ Lägg till** är nedtonad, innebär det att det inte finns några program för den här versionen av HDInsight-klustret.
   
    ![HDInsight-program – tillgängliga program](./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png)
6. Välj ett av de tillgängliga programmen och följ sedan anvisningarna för att godkänna de juridiska villkoren.

Du kan se installations status från Portal meddelanden (Välj klock ikonen överst i portalen). När programmet har installerats kan visas programmet på listan över installerade appar.

## <a name="install-applications-during-cluster-creation"></a>Installera program när du skapar ett kluster
Du kan välja att installera HDInsight-program när du skapar ett kluster. Under processen installeras HDInsight-program när klustret har skapats och är i körläge. Om du vill installera program när du skapar ett kluster med hjälp av Azure Portal använder du det **anpassade** alternativet i stället för standard alternativet för **snabb registrering** .

## <a name="list-installed-hdinsight-apps-and-properties"></a>Visa en lista med installerade HDInsight-appar och deras egenskaper
På portalen kan du visa en lista över de installerade HDInsight-programmen för ett kluster och egenskaperna för varje installerat program.

**Lista HDInsight-program och deras egenskaper**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I den vänstra menyn navigerar du till **alla tjänster** > **Analytics** > **HDInsight-kluster**.
3. Välj ett HDInsight-kluster i listan.
4. Under kategorin **Inställningar** väljer du **program**. Du kan se en lista över installerade program i huvud fönstret. 
   
    ![HDInsight-program – installerade appar](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Välj ett av de installerade programmen för att visa egenskapen. Egenskapslistor:

    |Egenskap | Beskrivning |
    |---|---|
    |Appnamn |Program namn. |
    |State |Program status. |
    |Webbsida |URL: en för det webb program som du har distribuerat till Edge-noden. Autentiseringsuppgifterna är samma som HTTP-autentiseringsuppgifterna som du har konfigurerat för klustret. |
    |SSH-slutpunkt |Du kan använda SSH för att ansluta till Edge-noden. SSH-autentiseringsuppgifterna är samma som SSH-autentiseringsuppgifterna som du har konfigurerat för klustret. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight). |
    |Beskrivning | Programbeskrivning. |

6. Om du vill ta bort ett program, högerklickar du på programmet och klicka sedan på **ta bort** på snabbmenyn.

## <a name="connect-to-the-edge-node"></a>Ansluta till gränsnoden
Du kan ansluta till gränsnoden med HTTP och SSH. Du kan hitta information om slutpunkten på [portalen](#list-installed-hdinsight-apps-and-properties). Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

Autentiseringsuppgifterna för HTTP-slutpunkten är HTTP-autentiseringsuppgifterna som du har konfigurerat för HDInsight-klustret. Autentiseringsuppgifterna för SSH-slutpunkten är SSH-autentiseringsuppgifterna som du har konfigurerat för HDInsight-klustret.

## <a name="troubleshoot"></a>Felsöka
Läs [Felsöka installationen](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Nästa steg
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md): Lär dig hur du distribuerar ett Opublicerat HDInsight-program till HDInsight.
* [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): Lär dig hur du publicerar dina anpassade HDInsight-program på Azure Marketplace.
* [MSDN Installera ett HDInsight-](https://msdn.microsoft.com/library/mt706515.aspx)program: Lär dig hur du definierar HDInsight-program.
* [Anpassa Linux-baserade HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md): information om hur du använder skriptåtgärd till att installera fler program.
* [Skapa Linux-baserade Apache Hadoop-kluster i HDInsight med hjälp av Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Lär dig hur du anropar Resource Manager-mallar för att skapa HDInsight-kluster.
* [Använda tomma edge-noder i HDInsight](hdinsight-apps-use-edge-node.md): Information om hur du använder en tom edge-nod för att få åtkomst till HDInsight-kluster, testa HDInsight-program och vara värd för HDInsight-program.

