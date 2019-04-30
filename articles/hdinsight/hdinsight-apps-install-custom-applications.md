---
title: Installera egna anpassade Apache Hadoop-program i Azure HDInsight
description: Ta reda på hur du installerar HDInsight-program på HDInsight-appar.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: ec2e53624ad48650f922439c5f16d1cf1f25c280
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097260"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>Installera anpassade Apache Hadoop-program i Azure HDInsight

I den här artikeln får du lära dig hur du installerar en [Apache Hadoop](https://hadoop.apache.org/) på Azure HDInsight, som inte har publicerats på Azure Portal. Programmet som du ska installera i den här artikeln är [Hue](https://gethue.com/).

Ett HDInsight-program är ett program som användarna kan installera på ett Linux-baserat HDInsight-kluster.  Dessa program kan utvecklas av Microsoft, oberoende programvaruleverantörer och av dig själv.  

Andra relaterade artiklar:

* [Installera HDInsight-program](hdinsight-apps-install-applications.md): Lär dig hur du installerar ett HDInsight-program till dina kluster.
* [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): Lär dig hur du publicerar dina anpassade HDInsight-program på Azure Marketplace.
* [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx): Lär dig hur du definierar HDInsight-program.

## <a name="prerequisites"></a>Nödvändiga komponenter
Om du vill installera HDInsight-program i ett befintligt HDInsight-kluster måste du ha ett HDInsight-kluster. Om du vill skapa ett läser du [Skapa kluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Du kan även installera HDInsight-program när du skapar ett HDInsight-kluster.

## <a name="install-hdinsight-applications"></a>Installera HDInsight-program
HDInsight-program kan installeras när du skapar ett kluster eller i ett befintligt HDInsight-kluster. Hur du definierar Azure Resource Manager-mallar finns i [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx).

Filer som behövs för att distribuera programmet (Hue):

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): Resource Manager-mall för att installera programmet HDInsight. Se [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx) för att utveckla en egen Resource Manager-mall.
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): Den skriptåtgärd som anropas av Resource Manager-mallen för konfiguration av kantnoden.
* [Hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): Hue binära filen som anropas från hui-install_v0.sh.
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): Hue binära filen som anropas från hui-install_v0.sh.
* [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): En exempelwebbapp (Tomcat) som anropas från hui-install_v0.sh.

**Installera Hue i ett befintligt HDInsight-kluster**

