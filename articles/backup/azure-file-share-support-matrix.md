---
title: Support mat ris för säkerhets kopiering av Azure-filresurs
description: Innehåller en översikt över support inställningar och begränsningar när du säkerhetskopierar Azure-filresurser.
ms.topic: conceptual
ms.date: 5/07/2020
ms.custom: references_regions
ms.openlocfilehash: 72492ea77534b636d90820c96e36163e6c6efebe
ms.sourcegitcommit: 20e246e86e25d63bcd521a4b4d5864fbc7bad1b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84488468"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Support mat ris för säkerhets kopiering av Azure-filresurs

Du kan använda [tjänsten Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) för att säkerhetskopiera Azure-filresurser. I den här artikeln sammanfattas support inställningarna när du säkerhetskopierar Azure-filresurser med Azure Backup.

## <a name="supported-regions"></a>Regioner som stöds

### <a name="ga-regions-for-azure-file-shares-backup"></a>GA-regioner för säkerhets kopiering av Azure-filresurser

Säkerhets kopiering av Azure-filresurser är tillgänglig i alla regioner **utom** : Tyskland, Tyskland (suverän), Tyskland nordöstra (suverän), Kina, östra, Kina, östra 2, Kina, norra, Kina, norra 2, US gov, Iowa

### <a name="supported-regions-for-accidental-delete-protection"></a>Regioner som stöds för oavsiktligt borttagnings skydd

Västra centrala USA, östra Australien, södra centrala Kanada

## <a name="supported-storage-accounts"></a>Lagringskonton som stöds

| Information om lagrings konto | Support                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Typ av konto            | Azure Backup stöder Azure-filresurser som finns i generell användning v1, General-Purpose v2 och lagrings konton för fil lagrings typ |
| Prestanda              | Azure Backup stöder fil resurser i både standard-och Premium Storages konton |
| Replikering              | Azure Files-resurser i lagrings konton med valfri replikeringstyp stöds |

## <a name="supported-file-shares"></a>Fil resurser som stöds

| Fil resurs typ                                   | Support   |
| -------------------------------------------------- | --------- |
| Standard                                           | Stöds |
| Stor                                              | Stöds |
| Premium                                            | Stöds |
| Fil resurser som är anslutna till Azure File Sync-tjänsten | Stöds |

## <a name="protection-limits"></a>Skydds gränser

| Inställningen                                                      | Gräns |
| ------------------------------------------------------------ | ----- |
| Maximalt antal fil resurser som kan skyddas per dag per valv | 200   |
| Maximalt antal lagrings konton som kan registreras per valv per dag | 50    |

## <a name="backup-limits"></a>Säkerhets kopierings gränser

| Inställningen                                      | Gräns |
| -------------------------------------------- | ----- |
| Maximalt antal säkerhets kopior på begäran per dag | 10   |
| Maximalt antal schemalagda säkerhets kopieringar per dag | 1     |

## <a name="restore-limits"></a>Återställa gränser

| Inställningen                                                      | Gräns   |
| ------------------------------------------------------------ | ------- |
| Maximalt antal återställningar per dag                           | 10      |
| Maximalt antal filer per återställning                         | 10      |
| Maximal rekommenderad återställnings storlek per återställning för stora fil resurser | 15 TiB |

## <a name="retention-limits"></a>Gräns för kvarhållning

| Inställningen                                                      | Gräns    |
| ------------------------------------------------------------ | -------- |
| Maximalt antal återställnings punkter per fil resurs vid varje tidpunkt | 200      |
| Högsta kvarhållning av återställnings punkt som skapats av säkerhets kopiering på begäran | 10 år |
| Maximal kvarhållning av dagliga återställnings punkter (ögonblicks bilder) per fil resurs| 200 dagar |
| Maximal kvarhållning av veckovis återställnings punkter (ögonblicks bilder) per fil resurs | 200 veckor |
| Högsta kvarhållning av månads återställnings punkter (ögonblicks bilder) per fil resurs | 120 månader |
| Högsta kvarhållning av årliga återställnings punkter (ögonblicks bilder) per fil resurs | 10 år |

## <a name="supported-restore-methods"></a>Återställnings metoder som stöds

| Restore-metod     | Information                                                      |
| ------------------ | ------------------------------------------------------------ |
| Fullständig delnings återställning | Du kan återställa den fullständiga fil resursen till den ursprungliga eller en annan plats |
| Återställning på objekt nivå | Du kan återställa enskilda filer och mappar till den ursprungliga eller en annan plats |

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [säkerhetskopierar Azure-filresurser](backup-afs.md)
* Lär dig hur du [återställer Azure-filresurser](restore-afs.md)
* Lär dig hur du [hanterar säkerhets kopior av Azure-filresurser](manage-afs-backup.md)
