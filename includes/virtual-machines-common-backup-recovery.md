---
title: inkludera fil
description: inkludera fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: be71b269e618d13a126e4005754b307e9c6517d7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "67187337"
---
## <a name="azure-backup"></a>Azure Backup

Använd Azure Backup för att säkerhetskopiera virtuella Azure-datorer som kör produktions arbets belastningar. Azure Backup stöder programkonsekventa säkerhets kopieringar för virtuella Windows-och Linux-datorer. Med Azure Backup skapas återställningspunkter som lagras i geo-redundanta återställningsvalv. När du återställer från en återställningspunkt kan du återställa hela den virtuella datorn eller bara specifika filer. 

En enkel, praktisk introduktion till Azure Backup för virtuella Azure-datorer finns i själv studie kursen säkerhetskopiera virtuella Azure-datorer för [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) eller [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).

Mer information om hur Azure Backup fungerar finns i [Planera infrastrukturen för säkerhets kopiering av virtuella datorer i Azure](../articles/backup/backup-azure-vms-introduction.md)


## <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery skyddar dina virtuella datorer från ett större katastrof scenario, när en hel region upplever ett avbrott på grund av stor natur katastrof eller omfattande tjänst avbrott. Du kan konfigurera Azure Site Recovery för dina virtuella datorer så att du kan återställa ditt program med ett enda klick i flera minuter. Du kan replikera till en valfri Azure-region, men den är inte begränsad till kopplade regioner. 

Du kan köra granskningar av haveri beredskap med redundanstest på begäran, utan att påverka produktions arbets belastningarna eller pågående replikering. Skapa återställnings planer för att dirigera redundans och återställning efter fel för hela programmet som körs på flera virtuella datorer. Återställnings plan funktionen är integrerad med Azure Automation-runbooks.

Du kan komma igång genom att [Replikera dina virtuella datorer](https://aka.ms/a2a-getting-started). 

## <a name="managed-snapshots"></a>Hanterade ögonblicks bilder 

I utvecklings-och test miljöer ger ögonblicks bilder ett snabbt och enkelt alternativ för att säkerhetskopiera virtuella datorer som använder Managed Disks. En hanterad ögonblicks bild är en skrivskyddad fullständig kopia av en hanterad disk. Ögonblicks bilder finns oberoende av käll disken och kan användas för att skapa nya hanterade diskar för att återskapa en virtuell dator. De faktureras baserat på den använda delen av disken. Om du till exempel skapar en ögonblicks bild av en hanterad disk med en etablerad kapacitet på 64 GB och den faktiska använda data storleken på 10 GB faktureras ögonblicks bilden endast för den använda data storleken på 10 GB.  

Mer information om hur du skapar ögonblicks bilder finns i:

* [Skapa kopia av en virtuell hårddisk som lagras som en hanterad disk med hjälp av ögonblicksbilder i Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Skapa kopia av en virtuell hårddisk som lagras som en hanterad disk med hjälp av ögonblicksbilder i Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>Nästa steg
Du kan prova Azure Backup genom att följa själv studie kursen säkerhetskopiera virtuella Windows-datorer för [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) eller [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).