1. Klicka på följande bild för att logga in på Azure och öppna Resource Manager-mallen i Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/deploy-to-azure.png" alt="Deploy to Azure"></a>

    När du klickar på den här knappen öppnas en Resource Manager-mall i Azure Portal.  Resource Manager-mallen finns på [ https://github.com/hdinsight/Iaas-Applications/tree/master/Hue ](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue).  Information om hur du skriver Resource Manager-mallen finns [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx).
2. Skriv följande på bladet **Parametrar**:

   * **Klusternamn**: Ange namnet på klustret där du vill installera programmet. Det här klustret måste vara ett befintligt kluster.
3. Spara parametrarna genom att klicka på **OK**.
4. I bladet **Anpassad distribution** anger du **Resursgrupp**.  Resursgruppen är en container som grupperar klustret, det beroende lagringskontot och andra resurser. Du måste använda samma resursgrupp som klustret.
5. Klicka på **Juridiska villkor** och sedan på **Skapa**.
6. Kontrollera att kryssrutan **Fäst på instrumentpanelen** är markerad och klicka på **Skapa**. Du kan se installationsstatus via ikonen som är fäst på portalens instrumentpanel och i portalmeddelandet (klicka på klockikonen överst i portalen).  Det tar cirka 10 minuter att installera programmet.

**Installera Hue när du skapar ett kluster**

1. Klicka på följande bild för att logga in på Azure och öppna Resource Manager-mallen i Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/deploy-to-azure.png" alt="Deploy to Azure"></a>

    När du klickar på den här knappen öppnas en Resource Manager-mall i Azure Portal.  Resource Manager-mallen finns på [ https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json ](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json).  Information om hur du skriver Resource Manager-mallen finns [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx).
2. Följ anvisningarna för att skapa kluster och installera Hue. Mer information om hur du skapar HDInsight-kluster finns i [Skapa Linux-baserade Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Utöver Azure-portalen kan du också använda [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) och [klassiska Azure-CLI](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli) du anropar Resource Manager-mallar.

## <a name="validate-the-installation"></a>Verifiera installationen
Du kan validera programinstallationen genom att kontrollera statusen för programmet på Azure Portal. Dessutom kan du verifiera att alla HTTP-slutpunkter visats som förväntat och webbsidan om det finns en:

**Öppna Hue-portalen**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **HDInsight-kluster** i den vänstra menyn.  Om du inte ser det klickar du på **Bläddra** och sedan på **HDInsight-kluster**.
3. Klicka på klustret där du installerade programmet.
4. I bladet **Inställningar** klickar du på **Program** under kategorin **Allmänt**. Du kommer att se **hue** listat i bladet **Installerade appar**.
5. Klicka på **hue** i listan om du vill visa egenskaperna.  
6. Klicka på webbsidelänken och verifiera webbplatsen; Öppna HTTP-slutpunkt i en webbläsare och validera Hue-webbgränssnittet, öppna SSH-slutpunkten med hjälp av SSH. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

## <a name="troubleshoot-the-installation"></a>Felsöka installationen
Du kan kontrollera programmets installationsstatus i portalmeddelandet (klicka på klockikonen överst i portalen).

Om en programinstallation misslyckas kan du se felmeddelanden visas och felsökningsinformation på tre ställen:

* HDInsight-program: allmän felinformation.

    Öppna klustret från portalen och klicka på Program i bladet Inställningar:

    ![hdinsight-program programinstallationsfel](./media/hdinsight-apps-install-applications/hdinsight-apps-error.png)
* HDInsight-skriptåtgärder: Om den HDInsight-programmets felmeddelande anger ett skript har misslyckats, visas mer information om i skriptåtgärdsfönstret.

    Klicka på Skriptåtgärd i bladet Inställningar. I historiken för skriptåtgärder visas felmeddelanden

    ![hdinsight-program skriptåtgärdsfel](./media/hdinsight-apps-install-applications/hdinsight-apps-script-action-error.png)
* Ambari-webbgränssnittet: Om installationsskriptet var orsaken till felet kan du använda Ambari-Webbgränssnittet för att kontrollera de fullständig loggarna om installationsskripten.

    Mer information finns i [Felsökning](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting).

## <a name="remove-hdinsight-applications"></a>Ta bort HDInsight-program
Det finns flera sätt att ta bort HDInsight-program på.

### <a name="use-portal"></a>Använda portalen
**Ta bort ett program via portalen**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **HDInsight-kluster** i den vänstra menyn.  Om du inte ser det klickar du på **Bläddra** och sedan på **HDInsight-kluster**.
3. Klicka på klustret där du installerade programmet.
4. I bladet **Inställningar** klickar du på **Program** under kategorin **Allmänt**. En lista med installerade program visas. I den här kursen anges **hue** i bladet **Installerade appar**.
5. Högerklicka på programmet du vill ta bort och klicka sedan på **Ta bort**.
6. Bekräfta genom att klicka på **Ja**.

Via portalen kan du också ta bort klustret eller resursgruppen som innehåller programmet.

### <a name="use-azure-powershell"></a>Använda Azure PowerShell
Med Azure PowerShell kan du ta bort klustret eller resursgruppen. Se [Ta bort kluster med hjälp av Azure PowerShell](hdinsight-administer-use-powershell.md#delete-clusters).

### <a name="use-azure-classic-cli"></a>Använda Azure klassiska CLI
Med klassiska Azure-CLI kan du ta bort klustret eller ta bort resursgruppen. Se [ta bort kluster med hjälp av klassiska Azure-CLI](hdinsight-administer-use-command-line.md#delete-clusters).

## <a name="next-steps"></a>Nästa steg
* [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx): Lär dig hur du utvecklar Resource Manager-mallar för distribution av HDInsight-program.
* [Installera HDInsight-program](hdinsight-apps-install-applications.md): Lär dig hur du installerar ett HDInsight-program till dina kluster.
* [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): Lär dig hur du publicerar dina anpassade HDInsight-program på Azure Marketplace.
* [Anpassa Linux-baserade HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md): information om hur du använder skriptåtgärd till att installera fler program.
* [Skapa Linux-baserade Apache Hadoop-kluster i HDInsight med hjälp av Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Lär dig hur du anropar Resource Manager-mallar för att skapa HDInsight-kluster.
* [Använda tomma edge-noder i HDInsight](hdinsight-apps-use-edge-node.md): Information om hur du använder en tom edge-nod för att få åtkomst till HDInsight-kluster, testa HDInsight-program och vara värd för HDInsight-program.
