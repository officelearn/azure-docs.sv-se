---
title: Hantera Azure-API Management med Azure Automation
description: Lär dig mer om hur Azure Automation-tjänsten kan användas för att hantera Azure-API Management.
services: api-management, automation
documentationcenter: ''
author: vladvino
manager: eamono
editor: ''
ms.assetid: 2e53c9af-f738-47f8-b1b6-593050a7c51b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: a472e00f9ecab8a5ffa6b19e4fe9a5f8b5ee5b95
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072048"
---
# <a name="managing-azure-api-management-using-azure-automation"></a>Hantera Azure-API Management med Azure Automation
Den här guiden beskriver hur du Azure Automation tjänsten och hur den kan användas för att förenkla hanteringen av Azure API Management.

## <a name="what-is-azure-automation"></a>Vad är Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) är en Azure-tjänst för att förenkla moln hantering genom process automatisering. Att använda Azure Automation, manuella, återkommande, långvariga och fel känsliga uppgifter kan automatiseras för att öka tillförlitligheten, effektiviteten och tiden för din organisation.

Azure Automation ger en mycket tillförlitlig motor för körning av arbets flöden med hög tillgänglighet som kan skalas efter dina behov. I Azure Automation kan processer startas manuellt, av system från tredje part eller med schemalagda intervall så att aktiviteterna sker exakt vid behov.

Minska drifts kostnader och frigör IT-avdelningen och DevOps personal för att fokusera på arbete som lägger till ett affärs värde genom att flytta dina moln hanterings uppgifter så att de körs automatiskt av Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>Hur kan Azure Automation hjälpa till att hantera Azure API Management?
API Management kan hanteras i Azure Automation med hjälp av [Windows PowerShell-cmdlets för Azure API Management API](https://docs.microsoft.com/powershell/module/az.apimanagement). I Azure Automation kan du skriva PowerShell Workflow-skript för att utföra många av dina API Management uppgifter med hjälp av cmdletarna. Du kan också para ihop dessa cmdlets i Azure Automation med cmdletar för andra Azure-tjänster, för att automatisera komplexa aktiviteter i Azure-tjänster och system från tredje part.

Här följer några exempel på hur du använder API Management med PowerShell:

* [Azure PowerShell-exempel för API Management](https://docs.microsoft.com/azure/api-management/powershell-samples)

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om Azure Automation och hur det kan användas för att hantera Azure API Management, kan du följa dessa länkar om du vill veta mer.

* Se självstudierna Azure Automation [Kom igång](../automation/automation-first-runbook-graphical.md).

