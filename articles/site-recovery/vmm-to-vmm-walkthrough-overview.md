---
title: "Replikera virtuella Hyper-V-datorer till en sekundär plats för VMM med Azure Site Recovery | Microsoft Docs"
description: "Översikt för att replikera virtuella Hyper-V-datorer till en sekundär VMM-plats med hjälp av Azure portal."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 476ca82a-8f5c-4498-9dcf-e1011d60ed59
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: raynew
ms.openlocfilehash: b422dd2cf23426de2f154a553b38509082536309
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site"></a>Replikera virtuella Hyper-V-datorer i VMM-moln till en sekundär plats för VMM

> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-vmm-to-vmm.md)
> * [Klassisk portal](site-recovery-vmm-to-vmm-classic.md)
> * [PowerShell – Resource Manager](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
>
>

Den här artikeln innehåller en översikt över de steg som krävs för att replikera lokala Hyper-V virtuella datorer (VM) som hanteras i System Center Virtual Machine Manager (VMM) moln till en sekundär plats i VMM med [Azure Site Recovery](site-recovery-overview.md) i Azure-portalen.

När du har läst den här artikeln kan du lämna kommentarer längst ned på sidan eller på [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="step-1-review-the-scenario-architecture"></a>Steg 1: Granska scenariots arkitektur

Innan du börjar distributionen bör du granska scenariots arkitektur och se till att du förstår de komponenter som du behöver distribuera.

Gå till [steg 1: granska arkitekturen](vmm-to-vmm-walkthrough-architecture.md).

## <a name="step-2-review-prerequisites-and-limitations"></a>Steg 2: Granska förutsättningar och begränsningar

Kontrollera att du förstår distributionskrav och begränsningar.

**Krav för Azure**: du behöver ett Microsoft Azure-prenumeration och Azure Recovery Services-valvet, dirigera och hantera replikering.
**Lokal VMM-servrar och Hyper-V-värdar**: Kontrollera att VMM-servrar och Hyper-V-värdar är kompatibla och förberedda för distributionen av Site Recovery.

Gå till [steg 2: Verifiera förutsättningar och begränsningar](vmm-to-vmm-walkthrough-prerequisites.md).

## <a name="step-3-plan-networking"></a>Steg 3: Planera nätverk

Du behöver göra vissa nätverk som planerar att se till att du kan konfigurera nätverksmappning mellan VMM VM-nätverk när du distribuerar scenariot.

Gå till [steg3: Planera nätverk](vmm-to-vmm-walkthrough-network.md).


## <a name="step-4-prepare-vmm-and-hyper-v"></a>Steg 4: Förbered VMM och Hyper-V

Förbereda VMM-servrar och Hyper-V-värdar för distributionen av Site Recovery.

Gå till [steg 4: förbereda lokala servrar](vmm-to-vmm-walkthrough-vmm-hyper-v.md).

## <a name="step-5-set-up-a-vault"></a>Steg 5: Konfigurera ett valv

Skapa ett Recovery Services-valvet. Valvet innehåller konfigurationsinställningar och styr replikeringen.

[Steg 5: Konfigurera ett valv](vmm-to-vmm-walkthrough-create-vault.md).

## <a name="step-6-set-up-source-and-target-settings"></a>Steg 6: Konfigurera inställningar för källa och mål

Ställ in på käll- och replikering VMM platser. Lägg till VMM-servrarna i valvet och hämta installationsfilerna för Site Recovery-komponenter. Kör installationsprogrammet för Azure Site Recovery-providern på VMM-servern. Installationsprogrammet installerar providern på VMM-servern och registrerar servern i valvet. Du installerar Microsoft Recovery Services-agenten på varje Hyper-V-värd.

Gå till [steg 6: Konfigurera inställningar för käll- och](vmm-to-vmm-walkthrough-source-target.md).

## <a name="step-7-configure-network-mapping"></a>Steg 7: Konfigurera nätverksmappning

Mappa VMM VM-nätverk i käll- och målplatserna. Efter växling vid fel skapas virtuella datorer i målnätverket som mappar till VM-källnätverket där källan Hyper-V-dator finns.

Gå till [steg 7: Konfigurera nätverksmappning](vmm-to-vmm-walkthrough-network-mapping.md).


## <a name="step-8-set-up-a-replication-policy"></a>Steg 8: Skapa en replikeringsprincip

Ange hur virtuella datorer kommer att replikeras mellan VMM-platser.

Gå till [steg 8: skapa en replikeringsprincip](vmm-to-vmm-walkthrough-replication.md).


## <a name="step-9-enable-replication-for-vms"></a>Steg 9: Aktivera replikering för virtuella datorer

Välj de virtuella datorerna som du vill replikera. Aktivera en virtuell dator för replikering utlöser den inledande replikeringen på den sekundära platsen, följt av en pågående deltareplikering.

Gå till [steg 9: Aktivera replikering](vmm-to-vmm-walkthrough-enable-replication.md).


## <a name="step-10-run-a-test-failover"></a>Steg 10: Köra ett redundanstest

Kör ett redundanstest för att kontrollera att allt fungerar som förväntat.

Gå till [steg 10: köra ett redundanstest](vmm-to-vmm-walkthrough-test-failover.md).
