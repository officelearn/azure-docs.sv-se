---
title: Hantera Azure Cloud Services med Azure Automation | Microsoft Docs
description: Läs mer om hur Azure Automation-tjänsten kan användas för att hantera Azure-molntjänster i stor skala.
services: cloud-services, automation
documentationcenter: ''
author: jodoglevy
manager: timlt
editor: ''
ms.assetid: 3789810a-2892-4eef-bf29-c781c1b5af48
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2016
ms.author: timlt
ms.openlocfilehash: b3660901c86dd644369e6d1913e825cbd5ea316b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60623212"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Hantera Azure Cloud Services med Azure Automation
Den här guiden innehåller en introduktion till Azure Automation-tjänsten och hur den kan användas för att förenkla hanteringen av dina Azure-molntjänster.

## <a name="what-is-azure-automation"></a>Vad är Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) är en Azure-tjänst för att förenkla molnhantering med Processautomatisering. Med Azure Automation, kan tidskrävande, manuell, felbenägna och regelbundet återkommande uppgifter automatiseras för att öka tillförlitligheten, effektivitet och tid till värde för din organisation.

Azure Automation tillhandahåller en mycket pålitlig och mycket tillgänglig motor för arbetsflödeskörning som kan skalas efter dina behov när organisationen växer. I Azure Automation, kan processer vara startats manuellt, med 3 part eller med schemalagda intervall så att uppgifter inträffa exakt när det behövs.

Minska driftsomkostnader och frigör IT / DevOps-personal att fokusera på arbete som lägger till företag värde genom att flytta din molnhanteringsuppgifter att köras automatiskt av Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Hur kan Azure Automation till att hantera Azure-molntjänster?
Azure-molntjänster kan hanteras i Azure Automation med hjälp av PowerShell-cmdlets som är tillgängliga i den [Azure PowerShell-verktyg](/powershell/). Azure Automation har dessa molnet PowerShell cmdlet: ar tillgängliga direkt, så att du kan utföra alla dina molntjänsthantering i tjänsten. Du kan också koppla dessa cmdletar i Azure Automation med cmdlets för andra Azure-tjänster, att automatisera avancerade uppgifter över Azure-tjänster och system med 3 part.

Vissa exempel användningsområden för Azure Automation för att hantera Azure Cloud Services är:

* [Kontinuerlig distribution av en tjänst i molnet när cscfg eller cspkg uppdateras i Azure Blob storage](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Starta om Cloud Service-instanser samtidigt, en uppgraderingsdomän i taget](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Azure Automation och hur den kan användas för att hantera Azure-molntjänster kan du följa dessa länkar om du vill veta mer om Azure Automation.

* [Översikt över Azure Automation](../automation/automation-intro.md)
* [Min första Runbook](../automation/automation-first-runbook-graphical.md)
* [Inlärningskarta för Azure Automation](https://azure.microsoft.com/documentation/learning-paths/automation/)
