---
title: "Använda tom edge noder på Hadoop-kluster i HDInsight - Azure | Microsoft Docs"
description: "Hur du lägger till en tom kantnod ett HDInsight-kluster som kan användas som en klient och sedan testa/host HDInsight-program."
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: 
ms.assetid: cdc7d1b4-15d7-4d4d-a13f-c7d3a694b4fb
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jgao
ms.openlocfilehash: e21dabcc6999b1f1047d334e782f723d0c03c2cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-empty-edge-nodes-on-hadoop-clusters-in-hdinsight"></a>Använda tom edge noder på Hadoop-kluster i HDInsight

Lär dig hur du lägger till en tom kantnod ett HDInsight-kluster. En tom kantnod är en virtuell Linux-dator med samma klientverktygen installeras och konfigureras enligt headnodes, men utan några Hadoop-tjänster som körs. Du kan använda kantnoden för åtkomst till klustret, testa ditt klientprogram och värd för ditt program. 

Du kan lägga till en tom kantnod till ett befintligt HDInsight-kluster till ett nytt kluster när du skapar klustret. Lägga till en tom kantnod görs med hjälp av Azure Resource Manager-mall.  I följande exempel visar hur du gör med hjälp av en mall:

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

Som det visas i exemplet, kan du om du vill anropa en [skript åtgärd](hdinsight-hadoop-customize-cluster-linux.md) att utföra ytterligare konfigurering, till exempel installera [Apache Hue](hdinsight-hadoop-hue-linux.md) i kantnoden. Skriptet åtgärdsskriptet måste vara allmänt tillgänglig på webben.  Till exempel om skriptet lagras i Azure-lagring kan använda offentliga behållare eller offentliga blobar.

