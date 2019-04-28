---
title: ta med fil
description: ta med fil
services: virtual-machines
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 03/09/2018
ms.date: 04/16/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: be71b269e618d13a126e4005754b307e9c6517d7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543721"
---
## <a name="azure-backup"></a>Azure Backup

Använda Azure Backup för att säkerhetskopiera virtuella Azure-datorer som kör produktionsarbetsbelastningar. Azure Backup stöder programkonsekventa säkerhetskopior för både Windows och Linux-datorer. Med Azure Backup skapas återställningspunkter som lagras i geo-redundanta återställningsvalv. När du återställer från en återställningspunkt kan du återställa hela den virtuella datorn eller bara specifika filer. 

En enkel, praktisk introduktion till Azure Backup för virtuella Azure-datorer, finns i ”Säkerhetskopiera Azure virtual machines”-självstudiekurs för [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) eller [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).

Mer information om hur Azure Backup fungerar finns i [planera din infrastruktur för VM-säkerhetskopiering i Azure](../articles/backup/backup-azure-vms-introduction.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery skyddar dina virtuella datorer från en större katastrof i scenariot när en hel region uppstår ett avbrott på grund av större naturkatastrofer eller utökas tjänstavbrott. Du kan konfigurera Azure Site Recovery för dina virtuella datorer så att du kan återställa ditt program med ett enda klick på bara minuter. Du kan replikera till en Azure-region du väljer, är inte begränsat till ihopparade regioner. 

Du kan köra tester av haveriberedskap med redundanstester på begäran, utan att påverka dina produktionsarbetsbelastningar eller pågående replikering. Skapa återställningsplaner för att dirigera redundans och återställning efter fel för hela programmet som körs på flera virtuella datorer. Recovery plan-funktionen är integrerad med Azure automation-runbooks.

Du kan komma igång med [replikera dina virtuella datorer](https://aka.ms/a2a-getting-started). 

## <a name="managed-snapshots"></a>Hanterade ögonblicksbilder 

I utvecklings- och testmiljöer erbjuder ögonblicksbilder alternativ för snabb och enkel för att säkerhetskopiera virtuella datorer som använder Managed Disks. En hanterad ögonblicksbild är en skrivskyddad fullständig kopia av en hanterad disk. Ögonblicksbilder finnas oberoende av källdisken och kan användas för att skapa nya hanterade diskar för att återskapa en virtuell dator. De faktureras baserat på Använd på disken. Om du till exempel skapar en ögonblicksbild av en hanterad disk med etablerad kapacitet på 64 GB och faktisk använd datastorlek på 10 GB så debiteras ögonblicksbilden endast för den använda datastorleken på 10 GB.  

Mer information om hur du skapar ögonblicksbilder finns:

* [Skapa kopia av en virtuell hårddisk som lagras som en hanterad disk med hjälp av ögonblicksbilder i Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Skapa kopia av en virtuell hårddisk som lagras som en hanterad disk med hjälp av ögonblicksbilder i Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

## <a name="next-steps"></a>Nästa steg
Du kan prova att använda Azure Backup genom att följa ”säkerhetskopiering av Windows-datorer självstudien” i [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) eller [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).