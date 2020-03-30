---
title: Installera program från tredje part på Azure HDInsight
description: Lär dig hur du installerar Apache Hadoop-program från tredje part på Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 22d27d50a7d2c304e5d1a04a9a5eaa03d2f0bfa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366350"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Installera Apache Hadoop-program från tredje part på Azure HDInsight

Lär dig hur du installerar ett [Apache Hadoop-program](https://hadoop.apache.org/) från tredje part på Azure HDInsight. Anvisningar om hur du installerar ett eget program finns i [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).

Ett HDInsight-program är ett program som användare kan installera på ett HDInsight-kluster. Dessa program kan utvecklas av Microsoft, oberoende programvaruleverantörer och av dig själv.  

Följande lista visar de publicerade programmen:

|Program |Klustertyper | Beskrivning |
|---|---|---|
|[PåScale Intelligence Platform](https://azuremarketplace.microsoft.com/marketplace/apps/atscaleinc.atscale) |Hadoop |AtScale förvandlar ditt HDInsight-kluster till en utskalnings-OLAP-server, så att du kan fråga miljarder rader med data interaktivt med hjälp av de BI-verktyg som du redan känner till, äger och älskar – från Microsoft Excel, Power BI, Tableau Software till QlikView. |
|[CDAP för HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/cask.cdap-for-hdinsight) |HBase |CDAP är den första enhetliga integrationsplattformen för stordata som accelererar tiden till värde för Hadoop och gör det möjligt för IT att tillhandahålla självbetjäningsdata. Cdap är öppen källkod och utökningsbar och undanröjer hinder för innovation. Krav: 4 Regionnoder, min D3 v2. |
|[Datameer (datameer)](https://azuremarketplace.microsoft.com/marketplace/apps/datameer.datameer) |Hadoop |Datameers skalbara plattform med självbetjäning för att förbereda, utforska och styra dina data för analys accelererar hur komplexa multisource-data omvandlas till värdefull affärsklar information och ger snabbare och smartare insikter i företagsskala. |
|[Dataiku DSS på HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Gnista |Dataiku DSS i en datavetenskapsplattform för företag som gör det möjligt för datavetare och dataanalytiker att samarbeta för att utforma och köra nya dataprodukter och datatjänster mer effektivt och omvandla rådata till effektfulla förutsägelser. |
|[WANdisco Fusion HDI App](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop, Spark, HBase, Storm, Kafka |Att hålla data konsekventa i en distribuerad miljö är en enorm utmaning för dataåtgärder. WANdisco Fusion, en programvaruplattform i företagsklass, löser problemet genom att aktivera ostrukturerad datakonsekvens i alla miljöer. |
|[H2O SparklingWater för HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O Mousserande vatten stöder följande distribuerade algoritmer: GLM, naiva fack, Distributed Random Forest, Gradient Boosting Machine, Deep Neural Networks, Deep Learning, K-means, PCA, Generalized Low Rank Models, Anomaly Detection, Autoencoders. |
|[Striim för dataintegrering i realtid till HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/striim.hdinsightintegration) |Hadoop, HBase, Storm, Spark, Kafka |Striim (uttalas "stream") är en heltäckande dataintegration + intelligensplattform som möjliggör kontinuerlig inmatning, bearbetning och analys av olika dataströmmar. |
|[Jumbune Enterprise-accelererande BigData Analytics](https://azuremarketplace.microsoft.com/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Gnista |På en hög nivå bistår Jumbune företag med, 1. Accelererande Tez, MapReduce & Spark motorbaserade Hive, Java, Scala arbetsbelastning prestanda. 2. Proaktiv Hadoop Cluster Monitoring, 3. Upprättande av datakvalitetshantering på distribuerat filsystem. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/kyligence.kyligence) |Hadoop, HBase, Gnista |Drivs av Apache Kylin, Aktiverar Kyligence Enterprise BI på Big Data. Som ett företag OLAP-motor på Hadoop ger Kyligence Enterprise affärsanalytiker till arkitekt BI på Hadoop med branschstandard datalager och BI-metodik. |
|[Starburst Presto för Azure HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/starburst.starburst-presto) |Hadoop |Presto är en snabb och skalbar distribuerad SQL-frågemotor. Presto är utformat för separation av lagring och beräkning och är perfekt för att fråga data i Azure Data Lake Storage, Azure Blob Storage, SQL- och NoSQL-databaser och andra datakällor. |
|[StreamSets datainsamlare för HDInsight Cloud](https://azuremarketplace.microsoft.com/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop, HBase, Spark, Kafka |StreamSets Data Collector är en lätt, kraftfull motor som strömmar data i realtid. Använd datainsamlare för att dirigera och bearbeta data i dina dataströmmar. Den levereras med en 30 dagars provlicens. |
|[Trifacta Wrangler Företag](https://azuremarketplace.microsoft.com/marketplace/apps/trifacta.tr01) |Hadoop, Gnista, HBase |Trifacta Wrangler Enterprise för HDInsight stöder datakäbbel i hela företaget för alla dataskalor. Kostnaden för att köra Trifacta på Azure är en kombination av Trifacta-prenumerationskostnader plus Azure-infrastrukturkostnaderna för de virtuella datorerna. |
|[Enhetlig dataplattform](https://unifisoftware.com/platform/) |Hadoop, HBase, Storm, Gnista |Unifi Data Platform är en sömlöst integrerad uppsättning självbetjäningsdataverktyg som utformats för att ge företagsanvändaren möjlighet att hantera datautmaningar som ökar intäkterna, minskar kostnaderna eller komplexiteten i driften. |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |Unravel Data app för HDInsight Spark kluster. |
|[Vattenlinje AI-driven datakatalog](https://azuremarketplace.microsoft.com/marketplace/apps/waterline_data.waterline_data) |Spark |Waterline kataloger, organiserar och styr data med hjälp av AI för att automatiskt tagga data med affärsvillkor. Waterlines affärskunniga katalog är en viktig, framgångsrik komponent för självbetjäningsanalys, efterlevnad och styrning samt IT-hanteringsinitiativ. |

I artikelns instruktioner används Azure-portalen. Du kan också exportera Azure Resource Manager-mallen från portalen eller hämta en kopia av Resource Manager-mallen från leverantörer och använda Azure PowerShell och Azure Classic CLI för att distribuera mallen.  Se [Skapa Apache Hadoop-kluster på HDInsight med hjälp av Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Krav
Om du vill installera HDInsight-program i ett befintligt HDInsight-kluster måste du ha ett HDInsight-kluster. Om du vill skapa ett läser du [Skapa kluster](hadoop/apache-hadoop-linux-tutorial-get-started.md). Du kan även installera HDInsight-program när du skapar ett HDInsight-kluster.

## <a name="install-applications-to-existing-clusters"></a>Installera program i befintliga kluster
I följande procedur beskriver vi hur du installerar HDInsight-program i ett befintligt HDInsight-kluster.

**Installera ett HDInsight-program**

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Från den vänstra menyn navigerar du till **Alla tjänster** > **Analytics** > **HDInsight-kluster**.
3. Välj ett HDInsight-kluster i listan.  Om du inte har något måste du skapa ett först.  Mer information finns i [Skapa kluster](hadoop/apache-hadoop-linux-tutorial-get-started.md).
4. Under kategorin **Inställningar** väljer du **Program**. Du kan se en lista över installerade program i huvudfönstret. 
   
    ![HDInsight-program – meny på portalen](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Välj **+Lägg till** på menyn. Du kan se en lista över tillgängliga program.  Om **+Lägg till** är nedtonat betyder det att det inte finns några program för den här versionen av HDInsight-klustret.
   
    ![HDInsight-program – tillgängliga program](./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png)
6. Välj ett av de tillgängliga programmen och följ sedan instruktionerna för att acceptera de juridiska villkoren.

Du kan se installationsstatus från portalmeddelanden (välj klockikonen högst upp på portalen). När programmet har installerats visas programmet i listan Installerade appar.

## <a name="install-applications-during-cluster-creation"></a>Installera program när du skapar ett kluster

Du kan välja att installera HDInsight-program när du skapar ett kluster. Under processen installeras HDInsight-program när klustret har skapats och är i körläge. Om du vill installera program under klusterskapande med hjälp av Azure-portalen väljer du **+ Lägg till program**på fliken Konfiguration + **prissättning** .

![Konfigurationsprogram för Azure Portal-kluster](./media/hdinsight-apps-install-applications/azure-portal-cluster-configuration-applications.png)

## <a name="list-installed-hdinsight-apps-and-properties"></a>Visa en lista med installerade HDInsight-appar och deras egenskaper
På portalen kan du visa en lista över de installerade HDInsight-programmen för ett kluster och egenskaperna för varje installerat program.

**Lista HDInsight-program- och visningsegenskaper**

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Från den vänstra menyn navigerar du till **Alla tjänster** > **Analytics** > **HDInsight-kluster**.
3. Välj ett HDInsight-kluster i listan.
4. Under kategorin **Inställningar** väljer du **Program**. Du kan se en lista över installerade program i huvudfönstret. 
   
    ![HDInsight-program – installerade appar](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Välj ett av de installerade programmen som ska visas. Egenskapen listar:

    |Egenskap | Beskrivning |
    |---|---|
    |Appnamn |Programmets namn. |
    |Status |Programstatus. |
    |Webbsida |URL:en för webbprogrammet som du har distribuerat till kantnoden. Autentiseringsuppgifterna är samma som HTTP-autentiseringsuppgifterna som du har konfigurerat för klustret. |
    |SSH-slutpunkt |Du kan använda SSH för att ansluta till kantnoden. SSH-autentiseringsuppgifterna är samma som SSH-autentiseringsuppgifterna som du har konfigurerat för klustret. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight). |
    |Beskrivning | Programbeskrivning. |

6. Om du vill ta bort ett program högerklickar du på programmet och klickar sedan på **Ta bort** från snabbmenyn.

## <a name="connect-to-the-edge-node"></a>Ansluta till gränsnoden
Du kan ansluta till gränsnoden med HTTP och SSH. Du kan hitta information om slutpunkten på [portalen](#list-installed-hdinsight-apps-and-properties). Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

Autentiseringsuppgifterna för HTTP-slutpunkten är HTTP-autentiseringsuppgifterna som du har konfigurerat för HDInsight-klustret. Autentiseringsuppgifterna för SSH-slutpunkten är SSH-autentiseringsuppgifterna som du har konfigurerat för HDInsight-klustret.

## <a name="troubleshoot"></a>Felsöka
Läs [Felsöka installationen](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Nästa steg
* [Installera anpassade HDInsight-program:](hdinsight-apps-install-custom-applications.md)lär dig hur du distribuerar ett opublicerat HDInsight-program till HDInsight.
* [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): information om hur du publicerar anpassade HDInsight-program på Azure Marketplace.
* [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx): information om hur du definierar HDInsight-program.
* [Anpassa Linux-baserade HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md): information om hur du använder skriptåtgärd till att installera fler program.
* [Skapa Linux-baserade Apache Hadoop-kluster i HDInsight med hjälp av Resource Manager-mallar:](hdinsight-hadoop-create-linux-clusters-arm-templates.md)lär dig hur du anropar Resource Manager-mallar för att skapa HDInsight-kluster.
* [Använda tomma edge-noder i HDInsight](hdinsight-apps-use-edge-node.md): Information om hur du använder en tom edge-nod för att få åtkomst till HDInsight-kluster, testa HDInsight-program och vara värd för HDInsight-program.

