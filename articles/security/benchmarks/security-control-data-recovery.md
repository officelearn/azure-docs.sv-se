---
title: Azure Security Control - Data Recovery
description: Azure Security Control dataåterställning
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 4f3e8540902809f951a441aa2fe8d00026c44d82
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408605"
---
# <a name="security-control-data-recovery"></a>Säkerhetskontroll: Dataåterställning

Se till att alla systemdata, konfigurationer och hemligheter säkerhetskopieras automatiskt regelbundet.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1: Se till att regelbundna automatiska säkerhetskopieringar

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 9.1 | 10.1 | Kund |

Aktivera Azure Backup och konfigurera säkerhetskopieringskällan (Virtuella Azure-datorer, SQL Server eller Filresurser) samt önskad frekvens och kvarhållningsperiod.

- [Aktivera Azure Backup](https://docs.microsoft.com/azure/backup/)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera alla kundhanterade nycklar

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 9.2 | 10.2 | Kund |

Aktivera Azure Backup och mål-VM,samt önskad frekvens och kvarhållningsperioder. Hanterade kundhanterade nycklar för säkerhetskopiering i Azure Key Vault.

- [Aktivera Azure Backup](https://docs.microsoft.com/azure/backup/)

- [Så här säkerhetskopierar du nycklar i Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validera alla säkerhetskopior inklusive kundhanterade nycklar

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 9.3 | 10.3 | Kund |

Se till att data återställs regelbundet i Azure Backup. Testa återställning av säkerhetskopierade kundhanterade nycklar.

- [Återställa filer från säkerhetskopiering av virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm)

- [Återställa nyckelnycklar i Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Se till att säkerhetskopior och kundhanterade nycklar skyddas

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 9.4 | 10.4 | Kund |

Vid lokal säkerhetskopiering tillhandahålls kryptering i vila med hjälp av den lösenfras som du anger när du säkerhetskopierar till Azure. För virtuella Azure-datorer krypteras data i vila med hjälp av kryptering för lagringstjänst (SSE). Använd rollbaserad åtkomstkontroll för att skydda säkerhetskopior och kundhanterade nycklar.  

Aktivera soft-delete och rensa skydd i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.  Om Azure Storage används för att lagra säkerhetskopior aktiverar du mjuk borttagning för att spara och återställa dina data när blobbar eller blob-ögonblicksbilder tas bort. 

- [Förstå Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

- [Aktivera skydd för mjuk borttagning och rensning i Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Mjuk borttagning för Azure Storage-blobar](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)


## <a name="next-steps"></a>Nästa steg

- Se nästa säkerhetskontroll: [Incident Response](security-control-incident-response.md)