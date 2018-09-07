---
title: Skillnader och överväganden för Managed Disks i Azure Stack | Microsoft Docs
description: Läs mer om skillnader och överväganden när du arbetar med Managed Disks i Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: 4fd2a26d9119e52fc75918abc1ca97e6f9888169
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028601"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack Managed Disks: Skillnader och överväganden
Den här artikeln sammanfattas de kända skillnaderna mellan Azure Stack Managed Disks och Managed Disks för Azure. Läs mer om övergripande skillnader mellan Azure Stack och Azure, i den [viktiga överväganden](azure-stack-considerations.md) artikeln.

Hanterade diskar förenklar Diskhantering för virtuella IaaS-datorer genom att hantera den [lagringskonton](/azure/azure-stack/azure-stack-manage-storage-accounts) som är associerade med de Virtuella diskarna.
  

## <a name="cheat-sheet-virtual-machine-differences"></a>Lathund: VM-skillnader

| Funktion | Azure (global) | Azure Stack |
| --- | --- | --- |
|Kryptering för vilande Data |Azure Storage Service Encryption (SSE), Azure Disk Encryption (ADE)     |BitLocker 128-bitars AES-kryptering      |
|Bild          | Stöd för anpassade avbildningar |Stöds inte än|
|Alternativ för säkerhetskopiering |Stöd för Azure Backup-tjänsten |Stöds inte än |
|Alternativen för katastrofåterställning |Stöd för Azure Site Recovery |Stöds inte än|
|Disktyper     |Premium SSD, Standard SSD (förhandsversion) och Standard-Hårddisk |Premium SSD, Standard-Hårddisk |
|Premium-diskar  |Stöds fullt ut |Kan tillhandahållas, men ingen prestandagräns eller garanterar  |
|Premium-diskar  |IOPs  |Beroende av disk-storlek 2300 IOPs per disk |
|Dataflöde för Premium-diskar |Beror på diskstorleken |145 MB/sekund per disk |
|Maxstorlek för disk  |4 TB       |1 TB       |
|Diskar prestanda analytiska |Aggregera mått och per disk-mått som stöds |Stöds inte än |
|Migrering      |Innehåller verktyg för att migrera från befintliga ohanterade virtuella Azure Resource Manager-datorer utan att behöva återskapa den virtuella datorn  |Stöds inte än |


## <a name="metrics"></a>Mått
Det finns också skillnader med mätvärden i storage:
- Med Azure Stack skilja inte transaktionsdata i lagringsmått interna eller externa nätverksbandbredd.
- Azure Stack-transaktionsdata i lagringsmått omfattar inte VM-åtkomst till de monterade diskarna.


## <a name="api-versions"></a>API-versioner
Managed Disks för Azure Stack stöd för följande API-versioner:
- 2017-03-30


## <a name="next-steps"></a>Nästa steg
[Lär dig mer om Azure Stack-datorer](azure-stack-compute-overview.md)
