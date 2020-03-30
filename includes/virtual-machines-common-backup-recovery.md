---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: be71b269e618d13a126e4005754b307e9c6517d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187337"
---
## <a name="azure-backup"></a>Azure Backup

Om du säkerhetskopierar virtuella Azure-datorer som kör produktionsarbetsbelastningar använder du Azure Backup. Azure Backup stöder programkonsekventa säkerhetskopior för både Virtuella Datorer i Windows och Linux. Med Azure Backup skapas återställningspunkter som lagras i geo-redundanta återställningsvalv. När du återställer från en återställningspunkt kan du återställa hela den virtuella datorn eller bara specifika filer. 

En enkel och praktisk introduktion till virtuella Azure-datorer för Azure finns i självstudien "Säkerhetskopiera virtuella Azure-datorer" för [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) eller [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).

Mer information om hur Azure Backup fungerar finns i [Planera infrastrukturen för säkerhetskopiering av virtuella datorer i Azure](../articles/backup/backup-azure-vms-introduction.md)


## <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery skyddar dina virtuella datorer från ett större katastrofscenario, när en hel region drabbas av ett avbrott på grund av större naturkatastrof eller omfattande avbrott i tjänsten. Du kan konfigurera Azure Site Recovery för dina virtuella datorer så att du kan återställa ditt program med ett enda klick på några minuter. Du kan replikera till en Azure-region som du väljer, den är inte begränsad till parkopplade regioner. 

Du kan köra haveriberedskapsövningar med testundansväxlingar på begäran, utan att påverka dina produktionsarbetsbelastningar eller pågående replikering. Skapa återställningsplaner för att dirigera redundans och redundans för hela programmet som körs på flera virtuella datorer. Återställningsplanfunktionen är integrerad med Azure automation runbooks.

Du kan komma igång genom [att replikera dina virtuella datorer](https://aka.ms/a2a-getting-started). 

## <a name="managed-snapshots"></a>Hanterade ögonblicksbilder 

I utvecklings- och testmiljöer är ögonblicksbilder ett snabbt och enkelt alternativ för säkerhetskopiering av virtuella datorer som använder hanterade diskar. En hanterad ögonblicksbild är en skrivskyddad fullständig kopia av en hanterad disk. Ögonblicksbilder finns oberoende av källdisken och kan användas för att skapa nya hanterade diskar för att återskapa en virtuell dator. De faktureras baserat på den använda delen av disken. Om du till exempel skapar en ögonblicksbild av en hanterad disk med etablerad kapacitet på 64 GB och en faktisk använd datastorlek på 10 GB, debiteras ögonblicksbild endast för den använda datastorleken på 10 GB.  

Mer information om hur du skapar ögonblicksbilder finns i:

* [Skapa kopia av en virtuell hårddisk som lagras som en hanterad disk med hjälp av ögonblicksbilder i Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Skapa kopia av en virtuell hårddisk som lagras som en hanterad disk med hjälp av ögonblicksbilder i Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>Nästa steg
Du kan prova Azure Backup genom att följa "Säkerhetskopiera Windows virtuella datorer tutorial" för [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) eller [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).
