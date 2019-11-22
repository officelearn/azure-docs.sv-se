---
title: Skapa Apache Hadoop kluster med mallar – Azure HDInsight
description: Lär dig hur du skapar kluster för HDInsight med hjälp av Resource Manager-mallar
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: dba64d432231873676c49a1f4f2fac579c3f4be9
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279266"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Skapa Apache Hadoop kluster i HDInsight med hjälp av Resource Manager-mallar

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

I den här artikeln får du lära dig flera sätt att skapa Azure HDInsight-kluster med hjälp av Azure Resource Manager mallar. Mer information finns i [distribuera ett program med Azure Resource Manager-mall](../azure-resource-manager/resource-group-template-deploy.md). Om du vill veta mer om andra verktyg och funktioner för kluster skapande klickar du på tabbväljaren överst på den här sidan eller se metoder för att [skapa kluster](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Krav

* En [Azure-prenumeration](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell och/eller Azure CLI.

### <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

En Resource Manager-mall gör det enkelt att skapa följande resurser för programmet i en enda, koordinerad åtgärd:
* HDInsight-kluster och deras beroende resurser (t. ex. standard lagrings kontot).
* Andra resurser (till exempel Azure SQL Database att använda [Apache Sqoop](https://sqoop.apache.org/)).

I mallen definierar du de resurser som behövs för programmet. Du anger också distributions parametrar för inmatade värden för olika miljöer. Mallen består av JSON och uttryck som du använder för att skapa värden för din distribution.

Du kan hitta exempel på HDInsight-mallar i [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?term=hdinsight). Använd [Visual Studio-kod](https://code.visualstudio.com/#alt-downloads) mellan plattformar med [Resource Manager-tillägget](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) eller en text redigerare för att spara mallen i en fil på din arbets Station.

Mer information om Resource Manager-mallar finns i följande artiklar och exempel:

* [Redigera Azure Resource Manager mallar](../azure-resource-manager/resource-group-authoring-templates.md)
* [Distribuera ett program med Azure Resource Manager mallar](../azure-resource-manager/resource-group-template-deploy.md)
* Referens för [Microsoft. HDInsight/kluster-](/azure/templates/microsoft.hdinsight/allversions) mall
* [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Skapa mallar

Med Resource Manager kan du exportera en Resource Manager-mall från befintliga resurser i din prenumeration med hjälp av olika verktyg. Du kan använda mallen som genereras för att lära dig mer om mallsyntaxen eller för att automatisera omdistributionen av din lösning om det behövs. Mer information finns i [Exportera mallar](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).

## <a name="deploy-using-the-portal"></a>Distribuera med portalen

Du kan distribuera en Resource Manager-mall med hjälp av Azure Portal. Mer information finns i [distribuera resurser från en anpassad mall](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Distribuera med PowerShell

Du kan distribuera en Resource Manager-mall med Azure PowerShell. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) och [distribuera en privat Resource Manager-mall med SAS-token och Azure PowerShell](../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="deploy-using-azure-cli"></a>Distribuera med Azure CLI

Du kan distribuera en Resource Manager-mall med Azure CLI. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) och [distribuera en privat Resource Manager-mall med SAS-token och Azure CLI](../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Distribuera med hjälp av REST API

Du kan distribuera en Resource Manager-mall med REST API. Mer information finns i [distribuera resurser med Resource Manager-mallar och Resource Manager-REST API](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Distribuera med Visual Studio

 Använd Visual Studio för att skapa ett resurs grupps projekt och distribuera det till Azure via användar gränssnittet. Du väljer vilken typ av resurser som ska ingå i projektet. Resurserna läggs automatiskt till i Resource Manager-mallen. Projektet innehåller också ett PowerShell-skript för att distribuera mallen.

En introduktion till att använda Visual Studio med resurs grupper finns i [skapa och Distribuera Azure-resurs grupper via Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig flera olika sätt att skapa ett HDInsight-kluster. Mer information finns i följande artiklar:

* Fler HDInsight-relaterade mallar finns i [snabb starts mallar för Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Ett exempel på hur du distribuerar resurser via .NET-klient biblioteket finns i [distribuera resurser med hjälp av .NET-bibliotek och en mall](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Ett djupgående exempel på hur du distribuerar ett program finns i [etablera och distribuera mikrotjänster förutsägbart i Azure](../app-service/deploy-complex-application-predictably.md).
* Information om hur du distribuerar lösningen till olika miljöer finns i [Utvecklings- och testmiljöer i Microsoft Azure](../solution-dev-test-environments.md).
* Mer information om avsnitten i Azure Resource Manager-mallen finns i [Redigera mallar](../azure-resource-manager/resource-group-authoring-templates.md).
* En lista över de funktioner som du kan använda i en Azure Resource Manager-mall finns i [Template Functions](../azure-resource-manager/resource-group-template-functions.md).
