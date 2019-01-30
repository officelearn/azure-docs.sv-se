---
title: Skillnader och överväganden för Managed Disks i Azure Stack | Microsoft Docs
description: Läs mer om skillnader och överväganden när du arbetar med Managed Disks i Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 05efa7eaf6d95cbf63efd17b00d321d8c8509f28
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246787"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack Managed Disks: skillnader och överväganden

Den här artikeln sammanfattas de kända skillnaderna mellan [Azure Stack Managed Disks](azure-stack-manage-vm-disks.md) och [Managed Disks för Azure](../../virtual-machines/windows/managed-disks-overview.md). Läs mer om övergripande skillnader mellan Azure Stack och Azure, i den [viktiga överväganden](azure-stack-considerations.md) artikeln.

Hanterade diskar förenklar Diskhantering för virtuella IaaS-datorer genom att hantera den [lagringskonton](../azure-stack-manage-storage-accounts.md) som är associerade med de Virtuella diskarna.

> [!Note]  
> Hanterade diskar på Azure Stack är tillgänglig från 1808 update. Det är aktiverat som standard när du skapar virtuella datorer med Azure Stack-portalen från 1811 update.
  

## <a name="cheat-sheet-managed-disk-differences"></a>Lathund: Managed disk skillnader

| Funktion | Azure (global) | Azure Stack |
| --- | --- | --- |
|Kryptering för vilande Data |Azure Storage Service Encryption (SSE), Azure Disk Encryption (ADE)     |BitLocker 128-bitars AES-kryptering      |
|Bild          | Stöd för anpassade avbildningar |Stöds inte än|
|Alternativ för säkerhetskopiering |Stöd för Azure Backup-tjänsten |Stöds inte än |
|Alternativen för katastrofåterställning |Stöd för Azure Site Recovery |Stöds inte än|
|Disktyper     |Premium SSD, Standard SSD (förhandsversion) och Standard-Hårddisk |Premium SSD, Standard HDD |
|Premium-diskar  |Stöds fullt ut |Kan tillhandahållas, men ingen prestandagräns eller garanterar  |
|Premium disks IOPs  |Beror på diskstorleken  |2300 IOPs per disk |
|Dataflöde för Premium-diskar |Beror på diskstorleken |145 MB/sekund per disk |
|Diskstorlek  |Azure Premium Disk: P4 (32 GiB) till P80 (32 TiB)<br>Azure Standard SSD-Disk: E10 (128 GiB) till E80 (32 TiB)<br>Azure Standard HDD Disk: S4 (32 GiB) till S80 (32 TiB) |M4: 32 GiB<br>M6: 64 GiB<br>M10: 128 GiB<br>M15: 256 GB<br>M20: 512 GiB<br>M30: 1024 giB |
|Diskar kopiering av ögonblicksbild|Ta en ögonblicksbild Azure hanterade diskar är anslutna till en aktiv virtuell dator som stöds|Stöds inte än |
|Diskar prestanda analytiska |Aggregera mått och per disk-mått som stöds |Stöds inte än |
|Migrering      |Innehåller verktyg för att migrera från befintliga ohanterade virtuella Azure Resource Manager-datorer utan att behöva återskapa den virtuella datorn  |Stöds inte än |

> [!NOTE]  
> Hanterade diskar IOPs och dataflöde i Azure Stack är en cap-nummer i stället för ett etablerade tal, som kan påverkas av maskin- och arbetsbelastningar som körs i Azure Stack.

## <a name="metrics"></a>Mått

Det finns också skillnader med mätvärden i storage:

- Med Azure Stack, görs transaktionsdata i lagringsmått ingen åtskillnad interna eller externa nätverksbandbredd.
- Azure Stack-transaktionsdata i mätvärden i storage inkluderar inte VM-åtkomst till de monterade diskarna.

## <a name="api-versions"></a>API-versioner

Managed Disks för Azure Stack stöd för följande API-versioner:

- 2017-03-30

## <a name="configuration"></a>Konfiguration

Efter att ha tillämpat 1808 uppdatera eller senare, måste du utföra följande konfigurationssteg innan du använder Managed Disks:

- Om en prenumeration skapades före uppdateringen 1808, följer du stegen nedan för att uppdatera prenumerationen. Annars kan misslyckas distribuera virtuella datorer i den här prenumerationen med felmeddelandet ”internt fel i Diskhanteraren”.
   1. I klient-portalen går du till **prenumerationer** och hitta prenumerationen. Klicka på **Resursprovidrar**, klicka sedan på **Microsoft.Compute**, och klicka sedan på **Omregistrera**.
   2. Under samma prenumeration, gå till **åtkomstkontroll (IAM)**, och kontrollera att **Azure Stack – hanterad Disk** visas.
- Om du använder en miljö med flera organisationer, be din molnoperator (kan vara i din organisation eller från Service Provider) att konfigurera om var och en av dina gäst-kataloger följa dessa steg på [i den här artikeln](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory). Annars kan misslyckas distribuera virtuella datorer i en prenumeration som är associerade med den gästkatalogen med felmeddelandet ”internt fel i Diskhanteraren”.


## <a name="next-steps"></a>Nästa steg

- [Lär dig mer om Azure Stack-datorer](azure-stack-compute-overview.md)
