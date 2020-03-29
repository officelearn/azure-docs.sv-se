---
title: Azure Security Control - Data Recovery
description: Återställning av data för säkerhetskontroll
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: c585ebd903d4070f6247456e06efffbc6ec45270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934497"
---
# <a name="security-control-data-recovery"></a>Säkerhetskontroll: Dataåterställning

Se till att alla systemdata, konfigurationer och hemligheter säkerhetskopieras automatiskt regelbundet.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1: Se till att regelbundna automatiska säkerhetskopieringar

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 9.1 | 10.1 | Kund |

Aktivera Azure Backup och konfigurera säkerhetskopieringskällan (Virtuella Azure-datorer, SQL Server eller Filresurser) samt önskad frekvens och kvarhållningsperiod.

Aktivera Azure Backup:

https://docs.microsoft.com/azure/backup/

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera alla kundhanterade nycklar

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 9.2 | 10.2 | Kund |

Aktivera Azure Backup och mål-VM,samt önskad frekvens och kvarhållningsperioder. Hanterade kundhanterade nycklar för säkerhetskopiering i Azure Key Vault.

Aktivera Azure Backup:

https://docs.microsoft.com/azure/backup/

Så här säkerhetskopierar du nyckelnycklar i Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validera alla säkerhetskopior inklusive kundhanterade nycklar

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 9.3 | 10.3 | Kund |

Se till att data återställs regelbundet i Azure Backup. Testa vid behov återställa till en isolerad VLAN. Testa återställning av säkerhetskopierade kundhanterade nycklar.

Återställa filer från säkerhetskopiering av virtuella Azure-datorer:

https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm

Så här återställer du nyckelvalvsnycklar i Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Se till att säkerhetskopior och kundhanterade nycklar skyddas

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 9.4 | 10.4 | Kund |

Vid lokal säkerhetskopiering tillhandahålls kryptering i vila med hjälp av den lösenfras som du anger när du säkerhetskopierar till Azure. För virtuella Azure-datorer krypteras data i vila med hjälp av kryptering för lagringstjänst (SSE). Du kan aktivera Soft-Delete i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

Aktivera Mjuk borttagning i Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

## <a name="next-steps"></a>Nästa steg

Se nästa säkerhetskontroll: [Incident response](security-control-incident-response.md)
