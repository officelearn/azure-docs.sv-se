---
title: Hantera Azure Storage med Azure Automation
description: Läs mer om hur Azure Automation-tjänsten kan användas för att hantera Azure Storage i stor skala.
services: storage, automation
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/10/2019
ms.author: tamram
ms.component: common
ms.openlocfilehash: 0cf8a1ee90757b9a89e7659282b2fd52fa436dea
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245080"
---
# <a name="managing-azure-storage-using-azure-automation"></a>Hantera Azure Storage med Azure Automation
Den här guiden innehåller en introduktion till Azure Automation-tjänsten och hur den kan användas för att förenkla hanteringen av dina Azure Storage-blobbar, tabeller och köer.

## <a name="what-is-azure-automation"></a>Vad är Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) är en Azure-tjänst för att förenkla molnhantering med Processautomatisering. Med Azure Automation, tidskrävande, manuell, felbenägna och regelbundet återkommande uppgifter kan automatiseras för att öka tillförlitligheten och effektiviteten och minska tiden till värde för din organisation.

Azure Automation tillhandahåller en pålitlig och högtillgängliga motor för arbetsflödeskörning som kan skalas efter dina behov när organisationen växer. I Azure Automation kan processer vara startats manuellt, genom att program från tredje part eller med schemalagda intervall så att uppgifter inträffa exakt när det behövs.

Minska driftsomkostnader och frigör IT / DevOps-personal att fokusera på arbete som lägger till företag värde genom att flytta din molnhanteringsuppgifter att köras automatiskt av Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-storage"></a>Hur kan Azure Automation till att hantera Azure Storage?
Azure Storage kan hanteras i Azure Automation med hjälp av PowerShell-cmdlets som är tillgängliga i [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Azure Automation har dessa Storage PowerShell-cmdletar som är tillgängliga direkt, så att du kan utföra alla blob-, tabell-och kön hanteringsuppgifter i tjänsten. Du kan också koppla dessa cmdletar i Azure Automation med cmdlets för andra Azure-tjänster, att automatisera avancerade uppgifter över Azure-tjänster och program från tredje part.

Den [Azure Automation runbook-galleriet](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) innehåller produkt-teamet och community runbooks för att komma igång med att automatisera hanteringen av Azure Storage, andra Azure-tjänster och program från tredje part. Runbooks från galleriet är:

* [Ta bort Azure Storage-Blobbar som är vissa dagar gamla med hjälp av Automation-tjänsten](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
* [Ladda ned en Blob från Azure Storage](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
* [Säkerhetskopiera alla diskar för en virtuell Azure-dator eller för alla virtuella datorer i en molntjänst](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5)

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Azure Automation och hur den kan användas för att hantera Azure Storage-blobbar, tabeller och köer kan du följa dessa länkar om du vill veta mer om Azure Automation.

Finns i Azure Automation-självstudierna [skapa eller importera en runbook i Azure Automation](../../automation/automation-creating-importing-runbook.md).

