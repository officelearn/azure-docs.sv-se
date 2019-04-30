---
title: Skapa Apache Hadoop-kluster med hjälp av mallar – Azure HDInsight
description: Lär dig att skapa kluster för HDInsight med hjälp av Resource Manager-mallar
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 73402421a87d2cf14719ff34201890ea96c90519
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097430"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Skapa Apache Hadoop-kluster i HDInsight med hjälp av Resource Manager-mallar
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

I den här artikeln får du lära dig flera olika sätt att skapa Azure HDInsight-kluster med Azure Resource Manager-mallar. Mer information finns i [distribuera ett program med Azure Resource Manager-mall](../azure-resource-manager/resource-group-template-deploy.md). Mer information om andra verktyg för att skapa klustret och funktioner, klickar du på flikväljaren överst i den här sidan eller se [kluster metoder för att skapa](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

## <a name="prerequisites"></a>Nödvändiga komponenter
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Om du vill följa anvisningarna i den här artikeln behöver du:

* En [Azure-prenumeration](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell och/eller Azure klassiskt CLI.

### <a name="resource-manager-templates"></a>Mallar för Resurshanteraren
En Resource Manager-mallen gör det enkelt att skapa följande resurser för ditt program i en enda, samordnad åtgärd:
* HDInsight-kluster och deras beroende resurser (till exempel standardkontot för lagring).
* Andra resurser (till exempel Azure SQL Database för att använda [Apache Sqoop](https://sqoop.apache.org/)).

I mallen definierar du de resurser som behövs för programmet. Du kan även ange distributionsparametrarna för att ange värden för olika miljöer. Mallen består av JSON och uttryck som används för att skapa värden för din distribution.

Du kan hitta HDInsight-mallexempel på [Azures snabbstartsmallar](https://azure.microsoft.com/resources/templates/?term=hdinsight). Använd plattformsoberoende [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) med den [Resource Manager-tillägget](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) eller en textredigerare för att spara mallen till en fil på din arbetsstation. 

Mer information om Resource Manager-mallar finns i följande artiklar och exempel:

* [Skapa Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md)
* [Distribuera ett program med Azure Resource Manager-mallar](../azure-resource-manager/resource-group-template-deploy.md)
* [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/allversions) mallreferensen
* [Azure-snabbstartmallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Skapa mallar

Resource Manager kan du exportera en Resource Manager-mall från befintliga resurser i din prenumeration med hjälp av olika verktyg. Du kan använda mallen som genereras för att lära dig mer om mallsyntaxen eller för att automatisera omdistributionen av din lösning om det behövs. Mer information finns i [Exportera mallar](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).

## <a name="deploy-using-the-portal"></a>Distribuera med hjälp av portalen

Du kan distribuera en Resource Manager-mall med Azure portal. Mer information finns i [distribuera resurser från anpassad mall](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Distribuera med hjälp av PowerShell

Du kan distribuera en Resource Manager-mall med Azure PowerShell. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) och [distribuera privat Resource Manager-mall med SAS-token och Azure PowerShell](../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="deploy-using-azure-cli"></a>Distribuera med hjälp av Azure CLI

Du kan distribuera en Resource Manager-mall med klassisk CLI. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) och [distribuera privat Resource Manager-mall med SAS-token och Azure CLI](../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Distribuera med hjälp av REST-API
Du kan distribuera en Resource Manager-mall med hjälp av REST API. Mer information finns i [distribuera resurser med Resource Manager-mallar och Resource Manager REST API](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Distribuera med Visual Studio
 Använd Visual Studio för att skapa en resursgrupp-projekt och distribuera den till Azure via användargränssnittet. Du kan välja vilken typ av resurser till ditt projekt. De här resurserna läggs automatiskt till Resource Manager-mallen. Projektet innehåller också ett PowerShell.skript för att distribuera mallen.

En introduktion till Visual Studio med resursgrupper, se [skapa och distribuera Azure-resursgrupper via Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig att skapa ett HDInsight-kluster på flera olika sätt. Mer information finns i följande artiklar:

* Fler HDInsight relaterade mallar finns i [Azures snabbstartsmallar](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Ett exempel för att distribuera resurser via .NET-klientbiblioteket finns i [distribuera resurser med hjälp av .NET-bibliotek och en mall](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Ett djupgående exempel för att distribuera ett program finns i [etablera och distribuera mikrotjänster förutsägbart i Azure](../app-service/deploy-complex-application-predictably.md).
* Information om hur du distribuerar lösningen till olika miljöer finns i [Utvecklings- och testmiljöer i Microsoft Azure](../solution-dev-test-environments.md).
* Läs om hur du delar av Azure Resource Manager-mallen i [Webbsidemallar](../azure-resource-manager/resource-group-authoring-templates.md).
* En lista över de funktioner du kan använda i en Azure Resource Manager-mall finns i [Mallfunktioner](../azure-resource-manager/resource-group-template-functions.md).
