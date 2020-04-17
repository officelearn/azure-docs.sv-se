---
title: Använd tomma kantnoder i Apache Hadoop-kluster i Azure HDInsight
description: Så här lägger du till en tom kantnod i ett HDInsight-kluster. Används som klient och testa eller är värd för dina HDInsight-program.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/16/2020
ms.openlocfilehash: f6dea00bf3b3e8a58f42da8fd8ad59ccec2dea72
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537805"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Använd tomma kantnoder på Apache Hadoop-kluster i HDInsight

Lär dig hur du lägger till en tom kantnod i ett HDInsight-kluster. En tom kantnod är en virtuell Linux-dator med samma klientverktyg installerade och konfigurerade som i headnodes. Men utan [Apache Hadoop](./hadoop/apache-hadoop-introduction.md) tjänster igång. Du kan använda kantnoden för åtkomst till klustret, testa klientprogrammen och vara värd för klientprogram.

Du kan lägga till en tom kantnod i ett befintligt HDInsight-kluster i ett nytt kluster när du skapar klustret. Det görs en tom kantnod med Azure Resource Manager-mallen.  Följande exempel visar hur det görs med hjälp av en mall:

```json
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
                        "vmSize": "{}"
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
```

Som visas i exemplet kan du anropa en [skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md) för att utföra ytterligare konfiguration. Till exempel installera [Apache Hue](hdinsight-hadoop-hue-linux.md) i kantnoden. Skriptåtgärdsskriptet måste vara allmänt tillgängligt på webben.  Om skriptet till exempel lagras i Azure Storage använder du antingen offentliga behållare eller offentliga blobbar.

Den virtuella kantnodens storlek måste uppfylla storlekskraven för HDInsight-klusterarbetaren vm. För de rekommenderade vm-storlekarna för arbetsnoder finns i [Skapa Apache Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-type).

När du har skapat en kantnod kan du ansluta till kantnoden med SSH och köra klientverktyg för att komma åt Hadoop-klustret i HDInsight.

