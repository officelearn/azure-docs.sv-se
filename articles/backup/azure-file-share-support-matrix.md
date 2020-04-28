---
title: Support mat ris för säkerhets kopiering av Azure-filresurs
description: Innehåller en översikt över support inställningar och begränsningar när du säkerhetskopierar Azure-filresurser.
ms.topic: conceptual
ms.date: 1/26/2020
ms.openlocfilehash: 03190c6b83fceec88442583a57ff46756cfe6221
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195697"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Support mat ris för säkerhets kopiering av Azure-filresurs

Du kan använda [tjänsten Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) för att säkerhetskopiera Azure-filresurser. I den här artikeln sammanfattas support inställningarna när du säkerhetskopierar Azure-filresurser med Azure Backup.

## <a name="supported-geos"></a>GEOGRAFISKA områden som stöds

Säkerhets kopiering av Azure-filresurser finns i följande geografiska områden:

| GA-regioner | Regioner som stöds men inte GA                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Sydöstra Australien (ASE), Kanada, centrala (CNC), västra centrala USA (WCUS), västra USA 2 (WUS 2), södra Indien (INS), norra centrala USA (NCUS), Östra Japan (JPE), södra Brasilien (BRS), Asien, sydöstra (SEA)                                                     |Östra Australien (AE), Kanada, östra (CE), Asien, östra (EA), östra USA (EUS), östra USA 2 (EUS2), västra Japan (JPW). Indien, centrala (KRC), Korea, södra (KRS), Nord Europa (NE), södra centrala USA (SCUS), Storbritannien, södra (UKS), Storbritannien, västra (UKW), Västeuropa (USA), västra USA (WUS), US gov, Arizona (UGA), US gov, Texas (UGT), (UGV), Australien, centrala (ACL) US gov, Virginia , Västra Indien (INW), södra Afrika, norra (SAN), Förenade Arabemiraten Nord (UAN), Frankrike Central (FRC), Tyskland, norra (GN), Tyskland, västra centrala (GWC), södra Afrika (såg), Förenade Arabemiraten Central (UAC), Norge, östra (NVI), Norge, västra (NWW Schweiz, norra), SZN (), centrala USA (CUS)           |

## <a name="supported-storage-accounts"></a>Lagringskonton som stöds

| Information om lagrings konto | Support                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Typ av konto            | Azure Backup stöder Azure-filresurser som finns i generell användning v1, General-Purpose v2 och fil resurs typ lagrings konton |
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

| Inställning                                                      | Gräns |
| ------------------------------------------------------------ | ----- |
| Maximalt antal fil resurser som kan skyddas per dag per valv | 200   |
| Maximalt antal lagrings konton som kan registreras per valv per dag | 50    |

## <a name="backup-limits"></a>Säkerhets kopierings gränser

| Inställning                                      | Gräns |
| -------------------------------------------- | ----- |
| Maximalt antal säkerhets kopior på begäran per dag | 4     |
| Maximalt antal schemalagda säkerhets kopieringar per dag | 1     |

## <a name="restore-limits"></a>Återställa gränser

| Inställning                                                      | Gräns   |
| ------------------------------------------------------------ | ------- |
| Maximalt antal återställningar per dag                           | 10      |
| Maximalt antal filer per återställning                         | 10      |
| Maximal rekommenderad återställnings storlek per återställning för stora fil resurser | 15 TiB |

## <a name="retention-limits"></a>Gräns för kvarhållning

| Inställning                                                      | Gräns    |
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
