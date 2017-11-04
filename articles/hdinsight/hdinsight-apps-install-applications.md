---
title: "Installera Hadoop-program från tredje part i Azure HDInsight | Microsoft Docs"
description: "Lär dig hur du installerar Hadoop-program från tredje part i Azure HDInsight."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: eaf5904d-41e2-4a5f-8bec-9dde069039c2
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/16/2017
ms.author: jgao
ms.openlocfilehash: b3b9d55f2d1e11156c21bc4ec5652e6d7c421db2
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="install-third-party-hadoop-applications-on-azure-hdinsight"></a>Installera Hadoop-program från tredje part i Azure HDInsight

Lär dig hur du installerar ett redan publicerade från tredje part Hadoop-program på Azure HDInsight i den här artikeln. Anvisningar om hur du installerar ett eget program finns i [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).

Ett HDInsight-program är ett program som användarna kan installera på ett Linux-baserat HDInsight-kluster. Dessa program kan utvecklas av Microsoft, oberoende programvaruleverantörer och av dig själv.  

För närvarande finns det fyra publicerade program:

* **DATAIKU DDS på HDInsight**: Dataiku DSS (Data Science Studio) är en programvara som gör att dataspecialister (dataforskare, affärsanalytiker, utvecklare med flera) kan skapa prototyper samt bygga och distribuera specifika tjänster som omvandlar rådata till effektfulla affärsförutsägelser.
* **Datameer**: [Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft) erbjuder analytiker ett interaktivt sätt att identifiera, analysera och visualisera resultat från stordata (Big Data). Hämta enkelt in fler datakällor för att identifiera nya relationer och snabbt få de svar du behöver.
* **Streamsets Data Collector för HDnsight** tillhandahåller en komplett integrerad utvecklingsmiljö (IDE) som du kan använda för att utforma, testa, distribuera och hantera pipelines för ”alla-till-alla”-inmatningar som kopplar ihop dataströms- och batchdata. Du kan också ta med olika interna dataströmstransformationer, allt utan att skriva anpassad kod. 
* **Cask CDAP för HDInsight** innehåller första enhetlig integrationsplattform för stordata som minskar tid till produktion för program och data sjöar med 80%. Det här programmet stöder endast Standard HBase 3.4-kluster.
* **H2O styrs av datorn för HDInsight (Beta)** H2O mousserande vattenstämplar stöder följande distribuerade algoritmer: GLM, Naïve Bayes, distribuerade slumpmässiga skog, toning förstärkning datorn, djupa Neurala nätverk, djupa learning, K-means, PCA, generaliserad låg rangordning modeller, Avvikelseidentifiering och Autoencoders.
* **Kyligence Analytics Platform** Kyligence Analytics Platform (KAP) är en enterprise-redo data warehouse drivs av Apache Kylin och Apache Hadoop, den ger sekund svarstid i massiv skala dataset och förenklar dataanalys för företagsanvändare och analytiker. 
* **SnapLogic Hadooplex** i SnapLogic Hadooplex körs på HDInsight ger kunder möjlighet att komma till affärsinsikter snabbare genom att tillhandahålla självbetjäning datapåfyllning och förberedelse från valfri källa för Microsoft Azure cloud-plattformen.
* **Spark-jobbserver för KNIME Spark utföraren** Spark-jobbserver för KNIME Spark utföraren används för att ansluta KNIME Analytics Platform till HDInsight-kluster.

