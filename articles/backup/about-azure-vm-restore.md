---
title: Om återställnings processen för den virtuella Azure-datorn
description: Lär dig hur tjänsten Azure Backup återställer virtuella Azure-datorer
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: b9a2636a2144ea40457bdc3d88786785cb012e0d
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84325826"
---
# <a name="about-azure-vm-restore"></a>Om återställning av virtuella Azure-datorer

Den här artikeln beskriver hur [Azure Backup tjänsten](https://docs.microsoft.com/azure/backup/backup-overview) återställer virtuella Azure-datorer (VM). Det finns ett antal återställnings alternativ. Vi diskuterar de olika scenarier som de stöder.

## <a name="concepts"></a>Begrepp

- **Återställnings punkt** (kallas även **återställnings punkt**): en återställnings punkt är en kopia av de ursprungliga data som säkerhets kopie ras.

- **Nivå (ögonblicks bild eller valv)**: Azure VM-säkerhetskopiering sker i två faser:

  - I fas 1 lagras den ögonblicks bild som tagits, tillsammans med disken. Detta kallas för **ögonblicks bild nivå**. Återställningar av ögonblicks bild nivåer är snabbare (än återställning från valvet) eftersom de eliminerar vänte tiden för ögonblicks bilder som ska kopieras till valvet innan återställningen utlöses. Återställning från ögonblicks bilds nivån kallas även för [omedelbar återställning](https://docs.microsoft.com/azure/backup/backup-instant-restore-capability).
  - I fas 2 överförs ögonblicks bilden och lagras i valvet som hanteras av Azure Backups tjänsten. Detta kallas för **valv nivå**.

- **Återställning av ursprunglig plats (OLR)**: en återställning som gjorts från återställnings punkten till den virtuella Azure-datorns virtuella Azure-dator från vilken säkerhets kopieringen gjordes, ersätter den med det tillstånd som lagrades i återställnings punkten. Detta ersätter OS-disken och data diskarna för den virtuella käll datorn.

- **Återställning av alternativ plats (återställning till)**: en återställning utfördes från återställnings punkten till en annan server än den ursprungliga servern där säkerhets kopiorna gjordes.

- **Återställning på objekt nivå (ILR):** Återställa enskilda filer eller mappar inuti den virtuella datorn från återställnings punkten

- **Tillgänglighet (typer av replikering)**: Azure Backup erbjuder två typer av replikering för att hålla lagringen/data hög tillgängliga:
  - [Lokalt redundant lagring (LRS)](../storage/common/storage-redundancy-lrs.md) replikerar dina data tre gånger (det skapas tre kopior av dina data) i en lagringsskalningsenhet i ett datacenter. Alla datakopior finns i samma region. LRS är ett billigt alternativ för att skydda dina data mot fel i den lokala maskinvaran.
  - [Geo-redundant lagring (GRS)](../storage/common/storage-redundancy-grs.md) är standardalternativet och det som rekommenderas vid replikering. GRS replikerar dina data till en sekundär region (hundratals mil bort från den primära platsen för datakällan). GRS kostar mer än LRS, men GRS ger en högre nivå av hållbarhet för dina data, även om det finns ett regionalt avbrott.

- **Återställning mellan regioner (CRR)**: som ett av [återställnings alternativen](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options)kan du med återställningen mellan regioner (CRR) återställa virtuella Azure-datorer i en sekundär region, som är en [Azure-kopplad region](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions).

## <a name="restore-scenarios"></a>Återställnings scenarier

![Återställnings scenarier ](./media/about-azure-vm-restore/recovery-scenarios.png)

| **Scenario**                                                 | **Vad är det som gör**                                             | **När du ska använda detta**                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Återställ för att skapa en ny virtuell dator](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms) | Återställer hela den virtuella datorn till OLR (om den virtuella käll datorn fortfarande finns) eller återställning till | <li> Om den virtuella käll datorn tappas bort eller skadas kan du återställa hela den virtuella datorn  <li> Du kan skapa en kopia av den virtuella datorn  <li> Du kan utföra en Restore-granskning för granskning eller efterlevnad  <li> Det här alternativet fungerar inte för virtuella Azure-datorer som skapats från Marketplace-avbildningar (det vill säga om de inte är tillgängliga eftersom licensen har upphört att gälla) |
| [Återställa diskar för den virtuella datorn](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) | Återställa diskar som är anslutna till den virtuella datorn                             |  Alla diskar: det här alternativet skapar mallen och återställer disken. Du kan redigera den här mallen med särskilda konfigurationer (till exempel tillgänglighets uppsättningar) för att uppfylla dina krav och sedan använda båda mallarna och återställa disken för att återskapa den virtuella datorn. |
| [Återställa vissa filer på den virtuella datorn](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) | Välj återställnings punkt, bläddra, Välj filer och Återställ dem till samma (eller kompatibla) OS som den säkerhetskopierade virtuella datorn. |  Om du vet vilka filer som ska återställas använder du det här alternativet i stället för att återställa hela den virtuella datorn. |
| [Återställa en krypterad virtuell dator](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) | Från portalen återställer du diskarna och använder sedan PowerShell för att skapa den virtuella datorn | <li> [Krypterad virtuell dator med Azure Active Directory (AAD)](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-windows-aad)  <li> [Krypterad virtuell dator utan AAD](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-windows) <li> [Krypterad virtuell dator *med AAD* migrerad till *utan AAD*](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-faq#can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app) |
| [Återställning mellan regioner](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#cross-region-restore) | Skapa en ny virtuell dator eller Återställ diskar till en sekundär region (Azure-kopplad region) | <li> **Fullständigt avbrott**: med funktionen för återställning av kors region finns det ingen vänte tid för att återställa data i den sekundära regionen. Du kan initiera återställningar i den sekundära regionen även innan Azure deklarerar ett avbrott. <li> **Partiellt avbrott**: nedtid kan uppstå i vissa lagrings kluster där Azure Backup lagrar dina säkerhetskopierade data eller till och med i nätverket, ansluter Azure Backup och lagrings kluster som är kopplade till dina säkerhetskopierade data. Med återställning mellan regioner kan du utföra en återställning i den sekundära regionen med hjälp av en replik av säkerhetskopierade data i den sekundära regionen. <li> **Inget avbrott**: du kan utföra granskningar av verksamhets kontinuitet och haveri beredskap (BCDR) för granskning eller efterlevnad med data för den sekundära regionen. På så sätt kan du utföra en återställning av säkerhetskopierade data i den sekundära regionen även om det inte finns ett fullständigt eller delvis avbrott i den primära regionen för verksamhets kontinuitet och haveri beredskap.  |

------





## <a name="next-steps"></a>Nästa steg

- [Vanliga frågor och svar om återställning av virtuella datorer](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq#restore)
- [Återställnings metoder som stöds](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#supported-restore-methods)
- [Felsök återställnings problem](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#restore)
