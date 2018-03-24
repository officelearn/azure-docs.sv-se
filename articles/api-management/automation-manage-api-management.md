---
title: Hantera Azure API Management med hjälp av Azure Automation
description: Läs mer om hur Azure Automation-tjänsten kan användas för att hantera Azure API Management.
services: api-management, automation
documentationcenter: ''
author: vladvino
manager: eamono
editor: ''
ms.assetid: 2e53c9af-f738-47f8-b1b6-593050a7c51b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 10b483c70f7b5a3d767815306d8a690b1b9a5faf
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="managing-azure-api-management-using-azure-automation"></a>Hantera Azure API Management med hjälp av Azure Automation
Den här guiden ger en introduktion till Azure Automation-tjänsten och hur det kan användas för att förenkla hanteringen av Azure API Management.

## <a name="what-is-azure-automation"></a>Vad är Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) är en Azure-tjänst som förenklar molnhantering via automatisering. Med hjälp av Azure Automation, manuell, kan upprepas tidskrävande och felbenägna aktiviteter automatiseras för att öka tillförlitligheten, effektivitet och tid för värde för din organisation.

Azure Automation ger en Körningsmotor för arbetsflöden för hög tillförlitlig och hög tillgänglighet som kan skalas för att uppfylla dina behov. I Azure Automation kan processer vara inletts manuellt av 3 part eller med schemalagda intervall så att aktiviteter inträffa exakt vid behov.

Minska operativ tillsyn och frigör IT och DevOps-personal att fokusera på arbete som lägger till affärsvärde genom att flytta dina uppgifter för hantering av molnet att köras automatiskt av Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>Hur Azure Automation kan hantera Azure API Management?
API-hantering kan hanteras i Azure Automation med hjälp av den [Windows PowerShell cmdlets för Azure API Management API](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/?view=azurermps-5.5.0#api_management/). Du kan skriva PowerShell arbetsflöde för skript för att utföra många av dina uppgifter för API Management med hjälp av cmdlets i Azure Automation. Du kan också koppla dessa cmdlets i Azure Automation med cmdlets för andra Azure-tjänster, att automatisera avancerade uppgifter över Azure-tjänster och 3 part.

Här följer några exempel på hur API Management med automatisering:

* [Azure API Management – använda PowerShell för säkerhetskopiering och återställning](https://blogs.msdn.microsoft.com/katriend/2015/10/02/azure-api-management-using-powershell-for-backup-and-restore/)

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Azure Automation och hur det kan användas för att hantera Azure API Management kan du följa dessa länkar om du vill veta mer.

* Se Azure Automation [komma igång-självstudiekurs](../automation/automation-first-runbook-graphical.md).

