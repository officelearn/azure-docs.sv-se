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
ms.date: 08/31/2017
ms.author: raynew
ms.openlocfilehash: 02f68b68491250f89e8b0e3057f2363b177ab32e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Migrera Azure IaaS-virtuella datorer mellan Azure-regioner med Azure Site Recovery
## <a name="overview"></a>Översikt
Välkommen till Azure Site Recovery! Använd den här artikeln om du vill migrera virtuella datorer i Azure mellan Azure-regioner.
>[!NOTE]
>
> För att replikera virtuella Azure-datorer till en annan region för disaster recovery och migreringen måste referera till [dokumentet](site-recovery-azure-to-azure.md). Site Recovery replikering för virtuella Azure-datorer är för närvarande under förhandsgranskning.

Observera att innan du börjar:

* Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: Azure Resource Manager och klassisk. Azure-portalen stöder båda distributionsmodellerna. De grundläggande stegen för migrering är samma oavsett om du konfigurerar Site Recovery i Resource Manager eller i klassiskt. 

Skriv dina kommentarer eller frågor längst ned i den här artikeln eller i [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Krav
Här är vad du behöver för den här distributionen:

* **IaaS-virtuella datorer**: på virtuella datorer som du vill migrera. Du kan migrera dessa virtuella datorer genom att behandla dem som fysiska datorer.

## <a name="deployment-steps"></a>Distributionssteg
Det här avsnittet beskrivs stegen för distributionen i Azure-portalen.

1. [Skapa ett valv](site-recovery-azure-to-azure.md#create-a-recovery-services-vault).
2. [Aktivera replikering](site-recovery-azure-to-azure.md) för de virtuella datorerna som du vill migrera och välj Azure som källa. Ursprunglig replikering av virtuella Azure-datorer med hjälp av hanterade diskar stöds för närvarande inte. Du kan använda alternativet ”fysiska till Azure” i [dokumentet](site-recovery-vmware-to-azure.md) att migrera virtuella datorer med hanterade diskar.
1. [Kör en redundansväxling](site-recovery-failover.md). När den inledande replikeringen är klar kan köra du en växling vid fel från en Azure-region till en annan. Du kan också kan du skapa en återställningsplan och kör en redundansväxling, för att migrera flera virtuella datorer mellan regioner. [Lär dig mer](site-recovery-create-recovery-plans.md) om återställningsplaner.

## <a name="next-steps"></a>Nästa steg
Mer information om andra replikeringarna i [vad är Azure Site Recovery?](site-recovery-overview.md)
