---
title: Hantera Azure-Cloud Services med Azure Automation | Microsoft Docs
description: Lär dig mer om hur Azure Automation-tjänsten kan användas för att hantera Azure Cloud Services i stor skala.
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
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72439068"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Hantera Azure-Cloud Services med Azure Automation
I den här guiden får du lära dig Azure Automation tjänsten och hur den kan användas för att förenkla hanteringen av dina Azure Cloud Services.

## <a name="what-is-azure-automation"></a>Vad är Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) är en Azure-tjänst för att förenkla moln hantering genom process automatisering. Att använda Azure Automation, långvariga, manuella, fel känsliga och ofta återkommande uppgifter kan automatiseras för att öka tillförlitligheten, effektiviteten och tiden för din organisation.

Azure Automation ger en mycket tillförlitlig och hög tillgänglig arbets flödes körnings motor som kan skalas för att uppfylla dina behov när din organisation växer. I Azure Automation kan processer startas manuellt, av system från tredje part eller med schemalagda intervall så att aktiviteterna sker exakt vid behov.

Sänk drifts kostnader och frigör IT/DevOps-personalen och fokusera på arbete som lägger till affärs värde genom att flytta dina moln hanterings uppgifter så att de körs automatiskt av Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Hur kan Azure Automation hjälpa till att hantera Azure Cloud Services?
Azure Cloud Services kan hanteras i Azure Automation med hjälp av PowerShell-cmdletar som är tillgängliga i [Azure PowerShell-verktygen](/powershell/). Azure Automation har följande PowerShell-cmdlets för moln tjänster tillgängliga i rutan så att du kan utföra alla dina moln tjänst hanterings uppgifter i tjänsten. Du kan också para ihop dessa cmdlets i Azure Automation med cmdletar för andra Azure-tjänster, för att automatisera komplexa aktiviteter i Azure-tjänster och system från tredje part.

Några exempel på användning av Azure Automation för att hantera Azure Cloud Services är:

* [Kontinuerlig distribution av en moln tjänst när cscfg eller cspkg uppdateras i Azure Blob Storage](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Starta om moln tjänst instanser parallellt, en uppgraderings domän i taget](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om Azure Automation och hur det kan användas för att hantera Azure Cloud Services, kan du följa dessa länkar om du vill veta mer om Azure Automation.

* [Översikt över Azure Automation](../automation/automation-intro.md)
* [Min första Runbook](../automation/automation-first-runbook-graphical.md)
