---
title: Replikera fysiska lokala servrar till Azure med Azure Site Recovery | Microsoft Docs
description: "Innehåller en översikt över stegen för att replikera arbetsbelastningar som körs på lokala Windows-/ Linux fysiska servrar till Azure med Azure Site Recovery-tjänsten."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 20122f01-929a-4675-b85b-a9b99d2618bc
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 0a09b35e98dc0b2f5283c2a707a3a2b8ac9a39f2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-physical-servers-to-azure-with-site-recovery"></a>Replikera fysiska servrar till Azure med Site Recovery

Den här artikeln innehåller en översikt över de steg som krävs för att replikera lokala Windows-/ Linux fysiska servrar till Azure med hjälp av den [Azure Site Recovery](site-recovery-overview.md) tjänsten i Azure-portalen.


## <a name="step-1-review-architecture-and-prerequisites"></a>Steg 1: Granska arkitektur och förutsättningar

Innan du börjar distributionen bör granska scenariots arkitektur och kontrollera att du förstår de komponenter som du behöver konfigurera distributionen.

Gå till [steg 1: granska arkitekturen](physical-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>Steg 2: Granska krav

Kontrollera att du har de nödvändiga förutsättningarna för varje komponent i distributionen:

- **Krav för Azure**: du behöver ett Microsoft Azure-konto, Azure-nätverk och lagringskonton.
- **Site Recovery-komponenter på lokala**: du behöver en dator som körs lokalt Site Recovery-komponenter.
- **Replikerade datorer**: servrar som du vill replikera måste uppfylla kraven för Azure och lokalt.

Gå till [steg 2: granska förutsättningar och begränsningar](physical-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>Steg 3: Planera kapacitet

Om du gör en fullständig distribution måste du ta reda på vilka replikering resurser som du behöver. Om du utför en snabb som ställts in för att testa miljön kan du hoppa över det här steget.

Gå till [Steg3: Planera kapacitet](physical-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>Steg 4: Planera nätverk

Du behöver göra några nätverk som planerar att se till att virtuella Azure-datorer är anslutna till nätverk efter växling vid fel och att de har rätt IP-adresser.

Gå till [steg 4: Planera nätverk](physical-walkthrough-network.md)

##  <a name="step-5-prepare-azure-resources"></a>Steg 5: Förbered Azure-resurser

Konfigurera Azure nätverken och lagringsenheterna innan du börjar. 

Gå till [steg 5: Förbered Azure](physical-walkthrough-prepare-azure.md)


## <a name="step-6-set-up-a-vault"></a>Steg 6: Konfigurera ett valv

Du kan skapa ett Recovery Services-valv dirigera och hantera replikering. När du ställer in valvet ange vad du vill replikera och var du vill replikera den till.

Gå till [steg 6: konfigurera ett valv](physical-walkthrough-create-vault.md)

## <a name="step-7-configure-source-and-target-settings"></a>Steg 7: Konfigurera inställningar för källa och mål

Konfigurera inställningar för källa och mål (Azure) plats. Inställningar för datakälla innehåller Unified hur du installerar lokalt Site Recovery-komponenter.

Gå till [steg 7: Konfigurera källa och mål](physical-walkthrough-source-target.md)

## <a name="step-8-set-up-a-replication-policy"></a>Steg 8: Skapa en replikeringsprincip

Du konfigurerar en princip för att ange hur fysiska servrar som ska replikeras.

Gå till [steg 8: skapa en replikeringsprincip](physical-walkthrough-replication.md)

## <a name="step-9-install-the-mobility-service"></a>Steg 9: Installera mobilitetstjänsten

Mobilitetstjänsten måste installeras på varje server som du vill replikera. Det finns några olika sätt att konfigurera tjänsten med push- eller pull-installation.

Gå till [steg 9: Installera mobilitetstjänsten](physical-walkthrough-install-mobility.md)

## <a name="step-10-enable-replication"></a>Steg 10: Aktivera replikering

När mobilitetstjänsten körs på en server, kan du aktivera replikering för den. När du har aktiverat inträffar första replikeringen av den virtuella datorn.

Gå till [steg 10: Aktivera replikering](physical-walkthrough-enable-replication.md)

## <a name="step-11-run-a-test-failover"></a>Steg 11: Kör ett redundanstest

När replikeringen har slutförts och deltareplikering körs, kan du köra ett redundanstest för att kontrollera att allt fungerar som förväntat.

Gå till [steg 11: kör ett redundanstest](physical-walkthrough-test-failover.md)

