---
title: "Ställ in ett valv för fysisk serverreplikering till Azure med Azure Site Recovery | Microsoft Docs"
description: "Sammanfattas de steg som du behöver konfigurera ett valv för att replikera fysiska servrar till Azure med Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d99f2605-f417-4995-be77-5323136b814f
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: deb5ad0495edc969b374795eeb2698326dd4ff4d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="step-6-set-up-a-vault-for-physical-server-replication-to-azure"></a>Steg 6: Konfigurera ett valv för fysisk serverreplikering till Azure


Den här artikeln beskriver hur du ställer in ett valv. Du skapar valvet och anger du vill replikera från den lokala platsen till Azure med hjälp av den [Azure Site Recovery](site-recovery-overview.md) tjänsten i Azure-portalen.


Publicera kommentarer och frågor längst ned i den här artikeln eller i den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).




## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Välj en skyddsmål

Välj vad och vart du vill replikera.

1. Klicka på **Recovery Services-valv** > valvet.
2. Resurs-menyn klickar du på **Site Recovery** > **Förbered infrastrukturen** > **skyddsmål**.
3. I **skyddsmål**väljer **till Azure** > **inte virtualiserade/andra**.


## <a name="next-steps"></a>Nästa steg

Gå till [steg 7: Konfigurera källa och mål](physical-walkthrough-source-target.md)