> [!WARNING]
> Anpassade komponenter som installeras på kantnoden får kommersiellt rimlig support från Microsoft. Detta kan leda till att du löser problem som uppstår. Eller så kan du hänvisas till community-resurser för ytterligare hjälp. Följande är några av de mest aktiva platserna för att få hjälp från communityn:
>
> * [MSDN forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Om du använder en Apache-teknik kan du kanske få hjälp [https://apache.org](https://apache.org)via Apache-projektwebbplatserna på , till exempel [Apache Hadoop-webbplatsen.](https://hadoop.apache.org/)

> [!IMPORTANT]
> Ubuntu-bilder blir tillgängliga för nya HDInsight-kluster som skapas inom 3 månader efter att de publicerats. Från och med januari 2019 korrigeras **inte** kluster som körs (inklusive kantnoder) automatiskt. Kunder måste använda skriptåtgärder eller andra mekanismer för att korrigera ett kluster som körs.  Mer information finns i [OS-korrigering för HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Lägga till en kantnod i ett befintligt kluster

I det här avsnittet använder du en Resource Manager-mall för att lägga till en kantnod i ett befintligt HDInsight-kluster.  Mallen Resource Manager finns i [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/). Mallen Resurshanteraren anropar en https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.shskriptåtgärd som finns på . Skriptet gör inga åtgärder.  Det är för att demonstrera anropa skriptåtgärder från en Resource Manager-mall.

1. Välj följande avbildning för att logga in på Azure och öppna Azure Resource Manager-mallen i Azure-portalen.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Konfigurera följande egenskaper:

    |Egenskap |Beskrivning |
    |---|---|
    |Prenumeration|Välj en Azure-prenumeration som används för att skapa klustret.|
    |Resursgrupp|Välj den resursgrupp som används för det befintliga HDInsight-klustret.|
    |Location|Välj platsen för det befintliga HDInsight-klustret.|
    |Klusternamn|Ange namnet på ett befintligt HDInsight-kluster.|

1. Kontrollera **att jag godkänner de villkor som anges ovan**och välj sedan **Köp** för att skapa kantnoden.

> [!IMPORTANT]  
> Se till att välja Azure-resursgruppen för det befintliga HDInsight-klustret.  Annars visas felmeddelandet "Kan inte utföra begärd åtgärd på kapslad resurs. Det gick&lt;inte att hitta den överordnade resursen ClusterName>."

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Lägga till en kantnod när du skapar ett kluster

I det här avsnittet använder du en Resource Manager-mall för att skapa HDInsight-kluster med en kantnod.  Resource Manager-mallen finns i [azure-snabbstartsmallargalleriet](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). Mallen Resurshanteraren anropar en https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.shskriptåtgärd som finns på . Skriptet gör inga åtgärder.  Det är för att demonstrera anropa skriptåtgärder från en Resource Manager-mall.

1. Skapa ett HDInsight-kluster om du inte har ett ännu.  Se [Kom igång med Hadoop i HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

1. Välj följande avbildning för att logga in på Azure och öppna Azure Resource Manager-mallen i Azure-portalen.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Konfigurera följande egenskaper:

    |Egenskap |Beskrivning |
    |---|---|
    |Prenumeration|Välj en Azure-prenumeration som används för att skapa klustret.|
    |Resursgrupp|Skapa en ny resursgrupp som används för klustret.|
    |Location|Välj en plats för resursgruppen.|
    |Klusternamn|Ange ett namn som det nya klustret ska skapa.|
    |Användarnamn för klusterinloggning|Ange namnet på Hadoop HTTP.  Standardnamnet är **admin**.|
    |Lösenord för klusterinloggning|Ange användarlösenordet för Hadoop HTTP.|
    |Ssh-användarnamn|Ange SSH-användarnamnet. Standardnamnet är **sshuser**.|
    |Ssh lösenord|Ange SSH-användarlösenordet.|
    |Installera skriptåtgärd|Behåll standardvärdet för att gå igenom den här artikeln.|

    Vissa egenskaper har hårdkodats i mallen: Klustertyp, antal klusterarbetarenoder, Edge-nodstorlek och Edge-nodnamn.

1. Kontrollera **att jag godkänner de villkor som anges ovan**och välj sedan **Köp** för att skapa klustret med kantnoden.

## <a name="add-multiple-edge-nodes"></a>Lägga till noder med flera kanter

Du kan lägga till flera kantnoder i ett HDInsight-kluster.  Konfigurationen av flera kanter kan bara göras med Azure Resource Manager-mallar.  Se mallexemplet i början av den här artikeln.  Uppdatera **targetInstanceCount** för att återspegla antalet kantnoder som du vill skapa.

## <a name="access-an-edge-node"></a>Komma åt en kantnod

Kannoden ssh-slutpunkten &lt;är EdgeNodeName>. &lt;ClusterName>-ssh.azurehdinsight.net:22.  Till exempel new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Kantnoden visas som ett program på Azure-portalen.  Portalen ger dig information för att komma åt kantnoden med hjälp av SSH.

**Så här verifierar du SSH-slutpunkten för kantnod**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Öppna HDInsight-klustret med en kantnod.
3. Välj **program**. Du ska se kantnoden.  Standardnamnet är **ny-edgenode**.
4. Markera kantnoden. Du ska se SSH-slutpunkten.

**Så här använder du Hive på kantnoden**

1. Använd SSH för att ansluta till gränsnoden. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. När du har anslutit till kantnoden med SSH använder du följande kommando för att öppna Hive-konsolen:

        hive

3. Kör följande kommando för att visa Hive-tabeller i klustret:

        show tables;

## <a name="delete-an-edge-node"></a>Ta bort en kantnod

Du kan ta bort en kantnod från Azure-portalen.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Öppna HDInsight-klustret med en kantnod.
3. Välj **program**. Du ska se en lista över kantnoder.  
4. Högerklicka på den kantnod som du vill ta bort och välj sedan **Ta bort**.
5. Välj **Ja** för att bekräfta.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du lägger till en kantnod och hur du kommer åt kantnoden. Mer information finns i följande artiklar:

* [Installera HDInsight-program](hdinsight-apps-install-applications.md): Läs mer om hur du installerar ett HDInsight-program till dina kluster.
* [Installera anpassade HDInsight-program:](hdinsight-apps-install-custom-applications.md)lär dig hur du distribuerar ett opublicerat HDInsight-program till HDInsight.
* [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): information om hur du publicerar anpassade HDInsight-program på Azure Marketplace.
* [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx): information om hur du definierar HDInsight-program.
* [Anpassa Linux-baserade HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md): information om hur du använder skriptåtgärd till att installera fler program.
* [Skapa Linux-baserade Apache Hadoop-kluster i HDInsight med hjälp av Resource Manager-mallar:](hdinsight-hadoop-create-linux-clusters-arm-templates.md)lär dig hur du anropar Resource Manager-mallar för att skapa HDInsight-kluster.
