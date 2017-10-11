---
title: Replikera virtuella Hyper-V-datorer i VMM-moln till Azure med Azure Site Recovery | Microsoft Docs
description: "Ger en översikt för att replikera virtuella Hyper-V-datorer i VMM-moln till Azure med Azure Site Recovery-tjänsten"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.openlocfilehash: af68d21184c137b2b0f1bb4c1afb9bf507e8332d
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-site-recovery-in-the-azure-portal"></a>Replikera virtuella Hyper-V-datorer i VMM-moln till Azure med hjälp av Site Recovery i Azure Portal
> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-vmm-to-azure.md)
> * [Klassiska Azure](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell – Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [PowerShell – Klassisk](site-recovery-deploy-with-powershell.md)


Den här artikeln innehåller en översikt över de steg som krävs för att replikera lokala Hyper-V virtuella datorer (VM) som hanteras i System Center Virtual Machine Manager (VMM)-moln till Azure, med hjälp av den [Azure Site Recovery](site-recovery-overview.md) tjänsten på den Azure-portalen.

När du har läst den här artikeln kan du lämna kommentarer längst ned på sidan eller på [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="step-1-review-the-scenario-architecture"></a>Steg 1: Granska scenariots arkitektur

Innan du börjar distributionen bör du granska scenariots arkitektur och se till att du förstår de komponenter som du behöver distribuera.

Gå till [steg 1: granska arkitekturen](vmm-to-azure-walkthrough-architecture.md)

## <a name="step-2-review-prerequisites-and-limitations"></a>Steg 2: Granska förutsättningar och begränsningar

Kontrollera att du förstår distributionskrav och begränsningar.

**Krav för Azure**: du behöver ett Microsoft Azure-konto, Azure-nätverk och lagringskonton.
**Lokal VMM-servrar och Hyper-V-värdar**: Kontrollera att VMM-servrar och Hyper-V-värdar är kompatibla och förberedda för distributionen av Site Recovery.
**Replikerade virtuella datorer**: Kontrollera att virtuella datorer som du vill replikera uppfyller kraven för Azure.

Gå till [steg 2: Verifiera förutsättningar och begränsningar](vmm-to-azure-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>Steg 3: Planera kapacitet

Om du gör en fullständig distribution, måste du ta reda på vilka replikering resurser som du behöver. Det finns några tillgängliga för att hjälpa dig med detta verktyg. Om du utför en snabb som ställts in för att testa miljön kan du hoppa över det här steget.

Gå till [Steg3: Planera kapacitet](vmm-to-azure-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>Steg 4: Planera nätverk

Du behöver göra några nätverk som planerar att se till att du kan konfigurera nätverksmappning när du distribuerar Scenarionamn som virtuella Azure-datorer ansluts till virtuella Azure-nätverk efter växling vid fel och som att de är tilldelade lämplig IP-adresser.

Gå till [steg 4: Planera nätverk](vmm-to-azure-walkthrough-network.md)


## <a name="step-5-prepare-azure-resources"></a>Steg 5: Förbered Azure-resurser

Skapa ett Azure-konto, nätverk och lagring. Du kan göra detta under distributionen, men vi rekommenderar att du gör detta innan du börjar.

Gå till [steg 5: Förbered Azure](vmm-to-azure-walkthrough-prepare-azure.md)

## <a name="step-6-prepare-vmm-and-hyper-v"></a>Steg 6: Förbereda VMM och Hyper-V

Förbered den lokala VMM-servrar och Hyper-V-värdar för distributionen av Site Recovery.

Gå till [steg 6: förbereda lokala servrar](vmm-to-azure-walkthrough-vmm-hyper-v.md)

## <a name="step-7-set-up-a-vault"></a>Steg 7: Konfigurera ett valv

Skapa ett Recovery Services-valvet. Valvet innehåller konfigurationsinställningar och styr replikeringen.

[Steg 7: Konfigurera ett valv](vmm-to-azure-walkthrough-create-vault.md)

## <a name="step-8-configure-source-and-target-settings"></a>Steg 8: Konfigurera inställningar för källa och mål

Konfigurera käll- och målplatserna för replikering. Lägg till VMM-servern i valvet och hämta installationsfilerna för Site Recovery-komponenter. Kör installationsprogrammet för Azure Site Recovery-providern på VMM-servern. Installationsprogrammet installerar providern på VMM-servern och registrerar servern i valvet. Du installerar Microsoft Recovery Services-agenten på varje Hyper-V-värd.

Gå till [steg 8: Konfigurera inställningar för källa och mål](vmm-to-azure-walkthrough-source-target.md)

## <a name="step-9-configure-network-mapping"></a>Steg 9: Konfigurera nätverksmappning

Mappa en lokal VMM VM-nätverk för virtuella Azure-nätverk. Efter växling vid fel skapas virtuella Azure-datorer i Azure-nätverk som mappar till det lokala nätverket som datakälla Hyper-V finns.

Gå till [steg 9: Konfigurera nätverksmappning](vmm-to-azure-walkthrough-network-mapping.md)


## <a name="step-10-set-up-a-replication-policy"></a>Steg 10: Ställ in en replikeringsprincip

Ange hur lokala virtuella datorer kommer att replikeras till Azure.

Gå till [steg 10: Konfigurera en replikeringsprincip](vmm-to-azure-walkthrough-replication.md)


## <a name="step-11-enable-replication-for-vms"></a>Steg 11: Aktivera replikering för virtuella datorer

Välj de virtuella datorerna som du vill replikera. Aktivera en virtuell dator för replikering utlöser den inledande replikeringen till Azure, följt av en pågående deltareplikering.

Gå till [steg 11: Aktivera replikering](vmm-to-azure-walkthrough-enable-replication.md)


## <a name="step-12-run-a-test-failover"></a>Steg 12: Kör ett redundanstest

Kör ett redundanstest för att kontrollera att allt fungerar som förväntat.

Gå till [steg 12: kör ett redundanstest](vmm-to-azure-walkthrough-test-failover.md)


