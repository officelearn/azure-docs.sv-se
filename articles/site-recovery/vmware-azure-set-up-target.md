---
title: Förbereda VMware VM-replikeringsmålet i Azure Site Recovery
description: I den här artikeln beskrivs hur du förbereder din azure-målmiljö för VMware VM-replikering till Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: c721234f2e1d806d51d31f3466e441bf8360f6b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73693161"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Förbereda målmiljön för haveriberedskap av virtuella datorer eller fysiska servrar till Azure

I den här artikeln beskrivs hur du förbereder din Azure-målgrupp för att börja replikera virtuella VMware-datorer eller fysiska servrar till Azure.

## <a name="prerequisites"></a>Krav

Artikeln förutsätter:
- Du har skapat ett Recovery Services Vault på [Azure-portalen](https://portal.azure.com "Azure Portal") för att skydda källdatorerna
- Du har konfigurerat din lokala miljö för att replikera källan [VMware virtuella datorer](vmware-azure-set-up-source.md) eller [fysiska servrar](physical-azure-set-up-source.md) till Azure.

## <a name="prepare-target"></a>Förbered målet

När du har slutfört **steg 1: Välj skyddsmål** och **steg 2: Förbered källa**tas du till steg **3: Mål**

![Förbered målet](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Prenumeration:** På den nedrullningsbara menyn väljer du den prenumeration som du vill replikera dina virtuella datorer eller fysiska servrar till.
2. **Distributionsmodell:** Välj distributionsmodell (Classic eller Resource Manager)

Baserat på den valda distributionsmodellen körs en validering för att säkerställa att du har minst ett virtuellt nätverk i målprenumerationen för att replikera och redundans din virtuella dator eller fysiska server till.

När valideringarna har slutförts klickar du på OK för att gå till nästa steg.

Om du inte har ett virtuellt nätverk kan du skapa ett genom att klicka på knappen **+ Nätverk** högst upp på sidan.

## <a name="next-steps"></a>Nästa steg
[Konfigurera replikeringsinställningar](vmware-azure-set-up-replication.md).
