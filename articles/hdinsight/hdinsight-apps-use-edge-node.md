---
title: Använda tomma kantnoder på Apache Hadoop-kluster i HDInsight - Azure
description: Hur du lägger till en tom edge-nod till ett HDInsight-kluster som kan användas som en klient och sedan test/värd dina HDInsight-program.
services: hdinsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 5994abaf0276d915553494b7272d2a8abed451bb
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52498618"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Använda tomma kantnoder på Apache Hadoop-kluster i HDInsight

Lär dig hur du lägger till en tom edge-nod till ett HDInsight-kluster. En tom edge-nod är en Linux-dator med samma klientverktyg installeras och konfigureras enligt huvudnoderna, men utan [Apache Hadoop](https://hadoop.apache.org/) tjänster som körs. Du kan använda gränsnoden för åtkomst till klustret, testa dina klientprogram och som är värd för dina klientprogram. 

Du kan lägga till en tom edge-nod i ett befintligt HDInsight-kluster till ett nytt kluster när du skapar klustret. Att lägga till en tom kantnod görs med hjälp av Azure Resource Manager-mall.  I följande exempel visas hur du kan göra med en mall:

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

I exemplet visas kan du också anropa en [skripta åtgärd](hdinsight-hadoop-customize-cluster-linux.md) att utföra ytterligare konfiguration, till exempel installera [Apache Hue](hdinsight-hadoop-hue-linux.md) i gränsnoden. Åtgärdsskriptet skriptet måste vara offentligt tillgänglig på webben.  Till exempel om skriptet lagras i Azure storage, använda offentliga behållare eller offentliga blobar.

Edge-nodstorlek för virtuell dator måste uppfylla storlekskraven för HDInsight-kluster worker noden vm. Rekommenderade worker noden vm-storlekar finns i [skapa Apache Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

När du har skapat en kantnod, kan du ansluta till gränsnoden via SSH och kör klientverktyg för att komma åt Hadoop-kluster i HDInsight.

> [!WARNING] 
> Anpassade komponenter som är installerade på gränsnoden får kommersiellt rimlig support från Microsoft. Detta kan resultera i att lösa eventuella problem. Eller så kan du hänvisas till community-resurser för ytterligare hjälp. Följande är några av de mest aktiva webbplatser för att få hjälp från communityn:
>
> * [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [http://stackoverflow.com](http://stackoverflow.com).
>
> Om du använder en Apache-teknik kan du kanske kan hitta hjälp via Apache project-webbplatser på [ http://apache.org ](http://apache.org), till exempel den [Apache Hadoop](http://hadoop.apache.org/) plats.

> [!NOTE]
> Precis som andra klusternoder är kantnoder också patch hanteras.  Mer information finns i [OS-korrigering för HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Lägg till en edge-nod i ett befintligt kluster
I det här avsnittet använder du Resource Manager-mall för att lägga till en edge-nod i ett befintligt HDInsight-kluster.  Resource Manager-mallen finns i [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/). Resource Manager-mallen anropar en skriptåtgärd i https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh. Skriptet utföra inte några åtgärder.  Det är att demonstrera den anropande skriptåtgärd från en Resource Manager-mall.

**Att lägga till en tom edge-nod i ett befintligt kluster**

1. Klicka på följande bild för att logga in på Azure och öppna Azure Resource Manager-mall i Azure-portalen. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Konfigurera följande egenskaper:
   
   * **Prenumeration**: Välj en Azure-prenumeration används för att skapa klustret.
   * **Resursgrupp**: Välj den resursgrupp som används för det befintliga HDInsight-klustret.
   * **Plats**: Välj platsen i det befintliga HDInsight-klustret.
   * **Klusternamn**: Ange namnet på ett befintligt HDInsight-kluster.
   * **Edge-nodstorlek**: Välj någon av VM-storlekar. Virtuella datorstorlek måste uppfylla storlekskraven för worker noden vm. Rekommenderade worker noden vm-storlekar finns i [skapa Apache Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).
   * **Edge-nod prefixet**: standardvärdet är **nya**.  Med hjälp av standardvärdet, namnet på edge-noden är **nya edgenode**.  Du kan anpassa prefixet från portalen. Du kan också anpassa det fullständiga namnet från mallen.

4. Kontrollera **jag godkänner villkoren som anges ovan**, och klicka sedan på **köp** att skapa kantnoden.

>[!IMPORTANT]
> Se till att välja Azure-resursgrupp för det befintliga HDInsight-klustret.  I annat fall får felmeddelandet ”Det går inte att utföra åtgärden på kapslad resurs. Överordnad resurs '&lt;klusternamn >' hittades inte ”.

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Lägg till en kantnod när du skapar ett kluster
I det här avsnittet ska använda du Resource Manager-mall för att skapa HDInsight-kluster med en kantnod.  Resource Manager-mallen finns i den [Azure-Snabbstartsmallar galleriet](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). Resource Manager-mallen anropar en skriptåtgärd i https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh. Skriptet utföra inte några åtgärder.  Det är att demonstrera den anropande skriptåtgärd från en Resource Manager-mall.

**Skapa ett HDInsight-kluster med en kantnod**

1. Skapa ett HDInsight-kluster om du inte har något ännu.  Se [komma igång med Hadoop i HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
2. Klicka på följande bild för att logga in på Azure och öppna Azure Resource Manager-mall i Azure-portalen. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Konfigurera följande egenskaper:
   
   * **Prenumeration**: Välj en Azure-prenumeration används för att skapa klustret.
   * **Resursgrupp**: skapa en ny resursgrupp som används för klustret.
   * **Plats**: Välj en plats för resursgruppen.
   * **Klusternamn**: Ange ett namn för det nya klustret att skapa.
   * **Kluster-användarnamnet för inloggningen**: Ange det Hadoop-HTTP-användarnamnet.  Standardnamnet är **admin**.
   * **Kluster inloggningslösenordet**: Ange det Hadoop-HTTP-användarlösenordet.
   * **SSH användarnamn**: Ange SSH-användarnamn. Standardnamnet är **sshuser**.
   * **SSH lösenord**: Ange SSH-användarlösenordet.
   * **Installera skriptåtgärd**: Behåll standardvärdet för att gå igenom den här kursen.
     
     Vissa egenskaper har hårdkodats i mallen: typ av kluster, antalet klusternoder worker, Edge nodstorlek och Edge nodnamnet.
4. Kontrollera **jag godkänner villkoren som anges ovan**, och klicka sedan på **köp** att skapa klustret med kantnoden.

## <a name="add-multiple-edge-nodes"></a>Lägga till flera edge-noder

Du kan lägga till flera edge-noder i ett HDInsight-kluster.  Konfiguration för flera edge-noder kan endast göras med hjälp av Azure Resource Manager-mallar.  Se exemplet mallen i början av den här artikeln.  Du måste uppdatera den **targetInstanceCount** så att antalet edge-noder som du vill skapa.

## <a name="access-an-edge-node"></a>Komma åt en kantnod
Kantnoden ssh-slutpunkten är &lt;EdgeNodeName >.&lt; Klusternamn >-ssh.azurehdinsight.net:22.  Till exempel nya-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Edge-nod visas som ett program på Azure portal.  Portalen innehåller information om att få åtkomst till kantnoden via SSH.

**Kontrollera SSH-slutpunkten edge-nod**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Öppna HDInsight-kluster med en kantnod.
3. Klicka på **program**. Du bör se kantnoden.  Standardnamnet är **nya edgenode**.
4. Klicka på gränsnoden. Du bör se SSH-slutpunkten.

**Använda Hive på gränsnoden**

1. Använd SSH för att ansluta till gränsnoden. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. När du har anslutit till gränsnoden via SSH använder du följande kommando för att öppna Hive-konsolen:
   
        hive
3. Kör följande kommando för att visa Hive-tabeller i klustret:
   
        show tables;

## <a name="delete-an-edge-node"></a>Ta bort en kantnod
Du kan ta bort en kantnod från Azure-portalen.

**Åtkomst till en kantnod**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Öppna HDInsight-kluster med en kantnod.
3. Klicka på **program**. Du bör se en lista över kantnoder.  
4. Högerklicka på edge-noden som du vill ta bort och klicka sedan på **ta bort**.
5. Bekräfta genom att klicka på **Ja**.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du lägger till en kantnod och hur du åtkomst till kantnoden. Mer information finns i följande artiklar:

* [Installera HDInsight-program](hdinsight-apps-install-applications.md): Läs mer om hur du installerar ett HDInsight-program till dina kluster.
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md): Lär dig hur du distribuerar ett Opublicerat HDInsight-program till HDInsight.
* [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): information om hur du publicerar anpassade HDInsight-program på Azure Marketplace.
* [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx): information om hur du definierar HDInsight-program.
* [Anpassa Linux-baserade HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md): information om hur du använder skriptåtgärd till att installera fler program.
* [Skapa Linux-baserade Apache Hadoop-kluster i HDInsight med hjälp av Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Lär dig hur du anropar Resource Manager-mallar för att skapa HDInsight-kluster.

