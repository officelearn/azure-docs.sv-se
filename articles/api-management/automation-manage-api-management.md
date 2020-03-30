---
title: Hantera Azure API Management med Azure Automation
description: Lär dig mer om hur Azure Automation-tjänsten kan användas för att hantera Azure API Management.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70072048"
---
# <a name="managing-azure-api-management-using-azure-automation"></a>Hantera Azure API Management med hjälp av Azure Automation
Den här guiden introducerar dig till Azure Automation-tjänsten och hur den kan användas för att förenkla hanteringen av Azure API Management.

## <a name="what-is-azure-automation"></a>Vad är Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) är en Azure-tjänst för att förenkla molnhantering genom processautomatisering. Med hjälp av Azure Automation kan manuella, upprepade, långvariga och felbenägna uppgifter automatiseras för att öka tillförlitligheten, effektiviteten och tiden till värde för din organisation.

Azure Automation tillhandahåller en mycket tillförlitlig och högtillgänglig arbetsflödeskörningsmotor som skalas efter dina behov. I Azure Automation kan processer startas manuellt, av tredjepartssystem eller med schemalagda intervall så att aktiviteter sker exakt när det behövs.

Minska driftkostnaderna och frigör IT- och DevOps-personal för att fokusera på arbete som ger affärsnytta genom att flytta dina molnhanteringsuppgifter som ska köras automatiskt av Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>Hur kan Azure Automation hjälpa till att hantera Azure API Management?
API Management kan hanteras i Azure Automation med hjälp av [Windows PowerShell-cmdlets för Azure API Management API](https://docs.microsoft.com/powershell/module/az.apimanagement). I Azure Automation kan du skriva PowerShell-arbetsflödesskript för att utföra många av dina API Management-uppgifter med hjälp av cmdlets. Du kan också para ihop dessa cmdlets i Azure Automation med cmdlets för andra Azure-tjänster, för att automatisera komplexa uppgifter över Azure-tjänster och tredjepartssystem.

Här är några exempel på hur du använder API Management med Powershell:

* [Azure PowerShell-exempel för API Management](https://docs.microsoft.com/azure/api-management/powershell-samples)

## <a name="next-steps"></a>Efterföljande moment
Nu när du har lärt dig grunderna i Azure Automation och hur det kan användas för att hantera Azure API Management följer du dessa länkar för att lära dig mer.

* Se azure automation [komma igång självstudien](../automation/automation-first-runbook-graphical.md).

