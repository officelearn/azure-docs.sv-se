---
title: Hantera Azure Cloud Services med Azure Automation | Microsoft-dokument
description: Lär dig mer om hur Azure Automation-tjänsten kan användas för att hantera Azure-molntjänster i stor skala.
services: cloud-services, automation
author: jodoglevy
manager: timlt
editor: ''
ms.assetid: 3789810a-2892-4eef-bf29-c781c1b5af48
ms.service: cloud-services
ms.topic: article
ms.date: 06/20/2016
ms.author: timlt
ms.openlocfilehash: 482fcf7d100a90d9527f510382c5dafb4f67adfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72439068"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Hantera Azure Cloud Services med Azure Automation
Den här guiden introducerar dig till Azure Automation-tjänsten och hur den kan användas för att förenkla hanteringen av dina Azure-molntjänster.

## <a name="what-is-azure-automation"></a>Vad är Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) är en Azure-tjänst för att förenkla molnhantering genom processautomatisering. Med Hjälp av Azure Automation kan tidskrävande, manuella, felbenägna och ofta upprepade uppgifter automatiseras för att öka tillförlitligheten, effektiviteten och tiden till värde för din organisation.

Azure Automation tillhandahåller en mycket tillförlitlig och högtillgänglig arbetsflödeskörningsmotor som skalas efter dina behov när din organisation växer. I Azure Automation kan processer startas manuellt, av tredjepartssystem eller med schemalagda intervall så att aktiviteter sker exakt när det behövs.

Lägre driftkostnader och frigör IT/DevOps-personal för att fokusera på arbete som tillför affärsvärde genom att flytta dina molnhanteringsuppgifter som ska köras automatiskt av Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Hur kan Azure Automation hjälpa till att hantera Azure-molntjänster?
Azure-molntjänster kan hanteras i Azure Automation med hjälp av PowerShell-cmdlets som är tillgängliga i [Azure PowerShell-verktygen](/powershell/). Azure Automation har dessa powershell-molntjänst-cmdlets tillgängliga direkt, så att du kan utföra alla dina molntjänsthanteringsuppgifter i tjänsten. Du kan också para ihop dessa cmdlets i Azure Automation med cmdlets för andra Azure-tjänster, för att automatisera komplexa uppgifter över Azure-tjänster och tredjepartssystem.

Några exempel på användningsområden för Azure Automation för att hantera Azure Cloud Services är:

* [Kontinuerlig distribution av en molntjänst när cscfg eller cspkg uppdateras i Azure Blob-lagring](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Starta om Cloud Service-instanser parallellt, en uppgraderingsdomän i taget](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Efterföljande moment
Nu när du har lärt dig grunderna i Azure Automation och hur det kan användas för att hantera Azure-molntjänster följer du dessa länkar för att lära dig mer om Azure Automation.

* [Översikt över Azure Automation](../automation/automation-intro.md)
* [Min första Runbook](../automation/automation-first-runbook-graphical.md)
