---
title: Använd tomma Edge-noder på Apache Hadoop kluster i HDInsight – Azure
description: Hur du lägger till en tom Edge-nod i ett HDInsight-kluster som kan användas som en klient och sedan testa/vara värd för dina HDInsight-program.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: hrasheed
ms.openlocfilehash: cf84d685bf20f1117aa2378cf2e2696cb78a46a5
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076155"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Använd tomma Edge-noder på Apache Hadoop kluster i HDInsight

Lär dig hur du lägger till en tom Edge-nod i ett HDInsight-kluster. En tom Edge-nod är en virtuell Linux-dator med samma klient verktyg installerade och konfigurerade som i huvudnoderna, men utan att några [Apache Hadoop](https://hadoop.apache.org/) -tjänster körs. Du kan använda Edge-noden för att komma åt klustret, testa dina klient program och vara värd för dina klient program. 

Du kan lägga till en tom Edge-nod i ett befintligt HDInsight-kluster till ett nytt kluster när du skapar klustret. Att lägga till en tom Edge-nod görs med Azure Resource Manager mall.  Följande exempel visar hur det görs med hjälp av en mall:

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "Standard_D3"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "[parameters('installScriptAction')]",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

Som du ser i exemplet kan du anropa en [skript åtgärd](hdinsight-hadoop-customize-cluster-linux.md) för att utföra ytterligare konfiguration, till exempel installera [Apache nyans](hdinsight-hadoop-hue-linux.md) i Edge-noden. Skript åtgärds skriptet måste vara offentligt tillgängligt på webben.  Om skriptet till exempel lagras i Azure Storage, använder du antingen offentliga behållare eller offentliga blobbar.

Storleken på den virtuella datorns Edge-nod måste uppfylla kraven på HDInsight-klustrets arbetsnods VM-storlek. De rekommenderade VM-storlekarna för arbetsnoder finns i [skapa Apache Hadoop kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

När du har skapat en Edge-nod kan du ansluta till Edge-noden med SSH och köra klient verktyg för att komma åt Hadoop-klustret i HDInsight.

> [!WARNING]   
> Anpassade komponenter som är installerade på Edge-noden får kommersiellt skälig support från Microsoft. Detta kan resultera i att lösa problem som du stöter på. Eller så kan du hänvisas till community-resurser för att få hjälp. Följande är några av de mest aktiva webbplatserna för att få hjälp från communityn:
>
> * [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Om du använder en Apache-teknik kanske du kan hitta hjälp genom Apache-projekt webbplatser på [https://apache.org](https://apache.org), till exempel [Apache Hadoop](https://hadoop.apache.org/) webbplats.

> [!IMPORTANT]
> Ubuntu-avbildningar blir tillgängliga för nya HDInsight-kluster inom tre månader efter att de har publicerats. Från och med januari 2019 korrigeras **inte** kluster som körs (inklusive Edge-noder) automatiskt. Kunder måste använda skript åtgärder eller andra mekanismer för att korrigera ett kluster som körs.  Mer information finns i [OS-uppdatering för HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Lägga till en Edge-nod i ett befintligt kluster
I det här avsnittet använder du en Resource Manager-mall för att lägga till en Edge-nod i ett befintligt HDInsight-kluster.  Du hittar Resource Manager-mallen i [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/). Resource Manager-mallen anropar en skript åtgärd som https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh finns på. Skriptet utför inga åtgärder.  Det är att demonstrera den anropande skript åtgärden från en Resource Manager-mall.

**Lägga till en tom Edge-nod i ett befintligt kluster**

1. Klicka på följande bild för att logga in på Azure och öppna Azure Resource Manager-mallen i Azure Portal. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>
3. Konfigurera följande egenskaper:
   
   * **Prenumeration**: Välj en Azure-prenumeration som används för att skapa klustret.
   * **Resursgrupp**: Välj den resurs grupp som används för det befintliga HDInsight-klustret.
   * **Plats**: Välj platsen för det befintliga HDInsight-klustret.
   * **Kluster namn**: Ange namnet på ett befintligt HDInsight-kluster.
   * **Storlek på Edge-nod**: Välj en av VM-storlekarna. Storleken på den virtuella datorn måste uppfylla kraven för VM-storlek för arbetsnoden. De rekommenderade VM-storlekarna för arbetsnoder finns i [skapa Apache Hadoop kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).
   * **Prefix för Edge-nod**: Standardvärdet är **New**.  Med standardvärdet är Edge **-noden New-edgenode**.  Du kan anpassa prefixet från portalen. Du kan också anpassa det fullständiga namnet från mallen.

4. Kontrol lera **att jag godkänner villkoren som anges ovan**och klicka sedan på **köp** för att skapa Edge-noden.

>[!IMPORTANT]  
> Se till att välja Azure-resurs gruppen för det befintliga HDInsight-klustret.  Annars visas fel meddelandet "det går inte att utföra den begärda åtgärden på en kapslad resurs. Det gick inte&lt;att hitta den överordnade resursens kluster namn >.

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Lägg till en Edge-nod när du skapar ett kluster
I det här avsnittet använder du en Resource Manager-mall för att skapa HDInsight-kluster med en Edge-nod.  Du hittar Resource Manager-mallen i [galleriet för Azure snabb starts mallar](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). Resource Manager-mallen anropar en skript åtgärd som https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh finns på. Skriptet utför inga åtgärder.  Det är att demonstrera den anropande skript åtgärden från en Resource Manager-mall.

**Så här skapar du ett HDInsight-kluster med en Edge-nod**

1. Skapa ett HDInsight-kluster om du inte har något ännu.  Se [Kom igång med Hadoop i HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
2. Klicka på följande bild för att logga in på Azure och öppna Azure Resource Manager-mallen i Azure Portal. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>
3. Konfigurera följande egenskaper:
   
   * **Prenumeration**: Välj en Azure-prenumeration som används för att skapa klustret.
   * **Resursgrupp**: Skapa en ny resurs grupp som används för klustret.
   * **Plats**: Välj en plats för resursgruppen.
   * **Kluster namn**: Ange ett namn för det nya klustret som ska skapas.
   * **Användar namn för kluster inloggning**: Ange användar namnet för Hadoop HTTP.  Standardnamnet är **admin**.
   * **Lösen ord för kluster inloggning**: Ange användarens lösen ord för Hadoop HTTP.
   * **SSH-användar namn**: Ange användar namnet för SSH. Standard namnet är **sshuser**.
   * **SSH-lösenord**: Ange lösen ordet för SSH-användaren.
   * **Installera skript åtgärd**: Behåll standardvärdet för att gå igenom den här artikeln.
     
     Vissa egenskaper har hårdkodad i mallen: Kluster typ, antal kluster Worker-noder, Edge-nods storlek och kant-Node-namn.
4. Kontrol lera **att jag godkänner villkoren som anges ovan**och klicka sedan på **köp** för att skapa klustret med Edge-noden.

## <a name="add-multiple-edge-nodes"></a>Lägg till flera Edge-noder

Du kan lägga till flera Edge-noder i ett HDInsight-kluster.  Konfigurationen av flera noder kan bara göras med Azure Resource Manager mallar.  Se mal exemplet i början av den här artikeln.  Du måste uppdatera **targetInstanceCount** för att avspegla antalet Edge-noder som du vill skapa.

## <a name="access-an-edge-node"></a>Åtkomst till en Edge-nod
SSH-slutpunkten för &lt;Edge-noden&lt; är EdgeNodeName >. Kluster namn >-ssh.azurehdinsight.net:22.  Till exempel new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Edge-noden visas som ett program på Azure Portal.  Portalen ger dig information om hur du kommer åt Edge-noden med SSH.

**Verifiera SSH-slutpunkten för Edge-noden**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Öppna HDInsight-klustret med en Edge-nod.
3. Klicka på **program**. Du ser noden Edge.  Standard namnet är **New-edgenode**.
4. Klicka på noden Edge. Du ska se SSH-slutpunkten.

**Använda Hive på Edge-noden**

1. Använd SSH för att ansluta till gränsnoden. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. När du har anslutit till Edge-noden med SSH använder du följande kommando för att öppna Hive-konsolen:
   
        hive
3. Kör följande kommando för att Visa Hive-tabeller i klustret:
   
        show tables;

## <a name="delete-an-edge-node"></a>Ta bort en Edge-nod
Du kan ta bort en Edge-nod från Azure Portal.

**Få åtkomst till en Edge-nod**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Öppna HDInsight-klustret med en Edge-nod.
3. Klicka på **program**. Du ska se en lista över Edge-noder.  
4. Högerklicka på den Edge-nod som du vill ta bort och klicka sedan på **ta bort**.
5. Bekräfta genom att klicka på **Ja**.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du lägger till en Edge-nod och hur du kommer åt Edge-noden. Mer information finns i följande artiklar:

* [Installera HDInsight-program](hdinsight-apps-install-applications.md): Lär dig hur du installerar ett HDInsight-program i klustren.
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md): Lär dig hur du distribuerar ett Opublicerat HDInsight-program till HDInsight.
* [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): Lär dig hur du publicerar dina anpassade HDInsight-program på Azure Marketplace.
* [MSDN Installera ett HDInsight-](https://msdn.microsoft.com/library/mt706515.aspx)program: Lär dig hur du definierar HDInsight-program.
* [Anpassa Linux-baserade HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md): information om hur du använder skriptåtgärd till att installera fler program.
* [Skapa Linux-baserade Apache Hadoop-kluster i HDInsight med hjälp av Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Lär dig hur du anropar Resource Manager-mallar för att skapa HDInsight-kluster.

