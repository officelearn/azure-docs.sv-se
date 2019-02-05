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
ms.openlocfilehash: c2c23b6709552d053ca8db5e32a045b416c1acfc
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55733424"
---
# <a name="managing-azure-api-management-using-azure-automation"></a>Hantera Azure API Management med hjälp av Azure Automation
Den här guiden ger en introduktion till Azure Automation-tjänsten och hur den kan användas för att förenkla hanteringen av Azure API Management.

## <a name="what-is-azure-automation"></a>Vad är Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) är en Azure-tjänst för att förenkla molnhantering med Processautomatisering. Med hjälp av Azure Automation, manuell, kan upprepas, tidskrävande och felbenägna aktiviteter automatiseras för att öka tillförlitligheten, effektivitet och tid till värde för din organisation.

Azure Automation tillhandahåller en mycket pålitlig, högtillgängliga motor för arbetsflödeskörning som kan skalas efter dina behov. I Azure Automation, kan processer vara startats manuellt, med 3 part eller med schemalagda intervall så att uppgifter inträffa exakt när det behövs.

Minska driftsomkostnader och frigör IT och DevOps-personal att fokusera på arbete som ger ett mervärde för verksamheten genom att flytta din molnhanteringsuppgifter att köras automatiskt av Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>Hur kan Azure Automation till att hantera Azure API Management?
API Management kan hanteras i Azure Automation med hjälp av den [Windows PowerShell-cmdletar för Azure API Management API](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/?view=azurermps-5.5.0). Du kan skriva PowerShell-arbetsflöde för skript för att utföra många av din API Management-uppgifter med hjälp av cmdletar i Azure Automation. Du kan också koppla dessa cmdletar i Azure Automation med cmdlets för andra Azure-tjänster, att automatisera avancerade uppgifter över Azure-tjänster och system med 3 part.

Här följer några exempel på hur du använder API Management med Powershell:

* [Azure PowerShell-exempel för API Management](https://docs.microsoft.com/azure/api-management/powershell-samples)

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Azure Automation och hur den kan användas för att hantera Azure API Management kan du följa dessa länkar om du vill veta mer.

* Finns i Azure Automation [komma igång-självstudiekurs](../automation/automation-first-runbook-graphical.md).

