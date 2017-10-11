---
title: Replikera virtuella Azure-datorer mellan Azure-regioner | Microsoft Docs
description: "Sammanfattas de steg som krävs för att replikera virtuella Azure-datorer mellan Azure-regioner med Azure Site Recovery-tjänsten i Azure-portalen"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: 6dd36239-4363-4538-bf80-a18e71b8ec67
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.openlocfilehash: 9258613161a61e36b1d0c5796d5763c916d66859
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="replicate-azure-vms-between-regions-with-azure-site-recovery"></a>Replikera virtuella Azure-datorer mellan regioner med Azure Site Recovery

>Den här artikeln innehåller en översikt över de steg som krävs för att replikera virtuella Azure-datorer (VM) i en Azure-region till Azure virtuella datorer i en annan region. 

>[!NOTE]
>
> Azure VM-replikering är för närvarande under förhandsgranskning.

Publicera kommentarer och frågor längst ned i den här artikeln eller på den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="step-1-review-architecture"></a>Steg 1: Granska arkitektur

Innan du börjar distributionen bör du granska scenariots arkitektur och komponenter som du behöver distribuera.

Gå till [steg 1: granska arkitekturen](azure-to-azure-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>Steg 2: Granska krav

Kontrollera att du har krav för Azure på platser, inklusive en prenumeration, virtuella nätverk, storage-konton och VM-krav.

Gå till [steg 2: Verifiera förutsättningar och begränsningar](azure-to-azure-walkthrough-prerequisites.md)


## <a name="step-3-plan-networking"></a>Steg 3: Planera nätverk

Kontrollera att utgående anslutning har ställts in på Azure virtuella datorer du vill replikera och att anslutningar från lokala har ställts in.

Gå till [steg 4: Planera nätverk](azure-to-azure-walkthrough-network.md)



## <a name="step-4-create-a-vault"></a>Steg 4: Skapa ett valv 

Du måste skapa ett Recovery Services-valv dirigera och hantera replikering och ange käll-region.

Gå till [steg 4: skapa ett valv](azure-to-azure-walkthrough-vault.md)


## <a name="step-5-enable-replication"></a>Steg 5: Aktivera replikering


Om du vill aktivera replikering konfigurerar Målinställningar för platsen, ställa in en replikeringsprincip och välj den virtuella Azure-datorer som du vill replikera. När du har aktiverat inträffar första replikeringen av den virtuella datorn.

Gå till [steg 5: Aktivera replikering](azure-to-azure-walkthrough-enable-replication.md)


## <a name="step-6-run-a-test-failover"></a>Steg 6: Köra ett redundanstest

När replikeringen har slutförts och deltareplikering körs, kan du köra ett redundanstest för att kontrollera att allt fungerar som förväntat.

Gå till [steg 6: köra ett redundanstest](azure-to-azure-walkthrough-test-failover.md)


