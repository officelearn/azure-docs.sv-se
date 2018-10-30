---
title: Azure Resource Manager-mallar för Azure Backup
description: PowerShell-exempel för Azure Backup
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 04/18/2018
ms.author: markgal
ms.custom: mvc
ms.openlocfilehash: 941357d0a8b7fb46a5c2dcebb70681452118f23f
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406389"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Azure Resource Manager-mallar för Azure Backup

I följande tabell finns länkar till Azure Resource Manager-mallar du kan använda för Recovery Services-valv och funktioner i Azure Backup.

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
| [Använda Log Analytics till att övervaka Azure Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | Mallen distribuerar Log Analytics-övervakning för Azure Backup så att du kan övervaka säkerhetskopierings- och återställningsjobb, aviseringar om säkerhetskopiering och den molnlagring som används i dina Recovery Services-valv.|  
|   |   |