I artikelns instruktioner används Azure-portalen. Du kan också exportera Azure Resource Manager-mallen från portalen eller skaffa en kopia av Resource Manager-mallen från leverantörer och distribuera mallen med Azure PowerShell och Azure CLI.  Mer information finns i [Skapa Linux-baserade Hadoop-kluster i HDInsight med hjälp av Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Krav
Om du vill installera HDInsight-program i ett befintligt HDInsight-kluster måste du ha ett HDInsight-kluster. Om du vill skapa ett läser du [Skapa kluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Du kan även installera HDInsight-program när du skapar ett HDInsight-kluster.

## <a name="install-applications-to-existing-clusters"></a>Installera program i befintliga kluster
I följande procedur beskriver vi hur du installerar HDInsight-program i ett befintligt HDInsight-kluster.

**Så här installerar du ett HDInsight-program**

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **HDInsight-kluster** i den vänstra menyn.  Om du inte ser det klickar du på **Fler tjänster** och sedan på **HDInsight-kluster**.
3. Klicka på ett HDInsight-kluster.  Om du inte har något måste du skapa ett först.  Mer information finns i [Skapa kluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Klicka på **Program** under kategorin **Konfigurationer**. Du kan se en lista över installerade program. Om du inte kan hitta Program innebär det att det inte finns några program för den här versionen av HDInsight-klustret.
   
    ![HDInsight-program – meny på portalen](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Klicka på **Lägg till** från bladets meny. 
   
    ![HDInsight-program – installerade appar](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps.png)
   
    Du ser en lista över befintliga HDInsight-program.
   
    ![HDInsight-program – tillgängliga program](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Klicka på ett av programmen, godkänn de juridiska villkoren och klicka sedan på **Välj**.

Du kan se installationsstatusen i meddelandena på portalen (klicka på klockikonen överst på portalen). När programmet har installerats visas det på bladet Installerade appar.

## <a name="install-applications-during-cluster-creation"></a>Installera program när du skapar ett kluster
Du kan välja att installera HDInsight-program när du skapar ett kluster. Under processen installeras HDInsight-program när klustret har skapats och är i körläge. Följande steg beskriver hur du installerar HDInsight-program när du skapar ett kluster.

**Så här installerar du ett HDInsight-program**

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **Nytt**, klicka på **Data + Analys** och sedan på **HDInsight**.
3. Ange **klusternamnet**: Det här namnet måste vara globalt unikt.
4. Klicka på **prenumeration** att välja den Azure-prenumeration som används för klustret.
5. Klicka på **Välj typ av kluster** och välj sedan:
   
   * **Klustertyp**: Om du inte vet vad du ska välja väljer du **Hadoop**. Det är den mest populära klustertypen.
   * **Operativsystem**: Välj **Linux**.
   * **Version**: Använd standardversionen om du inte vet vad du ska välja. Mer information finns i [HDInsight-klusterversioner](hdinsight-component-versioning.md).
   * **Klusternivå**: Azure HDInsight tillhandahåller molntjänster för stordata i två kategorier: Premium- och standardnivå. Mer information finns i [Klusternivåer](hdinsight-hadoop-provision-linux-clusters.md#cluster-tiers).
6. Klicka på **Program**, klicka på något av de publicerade programmen och sedan på **Välj**.
7. Klicka på **Autentiseringsuppgifter** och ange ett lösenord för administratörsanvändaren. Du måste också ange en **SSH-användarnamn** och antingen en **lösenord** eller **offentliga nyckel**, som används för att autentisera SSH-användare. Den rekommenderade metoden är att använda en offentlig nyckel. Spara konfigurationen av autentiseringsuppgifterna genom att klicka på **Välj** längst ned.
8. Klicka på **datakällan**, väljer du något av de befintliga lagringskontona eller skapa ett nytt lagringskonto som ska användas som standardkontot för lagring för klustret.
9. Klicka på **Resursgrupp** om du vill välja en befintlig resursgrupp eller på **Nytt** om du vill skapa en ny resursgrupp.
10. På bladet **Nytt HDInsight-kluster** kontrollerar du att **Fäst på startsidan** är valt och klickar sedan på **Skapa**. 

## <a name="list-installed-hdinsight-apps-and-properties"></a>Visa en lista med installerade HDInsight-appar och deras egenskaper
På portalen kan du visa en lista över de installerade HDInsight-programmen för ett kluster och egenskaperna för varje installerat program.

**Så här visar du en lista med HDInsight-program och deras egenskaper**

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **HDInsight-kluster** i den vänstra menyn.  Om du inte ser det klickar du på **Bläddra** och sedan på **HDInsight-kluster**.
3. Klicka på ett HDInsight-kluster.
4. I bladet **Inställningar** klickar du på **Program** under kategorin **Allmänt**. På bladet Installerade appar visas alla installerade program. 
   
    ![HDInsight-program – installerade appar](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Klicka på något av de installerade programmen för att visa egenskaperna. Egenskapsbladet innehåller:
   
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

