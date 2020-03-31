---
title: Konfigurera målmiljön för fysiska servrar i Azure Site Recovery
description: I den här artikeln beskrivs hur du konfigurerar azure-målmiljön för haveriberedskap av fysiska servrar med Hjälp av Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 685f04b27cd315b04ef5c45e155e825eebe2747b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953906"
---
# <a name="prepare-target-vmware-to-azure"></a>Förbered mål (VMware till Azure)

I den här artikeln beskrivs hur du förbereder din Azure-miljö för att börja replikera fysiska servrar (x64) som kör Windows eller Linux till Azure.

## <a name="prerequisites"></a>Krav

Artikeln förutsätter:
- Du har skapat ett Recovery Services Vault för att skydda dina fysiska servrar. Du kan skapa ett Recovery Services Vault från [Azure-portalen](https://portal.azure.com "Azure Portal").
- Du har [konfigurerat din lokala miljö](physical-azure-disaster-recovery.md) för att replikera fysiska servrar till Azure.

## <a name="prepare-target"></a>Förbered målet

När du har slutfört **steg 1:Välj skyddsmål** och **steg 2:Förbered källa**tas du till steg **3: Mål**

![Förbered målet](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Prenumeration:** Välj den prenumeration som du vill replikera dina fysiska servrar till på den nedrullningsbara menyn.
2. **Distributionsmodell:** Välj distributionsmodell (Classic eller Resource Manager)

Baserat på den valda distributionsmodellen körs en validering för att säkerställa att du har minst ett kompatibelt lagringskonto och virtuellt nätverk i målprenumerationen för att replikera och redundans dina fysiska servrar till.

När valideringarna har slutförts klickar du på OK för att gå till nästa steg.

Om du inte har ett kompatibelt Resource Manager-lagringskonto eller virtuellt nätverk kan du skapa ett genom att klicka på knapparna **+ Lagringskonto** eller **+ Nätverk** högst upp på sidan.

## <a name="next-steps"></a>Nästa steg
[Konfigurera replikeringsinställningar](vmware-azure-set-up-replication.md).
