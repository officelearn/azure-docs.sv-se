---
title: Azure Resource Manager-mallar för Azure Backup
description: PowerShell-exempel för Azure Backup
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 01/31/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c0b023b6bdd01bc64eb5a6deb5fafa8e3c34c616
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819028"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Azure Resource Manager-mallar för Azure Backup

I följande tabell finns länkar till Azure Resource Manager-mallar du kan använda för Recovery Services-valv och funktioner i Azure Backup. Mer information om JSON-syntaxen och JSON-egenskaperna finns i [Microsoft.RecoveryServices-resurstyper](/azure/templates/microsoft.recoveryservices/allversions).

|   |   |
|---|---|
|**Recovery Services-valv** | |
| [Skapa ett Recovery Services-valv](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Skapa ett Recovery Services-valv. Du kan använda valvet för Azure Backup och Azure Site Recovery. |
|**Säkerhetskopiera virtuella datorer**| |
| [Säkerhetskopiera virtuella Resource Manager-datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | Använd det befintliga Recovery Services-valvet och Backup-principen för säkerhetskopiering av virtuella Resource Manager-datorer i samma resursgrupp.|
| [Säkerhetskopiera virtuella IaaS-datorer till ett Recovery Services-valv](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | Mall för säkerhetskopiering av klassiska virtuella datorer och virtuella Resource Manager-datorer. |
| [Skapa en princip för veckovis säkerhetskopiering av virtuella IaaS-datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | Mallen skapar ett Recovery Services-valv och en princip för veckovis säkerhetskopiering som används till att säkerhetskopiera klassiska virtuella datorer och virtuella Resource Manager-datorer.|
| [Skapa en princip för daglig säkerhetskopiering av virtuella IaaS-datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | Mallen skapar ett Recovery Services-valv och en princip för daglig säkerhetskopiering som används till att säkerhetskopiera klassiska virtuella datorer och virtuella Resource Manager-datorer.|
| [Distribuera virtuella Windows Server-datorer med säkerhetskopiering aktiverad](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | Mallen skapar en virtuell Windows Server-dator och ett Recovery Services-valv med standardprincipen för säkerhetskopiering aktiverad.|
|**Övervaka säkerhetskopieringsjobb** |  |
| [Använda Azure Monitor-loggar med Azure Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | Mallen distribuerar Azure Monitor-loggar med Azure Backup så att du kan övervaka säkerhetskopierings- och återställningsjobb, aviseringar om säkerhetskopiering samt den molnlagring som används i dina Recovery Services-valv.|  
|   |   |

