---
title: "Installera mobilitetstjänsten för VMware på Azure replikering | Microsoft Docs"
description: "Den här artikeln beskriver hur du installerar mobilitetstjänstagenten för VMware på Azure replikering med Azure Site Recovery-tjänsten."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 3189fbcd-6b5b-4ffb-b5a9-e2080c37f9d9
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: bc520bd2ea54208889861a7a3b275e3008a05d53
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="step-10-install-the-mobility-service"></a>Steg 10: Installera mobilitetstjänsten


Den här artikeln beskriver hur du konfigurerar inställningar för källa och mål när replikera lokala virtuella VMware-datorer till Azure, med hjälp av den [Azure Site Recovery](site-recovery-overview.md) tjänsten i Azure-portalen.

Mobilitetstjänsten samlar in dataskrivningar på en dator och vidarebefordrar dem till processervern. Den bör installeras på varje dator som du vill replikera till Azure.

Du kan installera Mobility service manuell, med hjälp av en push-installation från Site Recovery processervern när replikering har aktiverats eller verktyget System Center Configuration Manager. Om du använder push-installation av är tjänsten installerad på den virtuella datorn när replikeringen är aktiverad.

Publicera kommentarer och frågor längst ned i den här artikeln eller i den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="install-manually"></a>Installera manuellt

1. Kontrollera den [krav](site-recovery-vmware-to-azure-install-mob-svc.md#prerequisites) för manuell installation.
2. Följ [instruktionerna](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui) för manuell installation med hjälp av portalen.
3. Om du vill installera från kommandoraden följer [instruktionerna](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt).

## <a name="install-from-the-process-server"></a>Installera från processervern

Om du vill skicka Mobility Tjänstinstallationen från processervern när du aktiverar replikering för en virtuell dator behöver du ett konto som kan användas av processervern för att få åtkomst till den virtuella datorn. Kontot används endast för push-installation.

1. Du bör ha [skapat ett konto](vmware-walkthrough-prepare-vmware.md) som kan användas för push-installation. Sedan kan du ange det konto som du vill använda när du konfigurerar inställningar för datakälla under distributionen av Site Recovery.
2. Följ [instruktionerna](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) om du vill skicka mobilitetstjänsten på virtuella datorer som kör Windows eller Linux.

## <a name="other-methods"></a>Andra metoder

Lär dig mer om [mobilitetstjänsten med Configuration Manager installeras](site-recovery-install-mobility-service-using-sccm.md), eller med hjälp av [Azure Automation DSC](site-recovery-automate-mobility-service-install.md).

## <a name="next-steps"></a>Nästa steg

Gå till [steg 11: Aktivera replikering](vmware-walkthrough-enable-replication.md)
