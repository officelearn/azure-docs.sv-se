---
title: Installera Hadoop-program från tredje part i Azure HDInsight | Microsoft Docs
description: Lär dig hur du installerar Hadoop-program från tredje part i Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: eaf5904d-41e2-4a5f-8bec-9dde069039c2
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 3ad112544a703a9b6ec37fa07cbd6df6976d5e26
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201922"
---
# <a name="install-third-party-hadoop-applications-on-azure-hdinsight"></a>Installera Hadoop-program från tredje part i Azure HDInsight

Lär dig hur du installerar en tredje parts Hadoop-programmet på Azure HDInsight. Anvisningar om hur du installerar ett eget program finns i [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).

Ett HDInsight-program är ett program som användarna kan installera på ett HDInsight-kluster. Dessa program kan utvecklas av Microsoft, oberoende programvaruleverantörer och av dig själv.  

I följande lista visas de publicerade program:

* **AtScale Intelligence Platform** stängs ditt HDInsight-kluster i en skalbar OLAP-server. Du kan fråga miljarder rader med data interaktivt med BI-verktyg från Microsoft Excel, PowerBI, Tableau programvara till QlikView i programmet.
* **Cask CDAP för HDInsight** innehåller första enhetlig integrationsplattform för stordata som minskar tid till produktion för program och data sjöar med 80%. Det här programmet stöder endast Standard HBase 3.4-kluster.
* **DATAIKU DDS på HDInsight** kan data tekniker som prototyp, skapa och distribuera hög specifika tjänster som transformera rådata till effektfulla business förutsägelser.
* **H2O styrs av datorn för HDInsight (Beta)** H2O mousserande vattenstämplar stöder följande distribuerade algoritmer: GLM, Naïve Bayes, distribuerade slumpmässiga skog, toning förstärkning datorn, djupa Neurala nätverk, djup learning K-means PCA, Generaliserad låg Rank modeller, Avvikelseidentifiering och Autoencoders.
* **Kyligence Analytics Platform** Kyligence Analytics Platform (KAP) är ett enterprise-redo datalager som tillhandahålls av Apache Kylin och Apache Hadoop, ger sekund fråga svarstid i massiv skala dataset och förenklar dataanalys för användare i verksamheten och analytiker. 
* **Förberedelse av Data Paxata självbetjäning**
* **Spark-jobbserver för KNIME Spark utföraren** Spark-jobbserver för KNIME Spark utföraren används för att ansluta KNIME Analytics Platform till HDInsight-kluster.
* **Streamsets Data Collector för HDnsight** tillhandahåller en komplett integrerad utvecklingsmiljö (IDE) som du kan använda för att utforma, testa, distribuera och hantera pipelines för ”alla-till-alla”-inmatningar som kopplar ihop dataströms- och batchdata. Du kan också ta med olika interna dataströmstransformationer, allt utan att skriva anpassad kod. 
* **[Trifacta](http://www.trifacta.com/)**  kan data engineers och analytiker effektivare utforska och förbereda olika data idag genom att använda maskininlärning att ange banbrytande användarupplevelse, arbetsflöde och arkitektur.
* **WANdisco Fusion HDI App** aktiverar kontinuerlig konsekvent anslutning till data ändras oavsett var de finns. Det ger dig åtkomst till dina data när som helst och var som helst utan driftavbrott eller några avbrott.

I artikelns instruktioner används Azure-portalen. Du kan också exportera Azure Resource Manager-mallen från portalen eller skaffa en kopia av Resource Manager-mallen från leverantörer och distribuera mallen med Azure PowerShell och Azure CLI.  Se [skapa Hadoop-kluster i HDInsight med hjälp av Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

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
6. Klicka på någon av de tillgängliga program och följ sedan anvisningarna för att acceptera de juridiska villkoren.

Du kan se installationsstatusen i meddelandena på portalen (klicka på klockikonen överst på portalen). När programmet installeras visas programmet i listan över installerade appar.

## <a name="install-applications-during-cluster-creation"></a>Installera program när du skapar ett kluster
Du kan välja att installera HDInsight-program när du skapar ett kluster. Under processen installeras HDInsight-program när klustret har skapats och är i körläge. För att installera program när klustret skapas med hjälp av Azure portal, alternativet--anpassad – istället för standard--snabbt skapa - alternativet.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Visa en lista med installerade HDInsight-appar och deras egenskaper
På portalen kan du visa en lista över de installerade HDInsight-programmen för ett kluster och egenskaperna för varje installerat program.

**Visa en lista med HDInsight-program och visa egenskaper**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **HDInsight-kluster** i den vänstra menyn. 
3. Klicka på ett HDInsight-kluster.
4. Från **inställningar**, klickar du på **program** under den **Configuration** kategori. Den installerade appar visas till höger. 
   
    ![HDInsight-program – installerade appar](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Klicka på något av de installerade programmen för att visa egenskaperna. Egenskapen visas:
   
   * Programnamn: programnamnet.
   * Status: programmets status. 
   * Webbsida: URL: en för webbprogram som du har distribuerat till kantnoden. Autentiseringsuppgifterna är samma som HTTP-autentiseringsuppgifterna som du har konfigurerat för klustret.
   * HTTP-slutpunkt: Autentiseringsuppgifterna är samma som HTTP-autentiseringsuppgifterna som du har konfigurerat för klustret. 
   * SSH-slutpunkten: du kan använda SSH för att ansluta till kantnoden. SSH-autentiseringsuppgifterna är samma som SSH-autentiseringsuppgifterna som du har konfigurerat för klustret. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).
6. Om du vill ta bort ett program, högerklicka på programmet och klicka sedan på **ta bort** på snabbmenyn.

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
* [Skapa Linux-baserade Hadoop-kluster i HDInsight med hjälp av Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Lär dig hur du anropar Resource Manager-mallar för att skapa HDInsight-kluster.
* [Använda tomma edge-noder i HDInsight](hdinsight-apps-use-edge-node.md): Information om hur du använder en tom edge-nod för att få åtkomst till HDInsight-kluster, testa HDInsight-program och vara värd för HDInsight-program.