Edge virtuella nodstorlek måste uppfylla HDInsight-kluster worker nod vm storlek krav. Vm-storlekar rekommenderade worker noden finns [skapa Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

När du har skapat en kantnod kan du ansluta till kantnoden med SSH och kör klientverktyg för att komma åt Hadoop-kluster i HDInsight.

> [!WARNING] 
> Använda en tom kantnod med HDInsight är för närvarande under förhandsgranskning. Anpassade komponenter som är installerade på kantnoden få kommersiellt rimliga support från Microsoft. Detta kan resultera i att lösa eventuella problem. Du kan vara enligt eller community-resurser för ytterligare hjälp. Följande är några av de mest aktiva platser för att få hjälp i communityn:
>
> * [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [http://StackOverflow.com](http://stackoverflow.com).
>
> Om du använder en Apache-teknik kan du hitta hjälp via Apache project-webbplatser på [http://apache.org](http://apache.org), som den [Hadoop](http://hadoop.apache.org/) plats.

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Lägg till en kantnod till ett befintligt kluster
I det här avsnittet använder du en Resource Manager-mall för att lägga till en kantnod i ett befintligt HDInsight-kluster.  Resource Manager-mallen finns i [GitHub](https://azure.microsoft.com/en-us/resources/templates/101-hdinsight-linux-add-edge-node/). Resource Manager-mallen anropar en skriptåtgärd på https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh. Skriptet utföra inte några åtgärder.  Det går att anropa åtgärder som skript från en Resource Manager-mall.

**Att lägga till en tom kantnod i ett befintligt kluster**

1. Skapa ett HDInsight-kluster om du inte har någon ännu.  Se [Hadoop-vägledning: komma igång med Hadoop i HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Klicka på följande bild för att logga in på Azure och öppna Azure Resource Manager-mall i Azure-portalen. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Konfigurera följande egenskaper:
   
   * **Prenumerationen**: Välj en Azure-prenumeration används för att skapa klustret.
   * **Resursgruppen**: Markera den resursgrupp som används för befintliga HDInsight-klustret.
   * **Plats**: Välj platsen för det befintliga HDInsight-klustret.
   * **Klusternamn**: Ange namnet på ett befintligt HDInsight-kluster.
   * **Kant nodstorlek**: Välj en storlek på Virtuella datorer. Vm-storlek måste uppfylla storlekskraven worker nod vm. Vm-storlekar rekommenderade worker noden finns [skapa Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).
   * **Kant nod prefixet**: standardvärdet är **nya**.  Med standardvärdet edge nodnamnet är **nya edgenode**.  Du kan anpassa prefixet från portalen. Du kan också anpassa det fullständiga namnet från mallen.

4. Kontrollera **jag samtycker till villkoren som anges ovan**, och klicka sedan på **inköp** att skapa kantnoden.

>[!IMPORTANT]
> Se till att välja Azure-resursgrupp för det befintliga HDInsight-klustret.  I annat fall får felmeddelandet ”Det går inte att utföra åtgärden på kapslad resurs. Överordnade resursen '&lt;klusternamn >' hittades inte ”.

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Lägg till en kantnod när du skapar ett kluster
I det här avsnittet använder du en Resource Manager-mall för att skapa HDInsight-kluster med en kantnod.  Resource Manager-mallen finns i den [Azure QuickStart mallgalleriet](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). Resource Manager-mallen anropar en skriptåtgärd på https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh. Skriptet utföra inte några åtgärder.  Det går att anropa åtgärder som skript från en Resource Manager-mall.

**Att lägga till en tom kantnod i ett befintligt kluster**

1. Skapa ett HDInsight-kluster om du inte har någon ännu.  Se [komma igång med Hadoop i HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Klicka på följande bild för att logga in på Azure och öppna Azure Resource Manager-mall i Azure-portalen. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Konfigurera följande egenskaper:
   
   * **Prenumerationen**: Välj en Azure-prenumeration används för att skapa klustret.
   * **Resursgruppen**: skapa en ny resursgrupp som används för klustret.
   * **Plats**: Välj en plats för resursgruppen.
   * **Klusternamn**: Ange ett namn för det nya klustret att skapa.
   * **Klustrets inloggningsnamn**: Ange användarnamnet Hadoop HTTP.  Standardnamnet är **admin**.
   * **Klustret inloggningslösenordet**: Ange Hadoop HTTP användarens lösenord.
   * **SSH användarnamn**: Ange SSH-användarnamn. Standardnamnet är **sshuser**.
   * **SSH lösenord**: Ange lösenord för SSH-användare.
   * **Installera skriptåtgärd**: Behåll standardvärdet för att gå igenom den här kursen.
     
     Vissa egenskaper har hårdkodad i mallen: typ av kluster, kluster worker-nodsantalet, Edge nodstorlek och Edge nodnamn.
4. Kontrollera **jag samtycker till villkoren som anges ovan**, och klicka sedan på **inköp** att skapa klustret med kantnoden.

## <a name="access-an-edge-node"></a>Komma åt en kantnod
Kantnoden ssh-slutpunkten är &lt;EdgeNodeName >.&lt; Klusternamn >-ssh.azurehdinsight.net:22.  Till exempel nya-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Edge-noden visas som ett program på Azure-portalen.  Portalen innehåller information om att komma åt kantnod med SSH.

**Att verifiera edge nod SSH-slutpunkten**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Öppna HDInsight-kluster med en kantnod.
3. Klicka på **program** från klusterbladet. Du bör se kantnoden.  Standardnamnet är **nya edgenode**.
4. Klicka på kantnoden. Du bör se SSH-slutpunkten.

**Använda Hive på kantnoden**

1. Använda SSH för att ansluta till kantnoden. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. När du har anslutit till kantnod med SSH, använder du följande kommando för att öppna konsolen Hive:
   
        hive
3. Kör följande kommando för att visa Hive-tabeller i klustret:
   
        show tables;

## <a name="delete-an-edge-node"></a>Ta bort en kantnod
Du kan ta bort en kantnod från Azure-portalen.

**Åtkomst till en kantnod**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Öppna HDInsight-kluster med en kantnod.
3. Klicka på **program** från klusterbladet. Visas en lista över edge noder.  
4. Högerklicka på kantnoden som du vill ta bort och klicka sedan på **ta bort**.
5. Bekräfta genom att klicka på **Ja**.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du lägger till en kantnod och hur du kommer åt kantnoden. Mer information finns i följande artiklar:

* [Installera HDInsight-program](hdinsight-apps-install-applications.md): Läs mer om hur du installerar ett HDInsight-program till dina kluster.
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md): Lär dig hur du distribuerar ett Opublicerat HDInsight-program till HDInsight.
* [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): information om hur du publicerar anpassade HDInsight-program på Azure Marketplace.
* [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx): information om hur du definierar HDInsight-program.
* [Anpassa Linux-baserade HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md): information om hur du använder skriptåtgärd till att installera fler program.
* [Skapa Linux-baserade Hadoop-kluster i HDInsight med hjälp av Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Lär dig hur du anropar Resource Manager-mallar för att skapa HDInsight-kluster.

