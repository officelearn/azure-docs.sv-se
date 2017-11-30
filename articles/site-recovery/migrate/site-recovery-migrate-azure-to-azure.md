---
title: Migrera Azure IaaS-VM mellan Azure-regioner | Microsoft Docs
description: "Använd Azure Site Recovery för att migrera Azure IaaS-virtuella datorer från en Azure-region till en annan."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8a29e0d9-0010-4739-972f-02b8bdf360f6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: raynew
ms.openlocfilehash: f645150aa7b93ebbd98899cc8fdd495dfeaf3076
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Migrera Azure IaaS-virtuella datorer mellan Azure-regioner med Azure Site Recovery

Välkommen till Azure Site Recovery! Använd den här artikeln om du vill migrera virtuella datorer i Azure mellan Azure-regioner.
>[!NOTE]
>
> För att replikera virtuella Azure-datorer till en annan region för disaster recovery och migreringen måste referera till [dokumentet](../site-recovery-azure-to-azure.md). Site Recovery replikering för virtuella Azure-datorer är för närvarande under förhandsgranskning.


## <a name="prerequisites"></a>Krav
Här är vad du behöver för den här distributionen:

* **IaaS-virtuella datorer**: på virtuella datorer som du vill migrera. Du kan migrera dessa virtuella datorer genom att behandla dem som fysiska datorer.

## <a name="deployment-steps"></a>Distributionssteg
Det här avsnittet beskrivs stegen för distributionen i den nya Azure-portalen.

1. [Skapa ett valv](../site-recovery-azure-to-azure.md#create-a-recovery-services-vault).
2. [Aktivera replikering...] /(Site-Recovery-Azure-to-Azure.MD) för de virtuella datorerna som du vill migrera och välj Azure som källa.
  >[!NOTE]
  >
  > Ursprunglig replikering av virtuella Azure-datorer med hjälp av hanterade diskar stöds för närvarande inte. Du kan använda alternativet ”fysiska till Azure” i [dokumentet](../site-recovery-vmware-to-azure.md) att migrera virtuella datorer med hanterade diskar.
3. [Kör en redundansväxling](../site-recovery-failover.md). När den inledande replikeringen är klar kan köra du en växling vid fel från en Azure-region till en annan. Du kan också kan du skapa en återställningsplan och kör en redundansväxling, för att migrera flera virtuella datorer mellan regioner. [Lär dig mer](../site-recovery-create-recovery-plans.md) om återställningsplaner.

## <a name="next-steps"></a>Nästa steg
Mer information om andra replikeringarna i [vad är Azure Site Recovery?](../site-recovery-overview.md)
