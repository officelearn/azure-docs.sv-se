---
title: Azure säkerhets kontroll – Data återställning
description: Återställning av säkerhets kontroll data
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 1cd3e39fd504b5095a83192a4a6314c71d3ca980
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564301"
---
# <a name="security-control-data-recovery"></a>Säkerhets kontroll: Data återställning

Se till att alla system data, konfigurationer och hemligheter säkerhets kopie ras automatiskt regelbundet.

## <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 9,1 | 10.1 | Kund |

Aktivera Azure Backup och konfigurera säkerhets kopierings källan (virtuella Azure-datorer, SQL Server eller fil resurser) samt önskad frekvens och kvarhållningsperiod.

Så här aktiverar du Azure Backup: https://docs.microsoft.com/azure/backup/

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 9.2 | 10.2 | Kund |

Aktivera Azure Backup och mål-VM: er samt önskade frekvens-och kvarhållningsperiod. Säkerhetskopiera kund hanterade nycklar inom Azure Key Vault.

Så här aktiverar du Azure Backup: https://docs.microsoft.com/azure/backup/

Så här säkerhetskopierar du nyckel valv nycklar i Azure: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 9,3 | 10,3 | Kund |

Se till att regelbundet utföra Data återställning av innehåll inom Azure Backup. Om det behövs kan du testa att återställa till ett isolerat VLAN. Testa återställning av säkerhetskopierade nycklar som hanteras av kunden.

Så här återställer du filer från säkerhets kopiering av virtuella Azure-datorer:

https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm

Så här återställer du Key Vault-nycklar i Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 9.4 | 10,4 | Kund |

Vid lokal säkerhetskopiering tillhandahålls kryptering i vila med hjälp av den lösenfras som du anger när du säkerhetskopierar till Azure. För virtuella Azure-datorer krypteras data i vila med hjälp av kryptering för lagringstjänst (SSE). Du kan aktivera mjuk borttagning i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

Så här aktiverar du mjuk borttagning i Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

## <a name="next-steps"></a>Nästa steg

Se nästa säkerhets kontroll: [incident svar](security-control-incident-response.md)
