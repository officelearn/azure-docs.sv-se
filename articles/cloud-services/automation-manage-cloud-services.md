---
title: Hantera Azure Cloud Services med Azure Automation | Microsoft Docs
description: "Läs mer om hur Azure Automation-tjänsten kan användas för att hantera Azure-molntjänster i större skala."
services: cloud-services, automation
documentationcenter: 
author: jodoglevy
manager: timlt
editor: 
ms.assetid: 3789810a-2892-4eef-bf29-c781c1b5af48
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2016
ms.author: timlt
ms.openlocfilehash: 6b5acac1b8647c324988c316cd5602b3dba98a1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Hantera Azure Cloud Services med Azure Automation
Den här guiden innehåller en introduktion till Azure Automation-tjänsten och hur det kan användas för att förenkla hanteringen av dina Azure-molntjänster.

## <a name="what-is-azure-automation"></a>Vad är Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) är en Azure-tjänst som förenklar molnhantering via automatisering. Med Azure Automation kan tidskrävande, manuell, felbenägna och ofta återkommande uppgifter automatiseras för att öka tillförlitligheten, effektivitet och tid för värde för din organisation.

Azure Automation ger en Körningsmotor för arbetsflöden för hög tillförlitliga och hög tillgänglighet som kan skalas för att uppfylla dina behov när organisationen växer. I Azure Automation kan processer vara inletts manuellt av 3 part eller med schemalagda intervall så att aktiviteter inträffa exakt vid behov.

Lägre operativ tillsyn och frigöra IT / DevOps-personal att fokusera på arbete som lägger till företag värde genom att flytta dina uppgifter för hantering av molnet att köras automatiskt av Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Hur Azure Automation kan hantera Azure-molntjänster?
Azure-molntjänster kan hanteras i Azure Automation med hjälp av PowerShell-cmdlets som är tillgängliga i den [Azure PowerShell verktygen](https://msdn.microsoft.com/library/azure/jj156055.aspx). Azure Automation har dessa moln PowerShell cmdlet: ar tillgängliga direkt, så att du kan utföra alla cloud service management aktiviteter inom tjänsten. Du kan också koppla dessa cmdlets i Azure Automation med cmdlets för andra Azure-tjänster, att automatisera avancerade uppgifter över Azure-tjänster och 3 part.

Vissa exempel användningsområden för Azure Automation för att hantera Azure Cloud Services är:

* [Kontinuerlig distribution av en tjänst i molnet när cscfg eller cspkg uppdateras i Azure Blob storage](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Starta om Molntjänsten instanser parallellt, en domän i taget](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Azure Automation och hur det kan användas för att hantera Azure-molntjänster kan du följa dessa länkar om du vill veta mer om Azure Automation.

* [Azure Automation-översikt](../automation/automation-intro.md)
* [Min första Runbook](../automation/automation-first-runbook-graphical.md)
* [Azure Automation-inlärningsguide](https://azure.microsoft.com/documentation/learning-paths/automation/)
