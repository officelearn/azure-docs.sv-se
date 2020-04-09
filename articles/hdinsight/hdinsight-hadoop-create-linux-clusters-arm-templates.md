---
title: Skapa Apache Hadoop-kluster med hjälp av mallar - Azure HDInsight
description: Lär dig hur du skapar kluster för HDInsight med hjälp av Resource Manager-mallar
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 67d5481b82b7469c5ae55704768ce494dbc9dca5
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879147"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Skapa Apache Hadoop-kluster i HDInsight med hjälp av Resource Manager-mallar

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

I den här artikeln lär du dig flera sätt att skapa Azure HDInsight-kluster med [Azure Resource Manager-mallar](../azure-resource-manager/templates/deploy-powershell.md). Om du vill veta mer om andra verktyg och funktioner för att skapa kluster klickar du på flikväljaren högst upp på den här sidan. Se även [metoder för att skapa kluster](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

En Resource Manager-mall gör det enkelt att skapa följande resurser för ditt program i en enda samordnad åtgärd:

* HDInsight-kluster och deras beroende resurser (till exempel standardlagringskontot).
* Andra resurser (till exempel Azure SQL Database för att använda [Apache Sqoop](https://sqoop.apache.org/)).

I mallen definierar du de resurser som behövs för programmet. Du kan också ange distributionsparametrar till indatavärden för olika miljöer. Mallen består av JSON och uttryck som du använder för att konstruera värden för distributionen.

Du hittar HDInsight-mallexempel på [Snabbstartsmallar](https://azure.microsoft.com/resources/templates/?term=hdinsight)för Azure . Använd Visual [Studio-kod](https://code.visualstudio.com/#alt-downloads) över flera plattformar med [Resurshanterarens tillägg](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) eller en textredigerare för att spara mallen i en fil på arbetsstationen.

Mer information om Resource Manager-mallar finns i följande artiklar och exempel:

* [Författare Azure Resource Manager-mallar](../azure-resource-manager/templates/template-syntax.md)
* [Distribuera ett program med Azure Resource Manager-mallar](../azure-resource-manager/templates/deploy-powershell.md)
* [Mallreferens för Microsoft.HDInsight/kluster](/azure/templates/microsoft.hdinsight/allversions)
* [Snabbstartsmallar för Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Generera mallar

Med Resurshanteraren kan du exportera en Resource Manager-mall från befintliga resurser i prenumerationen med olika verktyg. Du kan använda mallen som genereras för att lära dig mer om mallsyntaxen eller för att automatisera omdistributionen av din lösning om det behövs. Mer information finns i [Exportera mallar](../azure-resource-manager/templates/export-template-portal.md).

## <a name="deploy-using-the-portal"></a>Distribuera med hjälp av portalen

Du kan distribuera en Resource Manager-mall med Azure-portalen. Mer information finns i [Distribuera resurser från anpassad mall](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Distribuera med hjälp av PowerShell

Du kan distribuera en Resource Manager-mall med Azure PowerShell. Mer information finns i [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) och distribuera privat Resource [Manager-mall med SAS-token och Azure PowerShell](../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="deploy-using-azure-cli"></a>Distribuera med Azure CLI

Du kan distribuera en Resource Manager-mall med Azure CLI. Mer information finns i [Distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/templates/deploy-cli.md) och Distribuera privat Resource [Manager-mall med SAS-token och Azure CLI](../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Distribuera med REST API

Du kan distribuera en Resource Manager-mall med REST API. Mer information finns i [Distribuera resurser med Resource Manager-mallar och RESURSHANTERARENS REST API](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Distribuera med Visual Studio

 Använd Visual Studio för att skapa ett resursgruppsprojekt och distribuera det till Azure via användargränssnittet. Du väljer vilken typ av resurser som ska inkluderas i projektet. Dessa resurser läggs automatiskt till i resource manager-mallen. Projektet innehåller också ett PowerShell-skript för att distribuera mallen.

En introduktion till att använda Visual Studio med resursgrupper finns i [Skapa och distribuera Azure-resursgrupper via Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig flera sätt att skapa ett HDInsight-kluster. Mer information finns i följande artiklar:

* Fler HDInsight-relaterade mallar finns i [Snabbstartsmallar för Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Ett exempel på att distribuera resurser via .NET-klientbiblioteket finns [i Distribuera resurser med hjälp av .NET-bibliotek och en mall](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Ett djupgående exempel på hur du distribuerar ett program finns i [Etablera och distribuera mikrotjänster förutsägbart i Azure](../app-service/deploy-complex-application-predictably.md).
* Information om hur du distribuerar lösningen till olika miljöer finns i [Utvecklings- och testmiljöer i Microsoft Azure](../solution-dev-test-environments.md).
* Mer information om avsnitten i Azure Resource Manager-mallen finns i [Skapa mallar](../azure-resource-manager/templates/template-syntax.md).
* En lista över de funktioner som du kan använda i en Azure Resource Manager-mall finns i [Mallfunktioner](../azure-resource-manager/templates/template-functions.md).
