---
title: Installera program från tredje part på Azure HDInsight
description: Lär dig hur du installerar Hadoop-program från tredje part i Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 47a634ca1c4e904cc1054db3a834483489ade0ec
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53093564"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Installera från tredje part Apache Hadoop-program på Azure HDInsight

Lär dig hur du installerar en tredjeparts- [Apache Hadoop](https://hadoop.apache.org/) på Azure HDInsight. Anvisningar om hur du installerar ett eget program finns i [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).

Ett HDInsight-program är ett program som användarna kan installera på ett HDInsight-kluster. Dessa program kan utvecklas av Microsoft, oberoende programvaruleverantörer och av dig själv.  

I följande lista visas de publicerade programmen:

* **Plattform för AtScale Intelligence** stängs ditt HDInsight-kluster i en skalbar OLAP-server. Du kan fråga miljarder rader med data interaktivt med BI-verktyg från Microsoft Excel, Power BI, Tableau Software till QlikView i programmet.
* **Cask CDAP för HDInsight** ger den första enhetliga-plattformen för stordata som minskar tiden till produktion för dataprogram och datasjöar med 80%. Det här programmet stöder endast Standard HBase 3.4-kluster.
* **DATAIKU DDS på HDInsight** kan data-experter kan skapa prototyper samt bygga och distribuera specifika tjänster som omvandlar rådata till effektfulla affärsförutsägelser.
* **Datameer** är en självbetjäning skalbar plattform för att förbereda, utforska, och styr dina data för analys går det snabbare att omvandla komplexa flerkällig data till värdefull information i business ready, leverera snabbare, smartare insikter på en Enterprise-skalan.
* **H2O artificiell intelligens för HDInsight (Beta)** H2O Sparkling Water har stöd för följande distribuerade algoritmer: GLM, Naïve Bayes, distribuerade slumpmässiga skog, Gradient Boosting-dator, djupa Neurala nätverk, Djupinlärning, K-means, PCA, Generaliserad med låg rangordnas modeller, Avvikelseidentifiering och Autoencoders.
* **Kyligence Enterprise**, drivs av Apache kylin med, ger omedelbar insyn för enorma datauppsättningar för affärsanalytiker för användare och data. Med banbrytande machine learning-teknik och intelligenta funktioner för datamodellering kan förbättrar det avsevärt produktiviteten för analys av stordata. 
* **Paxata självbetjäning förberedelse av Data**
* **Spark-jobbserver för KNIME Spark-Executor** Spark-jobbserver för KNIME Spark-Executor används för att ansluta analysplattform KNIME till HDInsight-kluster.
* **Stjärnexplosion Presto** Presto är en snabb och skalbar distribuerad SQL frågemotor. Byggt för uppdelning av lagring och beräkning, är Presto perfekt för att fråga efter data i Azure Data Lake Storage, Azure Blob Storage, SQL och NoSQL-databaser och andra datakällor.
* **Streamsets Data Collector för HDnsight** tillhandahåller en komplett integrerad utvecklingsmiljö (IDE) som du kan använda för att utforma, testa, distribuera och hantera pipelines för ”alla-till-alla”-inmatningar som kopplar ihop dataströms- och batchdata. Du kan också ta med olika interna dataströmstransformationer, allt utan att skriva anpassad kod. 
* **Striims** (uttalas ”strömma”) är en slutpunkt till slutpunkt för direktuppspelning dataintegrering + intelligence-plattformen, att aktivera kontinuerlig datainmatning, bearbetning och analyser av olika dataströmmar.
* **[Trifacta](http://www.trifacta.com/)**  kan datatekniker och analytiker att mer effektivt och förbereda varierad för dagens genom att använda maskininlärning att tillhandahålla en banbrytande användarupplevelse, arbetsflöde och arkitektur.
* **Unifi dataplattform** är en sömlöst integrerad uppsättning med självbetjänad verktyg som utformats för att ge företagsanvändare att datautmaningarna den inkrementella intäkten, minska kostnaderna och komplexiteten i driften. 
* **WANdisco Fusion HDI App** möjliggör kontinuerlig konsekvent anslutning till data ändras, oavsett var de finns. Den ger dig åtkomst till dina data när som helst och var som helst utan driftavbrott eller utan avbrott.
* **Vattenlinjen** katalogiserar organiserar och reglerar data med hjälp av AI till Märk automatiskt data med termer. Vattenlinjens företag literate catalog är en kritisk, lyckades-komponent för självbetjäning analys, efterlevnad och styrning och IT-hantering initiativ.

I artikelns instruktioner används Azure-portalen. Du kan också exportera Azure Resource Manager-mallen från portalen eller skaffa en kopia av Resource Manager-mallen från leverantörer och distribuera mallen med hjälp av Azure PowerShell och Azure klassiskt CLI.  Se [skapa Apache Hadoop-kluster på HDInsight med hjälp av Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Förutsättningar
Om du vill installera HDInsight-program i ett befintligt HDInsight-kluster måste du ha ett HDInsight-kluster. Om du vill skapa ett läser du [Skapa kluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Du kan även installera HDInsight-program när du skapar ett HDInsight-kluster.

## <a name="install-applications-to-existing-clusters"></a>Installera program i befintliga kluster
I följande procedur beskriver vi hur du installerar HDInsight-program i ett befintligt HDInsight-kluster.

**Installera ett HDInsight-program**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **HDInsight-kluster** i den vänstra menyn.
3. Klicka på ett HDInsight-kluster.  Om du inte har något måste du skapa ett först.  Mer information finns i [Skapa kluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Klicka på **Program** under kategorin **Konfigurationer**. Du kan se en lista över installerade program. Om du inte kan hitta Program innebär det att det inte finns några program för den här versionen av HDInsight-klustret.
   
    ![HDInsight-program – meny på portalen](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Klicka på **Lägg till** på menyn. Du ser en lista över befintliga HDInsight-program.
   
    ![HDInsight-program – tillgängliga program](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Klicka på någon av de tillgängliga programmen och följ sedan anvisningarna för att acceptera de juridiska villkoren.

Du kan se installationsstatusen i meddelandena på portalen (klicka på klockikonen överst på portalen). När programmet har installerats kan visas programmet på listan över installerade appar.

## <a name="install-applications-during-cluster-creation"></a>Installera program när du skapar ett kluster
Du kan välja att installera HDInsight-program när du skapar ett kluster. Under processen installeras HDInsight-program när klustret har skapats och är i körläge. Om du vill installera program när klustret skapas med hjälp av Azure portal, alternativet--anpassad – istället för standardvärdet--snabbt skapa - alternativet.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Visa en lista med installerade HDInsight-appar och deras egenskaper
På portalen kan du visa en lista över de installerade HDInsight-programmen för ett kluster och egenskaperna för varje installerat program.

**Lista HDInsight-program och deras egenskaper**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **HDInsight-kluster** i den vänstra menyn. 
3. Klicka på ett HDInsight-kluster.
4. Från **inställningar**, klickar du på **program** under den **Configuration** kategori. Den installerade appar visas till höger. 
   
    ![HDInsight-program – installerade appar](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Klicka på något av de installerade programmen för att visa egenskaperna. Egenskapslistor:
   
   * Programnamn: programnamnet.
   * Status: programmets status. 
   * Webbsida: URL till webbprogrammet som du har distribuerat till gränsnoden. Autentiseringsuppgifterna är samma som HTTP-autentiseringsuppgifterna som du har konfigurerat för klustret.
   * HTTP-slutpunkt: Autentiseringsuppgifterna är samma som HTTP-autentiseringsuppgifterna som du har konfigurerat för klustret. 
   * SSH-slutpunkt: du kan använda SSH för att ansluta till gränsnoden. SSH-autentiseringsuppgifterna är samma som SSH-autentiseringsuppgifterna som du har konfigurerat för klustret. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).
6. Om du vill ta bort ett program, högerklickar du på programmet och klicka sedan på **ta bort** på snabbmenyn.

## <a name="connect-to-the-edge-node"></a>Ansluta till gränsnoden
Du kan ansluta till gränsnoden med HTTP och SSH. Du kan hitta information om slutpunkten på [portalen](#list-installed-hdinsight-apps-and-properties). Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

Autentiseringsuppgifterna för HTTP-slutpunkten är HTTP-autentiseringsuppgifterna som du har konfigurerat för HDInsight-klustret. Autentiseringsuppgifterna för SSH-slutpunkten är SSH-autentiseringsuppgifterna som du har konfigurerat för HDInsight-klustret.

## <a name="troubleshoot"></a>Felsöka
Läs [Felsöka installationen](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Nästa steg
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md): Lär dig hur du distribuerar ett Opublicerat HDInsight-program till HDInsight.
* [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): information om hur du publicerar anpassade HDInsight-program på Azure Marketplace.
* [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx): information om hur du definierar HDInsight-program.
* [Anpassa Linux-baserade HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md): information om hur du använder skriptåtgärd till att installera fler program.
* [Skapa Linux-baserade Apache Hadoop-kluster i HDInsight med hjälp av Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Lär dig hur du anropar Resource Manager-mallar för att skapa HDInsight-kluster.
* [Använda tomma edge-noder i HDInsight](hdinsight-apps-use-edge-node.md): Information om hur du använder en tom edge-nod för att få åtkomst till HDInsight-kluster, testa HDInsight-program och vara värd för HDInsight-program.

