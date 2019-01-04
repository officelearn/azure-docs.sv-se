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
ms.date: 09/05/2018
ms.author: sethm
ms.reviewer: jiahan
ms.openlocfilehash: 4bad339ca704f14f57c120db5e731d0dbfeb8d59
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793452"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack hanterade diskar: Skillnader och överväganden
Den här artikeln sammanfattas de kända skillnaderna mellan Azure Stack Managed Disks och Managed Disks för Azure. Läs mer om övergripande skillnader mellan Azure Stack och Azure, i den [viktiga överväganden](azure-stack-considerations.md) artikeln.

Hanterade diskar förenklar Diskhantering för virtuella IaaS-datorer genom att hantera den [lagringskonton](/azure/azure-stack/azure-stack-manage-storage-accounts) som är associerade med de Virtuella diskarna.

> [!Note]  
> Hanterade diskar på Azure Stack är tillgänglig från 1808.
  

## <a name="cheat-sheet-managed-disk-differences"></a>Lathund: Managed disk skillnader

| Funktion | Azure (global) | Azure Stack |
| --- | --- | --- |
|Kryptering för vilande Data |Azure Storage Service Encryption (SSE), Azure Disk Encryption (ADE)     |BitLocker 128-bitars AES-kryptering      |
|Bild          | Stöd för anpassade avbildningar |Stöds inte än|
|Alternativ för säkerhetskopiering |Stöd för Azure Backup-tjänsten |Stöds inte än |
|Alternativen för katastrofåterställning |Stöd för Azure Site Recovery |Stöds inte än|
|Disktyper     |Premium SSD, Standard SSD (förhandsversion) och Standard-Hårddisk |Premium SSD, Standard-Hårddisk |
|Premium-diskar  |Stöds fullt ut |Kan tillhandahållas, men ingen prestandagräns eller garanterar  |
|Premium-diskar IOPs  |Beror på diskstorleken  |2300 IOPs per disk |
|Dataflöde för Premium-diskar |Beror på diskstorleken |145 MB/sekund per disk |
|Diskstorlek  |Azure Premium-Disk: P4 (32 GiB) till P80 (32 TiB)<br>Azure Standard SSD-Disk: E10 (128 GiB) till E80 (32 TiB)<br>Azure Standard HDD Disk: S4 (32 GiB) till S80 (32 TiB) |M4: 32 GiB<br>M6: 64 GiB<br>M10: 128 GiB<br>M15: 256 GB<br>M20: 512 GiB<br>M30: 1024 giB |
|Diskar kopiering av ögonblicksbild|Ta en ögonblicksbild Azure hanterade diskar är anslutna till en aktiv virtuell dator som stöds|Stöds inte än |
|Diskar prestanda analytiska |Aggregera mått och per disk-mått som stöds |Stöds inte än |
|Migrering      |Innehåller verktyg för att migrera från befintliga ohanterade virtuella Azure Resource Manager-datorer utan att behöva återskapa den virtuella datorn  |Stöds inte än |

> [!Note]  
> Hanterade diskar IOPs och dataflöde i Azure Stack är en cap-nummer i stället för ett etablerade tal, som kan påverkas av maskin- och arbetsbelastningar som körs i Azure Stack.


## <a name="metrics"></a>Mått
Det finns också skillnader med mätvärden i storage:
- Med Azure Stack skilja inte transaktionsdata i lagringsmått interna eller externa nätverksbandbredd.
- Azure Stack-transaktionsdata i lagringsmått omfattar inte VM-åtkomst till de monterade diskarna.


## <a name="api-versions"></a>API-versioner
Managed Disks för Azure Stack stöd för följande API-versioner:
- 2017-03-30

## <a name="known-issues"></a>Kända problem
Efter att ha tillämpat 1809 uppdatera, följande problem kan uppstå när du distribuerar virtuella datorer med hanterade diskar:

   - Om prenumerationen har skapats innan uppdateringen gjordes 1808, distribution av virtuella datorer med Managed Disks kan misslyckas med felmeddelandet internt. Följ dessa steg för varje prenumeration för att lösa problemet:
      1. I klient-portalen går du till **prenumerationer** och hitta prenumerationen. Klicka på **Resursprovidrar**, klicka sedan på **Microsoft.Compute**, och klicka sedan på **Omregistrera**.
      2. Under samma prenumeration, gå till **åtkomstkontroll (IAM)**, och kontrollera att **Azure Stack – hanterad Disk** visas.
   - Om du har konfigurerat en miljö med flera organisationer kan kan distribuera virtuella datorer i en prenumeration som är associerade med en gäst-katalog misslyckas med ett internt felmeddelande. Lös felet genom att följa stegen i [i den här artikeln](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) att konfigurera om var och en av dina gäst-kataloger.


## <a name="next-steps"></a>Nästa steg
[Lär dig mer om Azure Stack-datorer](azure-stack-compute-overview.md)
