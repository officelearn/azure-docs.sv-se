---
title: Hantera Azure Storage med hjälp av Azure Automation
description: Läs mer om hur Azure Automation-tjänsten kan användas för att hantera Azure Storage i större skala.
services: storage, automation
documentationcenter: ''
author: jodoglevy
manager: eamono
editor: ''
ms.assetid: bac41c39-1d95-46aa-a481-ec17bbb21b40
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: eamono
ms.openlocfilehash: 4649e42a628307e15f8b067503e4e8e13f16f1af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873802"
---
# <a name="managing-azure-storage-using-azure-automation"></a>Hantera Azure Storage med hjälp av Azure Automation
Den här guiden innehåller en introduktion till Azure Automation-tjänsten och hur det kan användas för att förenkla hanteringen av dina Azure Storage-blobbar, tabeller och köer.

## <a name="what-is-azure-automation"></a>Vad är Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) är en Azure-tjänst som förenklar molnhantering via automatisering. Med Azure Automation tidskrävande, manuell, felbenägna och ofta återkommande uppgifter kan automatiseras för att öka tillförlitligheten och effektivitet och minska tiden till värdet för din organisation.

Azure Automation ger en Körningsmotor för arbetsflöden för hög tillförlitliga och hög tillgänglighet som kan skalas för att uppfylla dina behov när organisationen växer. I Azure Automation kan processer vara inletts manuellt av 3 part eller med schemalagda intervall så att aktiviteter inträffa exakt vid behov.

Lägre operativ tillsyn och frigöra IT / DevOps-personal att fokusera på arbete som lägger till företag värde genom att flytta dina uppgifter för hantering av molnet att köras automatiskt av Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-storage"></a>Hur Azure Automation kan hantera Azure Storage?
Azure Storage kan hanteras i Azure Automation med hjälp av PowerShell-cmdlets som är tillgängliga i [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Azure Automation har dessa lagring PowerShell-cmdlets som är tillgängliga direkt, så att du kan utföra alla blob, tabell och kön hanteringsuppgifter i tjänsten. Du kan också koppla dessa cmdlets i Azure Automation med cmdlets för andra Azure-tjänster, att automatisera avancerade uppgifter över Azure-tjänster och 3 part.

Den [Azure Automation runbook-galleriet](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) innehåller en mängd produkten team och community runbooks för att komma igång med att automatisera hanteringen av Azure Storage, andra Azure-tjänster och 3 part. Galleriet runbooks är:

* [Ta bort Azure Storage-Blobbar som är vissa dagar gamla med hjälp av Automation-tjänsten](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
* [Hämta en Blob från Azure Storage](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
* [Säkerhetskopiera alla diskar för en enskild virtuell dator i Azure eller för alla virtuella datorer i en tjänst i molnet](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5)

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Azure Automation och hur det kan användas för att hantera Azure Storage-blobbar, tabeller och köer, följa dessa länkar för att lära dig mer om Azure Automation.

Se Azure Automation-genomgång [skapa eller importera en runbook i Azure Automation](../../automation/automation-creating-importing-runbook.md).

