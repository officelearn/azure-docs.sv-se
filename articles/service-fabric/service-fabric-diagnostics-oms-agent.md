---
title: "Azure Service Fabric - Konfigurera övervakning med OMS-Agent | Microsoft Docs"
description: "Lär dig hur du ställer in OMS-agenten för övervakning av behållare och prestandaräknare för Azure Service Fabric-kluster."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/31/2017
ms.author: dekapur
ms.openlocfilehash: e1a45f9924291382bb1bbdc969e97ee54a7b6132
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="add-the-oms-agent-to-a-cluster"></a>Lägga till OMS-Agent till ett kluster

Den här artikeln beskriver stegen för att lägga till OMS-agenten som en virtuell dator skala ange tillägg i klustret och ansluta till befintliga OMS logganalys-arbetsytan. Detta gör att samla in diagnostikdata om behållare, program och prestandaövervakning. Genom att lägga till den som ett tillägg, Azure Resource Manager ser till att det installeras på varje nod skalning även när klustret.

> [!NOTE]
> Den här artikeln förutsätter att du har en OMS logganalys-arbetsyta som redan har konfigurerat. Om du inte vill gå till [konfigurera OMS logganalys](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>Lägga till filnamnstillägget agenten via Azure CLI

Det bästa sättet att lägga till OMS-Agent till ditt kluster anges via virtuella datorns skaluppsättning tillgängliga API: er med Azure CLI. Om du inte har Azure CLI ännu, gå till Azure-portalen och öppna en [moln Shell](../cloud-shell/overview.md) instans, eller [installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. När molnet gränssnittet begärs, kontrollera att du arbetar i samma prenumeration som din resurs. Kontrollera detta med `az account show` och kontrollera att värdet för ”name” matchar ditt kluster prenumeration.

2. Gå till resursgruppen där din OMS-arbetsyta finns i portalen. Klicka till logganalys resurs (typ av resursen ska vara logganalys), i navigeringen till höger, bläddra nedåt och klicka på **egenskaper**.

    ![OMS-egenskapssidan](media/service-fabric-diagnostics-oms-agent/oms-properties.png)

    Anteckna din `workspaceId`. 

3. Du måste också din `workspaceKey` distribuera agenten. För att få till nyckel som du kan klicka på **avancerade inställningar**under den *inställningar* avsnitt i det vänstra navigeringsfönstret. Klicka på **Windows-servrar** om du Ständiga in Windows-kluster och **Linux-servrar** om du skapar ett Linux-kluster. Du behöver den *primärnyckel* som visar för att distribuera dina agenter som den `workspaceKey`.

4. Kör kommandot för att installera OMS-agent till klustret med hjälp av den `vmss extension set` API i gränssnittet molnet:

    För ett Windows-kluster:
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    För ett Linux-kluster:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId'}":'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Här är ett exempel på OMS-Agent som ska läggas till ett Windows-kluster.

    ![OMS-agenten cli kommando](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
    Det bör ta mindre än 15 min ska kunna lägga till agenten noderna. Du kan kontrollera att agenterna har lagts till med hjälp av den `az vmss extension list` API:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Lägg till agenten via Resource Manager-mall

Exempel Resource Manager-mallar som distribuerar en OMS logganalys-arbetsytan och lägga till en agent till var och en av noderna är tillgänglig för [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) eller [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Du kan hämta och ändra den här mallen för att distribuera ett kluster som passar dig bäst.

## <a name="next-steps"></a>Nästa steg

* Samla in relevanta [prestandaräknare](service-fabric-diagnostics-event-generation-perf.md). Konfigurera OMS-agent för specifika prestandaräknare, head till OMS-portalen (länkad överst i OMS logganalys-resurs). Klicka på **Start > Inställningar > Data > Windows prestandaräknare** eller **Linux prestandaräknare** och välj de räknare du vill samla in.
* Konfigurera OMS att ställa in [automatiserade aviseringar](../log-analytics/log-analytics-alerts.md) att underlätta identifiering och diagnostik
