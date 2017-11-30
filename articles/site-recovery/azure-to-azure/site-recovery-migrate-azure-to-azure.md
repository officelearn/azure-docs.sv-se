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
ms.openlocfilehash: 805582d89ee906e21fbe588e895ce0fe3a926a66
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Migrera Azure IaaS-virtuella datorer mellan Azure-regioner med Azure Site Recovery

Använd den här artikeln om du vill migrera virtuella Azure-datorer (VM) mellan Azure-regioner, med hjälp av den [Site Recovery](../site-recovery-overview.md) service.

## <a name="prerequisites"></a>Krav

Du måste IaaS-VM som du vill migrera.

## <a name="deployment-steps"></a>Distributionssteg

1. [Skapa ett valv](azure-to-azure-tutorial-enable-replication.md#create-a-vault).
2. [Aktivera replikering](azure-to-azure-tutorial-enable-replication.md#enable-replication) för de virtuella datorerna som du vill migrera och välj Azure som källa. För närvarande stöds interna replikering av virtuella Azure-datorer med hjälp av hanterade diskar inte.
3. [Kör en redundansväxling](../site-recovery-failover.md). När den inledande replikeringen är klar kan köra du en växling vid fel från en Azure-region till en annan. Du kan också kan du skapa en återställningsplan och kör en redundansväxling, för att migrera flera virtuella datorer mellan regioner. [Lär dig mer](../site-recovery-create-recovery-plans.md) om återställningsplaner.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om andra replikeringarna i [vad är Azure Site Recovery?](../site-recovery-overview.md)
