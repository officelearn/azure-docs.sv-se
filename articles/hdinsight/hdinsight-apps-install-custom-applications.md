<properties
    pageTitle="Installera Hadoop-program på HDInsight | Microsoft Azure"
    description="Ta reda på hur du installerar HDInsight-program på HDInsight-appar."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="06/07/2016"
    ms.author="jgao"/>

# Installera anpassade HDInsight-program

Ett HDInsight-program är ett program som användarna kan installera på ett Linux-baserat HDInsight-kluster.  Dessa program kan utvecklas av Microsoft, oberoende programvaruleverantörer och av dig själv. I den här artikeln får du lära dig hur du installerar ett HDInsight-program som inte har publicerats till Azure Portal på HDInsight. Du ska installera programmet [Hue](http://gethue.com/). 

Andra relaterade artiklar:

- [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): information om hur du publicerar anpassade HDInsight-program på Azure Marketplace.
- [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx): information om hur du definierar HDInsight-program.

 
## Krav

Om du vill installera HDInsight-program i ett befintligt HDInsight-kluster måste du ha ett HDInsight-kluster. Om du vill skapa ett läser du [Skapa kluster](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). Du kan även installera HDInsight-program när du skapar ett HDInsight-kluster.


## Installera HDInsight-program

HDInsight-program kan installeras när du skapar ett kluster eller i ett befintligt HDInsight-kluster. Information om hur du definierar ARM-mallar finns i [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx).

Filer som behövs för att distribuera programmet (Hue):

- [azuredeploy.JSON](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): ARM-mallen för att installera programmet HDInsight. Information om hur du utvecklar en egen ARM-mall finns i [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx).
- [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): Den skriptåtgärd som anropas av ARM-mallen för konfiguration av kantnoden. 
- [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): hue-binärfilen som anropas från hui-install_v0.sh. 
- [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): hue-binärfilen som anropas från hui-install_v0.sh. 
- [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): En exempelwebbapp (Tomcat) som anropas från hui-install_v0.sh.

**Installera Hue i ett befintligt HDInsight-kluster**

1. Klicka på följande bild för att logga in på Azure och öppna ARM-mallen i Azure Portal. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

    När du klickar på den här knappen öppnas en ARM-mall i Azure Portal.  ARM-mallen finns på adressen [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue).  Information om hur du skriver ARM-mallen finns i [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx).
    
2. Skriv följande i bladet **Parametrar**:

    - **Klusternamn**: Ange namnet på klustret där du vill installera programmet. Det här klustret måste vara ett befintligt kluster.
    
3. Spara parametrarna genom att klicka på **OK**.
4. I bladet **Anpassad distribution** anger du **Resursgrupp**.  Resursgruppen är en behållare som grupperar klustret, det beroende lagringskontot och andra resurser. Du måste använda samma resursgrupp som klustret.
5. Klicka på **Juridiska villkor** och sedan på **Skapa**.
6. Kontrollera att kryssrutan **Fäst på instrumentpanelen** är markerad och klicka på **Skapa**. Du kan se installationsstatus via ikonen som är fäst på portalens instrumentpanel och i portalmeddelandet (klicka på klockikonen överst i portalen).  Det tar cirka 10 minuter att installera programmet.

**Installera Hue när du skapar ett kluster**

1. Klicka på följande bild för att logga in på Azure och öppna ARM-mallen i Azure Portal. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

    När du klickar på den här knappen öppnas en ARM-mall i Azure Portal.  ARM-mallen finns på adressen [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json).  Information om hur du skriver ARM-mallen finns i [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx).

2. Följ anvisningarna för att skapa kluster och installera Hue. Mer information om hur du skapar HDInsight-kluster finns i [Skapa Linux-baserade Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Utöver Azure Portal kan du också använda [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-with-powershell) och [Azure CLI](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-with-azure-cli) för att anropa ARM-mallar.

## Verifiera installationen

Du kan validera programinstallationen genom att kontrollera statusen för programmet på Azure Portal. Dessutom kan du verifiera att alla HTTP-slutpunkter visats som förväntat och webbsidan om det finns en:

**Öppna Hue-portalen**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **HDInsight-kluster** i den vänstra menyn.  Om du inte ser det klickar du på **Bläddra** och sedan på **HDInsight-kluster**.
3. Klicka på klustret där du installerade programmet.
4. I bladet **Inställningar** klickar du på **Program** under kategorin **Allmänt**. Du kommer att se **hue** listat i bladet **Installerade appar**.
5. Klicka på **hue** i listan om du vill visa egenskaperna.  
6. Klicka på webbsidelänken och verifiera webbplatsen. Öppna http-slutpunkten i en webbläsare och validera Hue-webbgränssnittet. Öppna SSH-slutpunkten med [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md) eller andra [SSH-klienter](hdinsight-hadoop-linux-use-ssh-unix.md).
 
## Felsöka installationen

Du kan kontrollera programmets installationsstatus i portalmeddelandet (klicka på klockikonen överst i portalen). 


Om en programinstallation misslyckas kan du se felmeddelanden visas och felsökningsinformation på tre ställen:

- HDInsight-program: allmän felinformation.

    Öppna klustret från portalen och klicka på Program i bladet Inställningar:

    ![hdinsight-program programinstallationsfel](./media/hdinsight-apps-install-applications/hdinsight-apps-error.png)

- HDInsight-skriptåtgärder: Om HDInsight-programmets felmeddelande anger att en skriptåtgärd inte kunde utföras hittar du mer information om skriptfelet i skriptåtgärdsfönstret.

    Klicka på Skriptåtgärd i bladet Inställningar. I historiken för skriptåtgärder visas felmeddelanden

    ![hdinsight-program skriptåtgärdsfel](./media/hdinsight-apps-install-applications/hdinsight-apps-script-action-error.png)
    
- Ambari-webbgränssnitt: Om felet orsakats av installationsskriptet kan du använda Ambari-webbgränssnittet för att kontrollera de fullständig loggarna om installationsskripten.

    Mer information finns i [Felsökning](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting).

## Ta bort HDInsight-program

Det finns flera sätt att ta bort HDInsight-program på.

### Använda portalen

**Ta bort ett program via portalen**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **HDInsight-kluster** i den vänstra menyn.  Om du inte ser det klickar du på **Bläddra** och sedan på **HDInsight-kluster**.
3. Klicka på klustret där du installerade programmet.
4. I bladet **Inställningar** klickar du på **Program** under kategorin **Allmänt**. En lista med installerade program visas. I den här kursen anges **hue** i bladet **Installerade appar**.
5. Högerklicka på programmet du vill ta bort och klicka sedan på **Ta bort**.
6. Bekräfta genom att klicka på **Ja**.

Via portalen kan du också ta bort klustret eller resursgruppen som innehåller programmet.

### Använda Azure PowerShell

Med Azure PowerShell kan du ta bort klustret eller resursgruppen. Se [Ta bort kluster med hjälp av Azure PowerShell](hdinsight-administer-use-powershell.md#delete-clusters).

### Använda Azure CLI

Med Azure CLI kan du ta bort klustret eller resursgruppen. Se [Ta bort kluster med hjälp av Azure CLI](hdinsight-administer-use-command-line.md#delete-clusters).


## Nästa steg

- [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx): information om hur du utvecklar ARM-mallar för distribution av HDInsight-program.
- [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): information om hur du publicerar anpassade HDInsight-program på Azure Marketplace.
- [Anpassa Linux-baserade HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md): information om hur du använder skriptåtgärd till att installera fler program.
- [Skapa Linux-baserade Hadoop-kluster i HDInsight med hjälp av ARM-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md): information om hur du anropar ARM-mallar för att skapa HDInsight-kluster.


<!--HONumber=Jun16_HO2-->


