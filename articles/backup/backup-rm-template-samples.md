---
title: Azure Resource Manager-mallar för Azure Backup | Microsoft Docs
description: PowerShell-exempel för Azure Backup
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
tags: ''
ms.assetid: ''
ms.service: backup
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 04/18/2018
ms.author: markgal
ms.custom: mvc
ms.openlocfilehash: b8502e4e3934b36fb4c8ccac00f4fa14565780d9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
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
| [Använda OMS Log Analytics till att övervaka Azure Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | Mallen distribuerar OMS-övervakning för Azure Backup så att du kan övervaka säkerhetskopierings- och återställningsjobb, aviseringar om säkerhetskopiering och den molnlagring som används i dina Recovery Services-valv.|  
|   |   |

