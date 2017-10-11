---
title: "Förbered Azure resurser för att replikera virtuella Hyper-V-datorer (med System Center VMM) till Azure med Azure Site Recovery | Microsoft Docs"
description: "Beskriver vad du behöver på plats i Azure innan du börjar replikera virtuella Hyper-V-datorer (med VMM) till Azure med Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 1568bdc3-e767-477b-b040-f13699ab5644
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: 63b005f37ab5e15e8a1b4645446d65f1529f1bbd
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="step-5-prepare-azure-resources-for-hyper-v-replication-with-vmm-to-azure"></a>Steg 5: Förbered Azure-resurser för Hyper-V-replikering (med VMM) till Azure

När du har verifierat [krav på](vmm-to-azure-walkthrough-network.md), följ instruktionerna i den här artikeln för att förbereda Azure-resurser så att du kan replikera lokala Hyper-V virtuella datorer i System Center Virtual Machine Manager (VMM)-moln till Azure, med [ Azure Site Recovery](site-recovery-overview.md) service.

När du har läst den här artikeln efter eventuella kommentarer längst ned eller tekniska frågor om den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-an-azure-account"></a>Konfigurera ett Azure-konto

- Hämta en [Microsoft Azure-konto](http://azure.microsoft.com/).
- Du kan börja med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
- Kontrollera regionerna som stöds för Site Recovery Under geografisk tillgänglighet i [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
- Lär dig mer om [priserna för Site Recovery](site-recovery-faq.md#pricing), och få de [prisinformationen](https://azure.microsoft.com/pricing/details/site-recovery/).
- Kontrollera att kontot har rätt [behörigheter](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)att skapa virtuella Azure-datorer. [Lär dig mer](../active-directory/role-based-access-built-in-roles.md) om rollbaserad åtkomstkontroll i Azure.


## <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk

- Konfigurera en [Azure-nätverk](../virtual-network/virtual-network-get-started-vnet-subnet.md). Virtuella Azure-datorer placeras i det här nätverket när de skapas efter växling vid fel.
- Nätverket måste finnas i samma region som Recovery Services-valvet
- Site Recovery på Azure-portalen kan använda nätverk i [Resource Manager](../resource-manager-deployment-model.md), eller i klassiskt läge.
- Vi rekommenderar att du konfigurerar ett nätverk innan du börjar. Om du inte gör det måste du göra det under distributionen av Site Recovery.
- Lär dig mer om [priser för virtuella nätverk](https://azure.microsoft.com/pricing/details/virtual-network/).


## <a name="set-up-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto

- Site Recovery replikerar lokala datorer till Azure-lagring. Virtuella Azure-datorer skapas från lagringen efter redundansväxlingen.
- Konfigurera standard/premium [Azure storage-konto](../storage/common/storage-create-storage-account.md#create-a-storage-account) att lagra data som replikeras till Azure.
- [Premium-lagring](../storage/common/storage-premium-storage.md) används vanligtvis för virtuella datorer som behöver en konsekvent höga i/o-prestanda och låg fördröjning till värd-i/o-intensiv arbetsbelastning.
- Om du vill använda ett Premium Storage-konto för replikerade data konfigurerar du ytterligare ett standardlagringskonto för att lagra replikeringsloggar som samlar in löpande ändringar i lokala data.
- Beroende på vilken resursmodell som du vill använda för redundansväxlade virtuella Azure-datorer kan du konfigurera ett konto i [Resource Manager-läget](../storage/common/storage-create-storage-account.md), eller [klassiskt läge](../storage/common/storage-create-storage-account.md).
- Vi rekommenderar att du ställer in ett storage-konto innan du börjar. Om du inte behöver göra det under distributionen av Site Recovery. Konton måste vara i samma region som Recovery Services-valvet.
- Du kan inte flytta storage-konton som används av Site Recovery i resursgrupper inom samma prenumeration, eller olika prenumerationer.


## <a name="next-steps"></a>Nästa steg

Gå till [steg 6: förbereda VMM](vmm-to-azure-walkthrough-vmm-hyper-v.md)
