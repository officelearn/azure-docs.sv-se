---
title: "Förbereda mål (fysisk till Azure) | Microsoft Docs"
description: "Den här artikeln beskriver hur du förbereder din Azure-miljön att starta replikering av fysiska servrar som kör Windows eller Linux till Azure."
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhemraj
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 5/31/2017
ms.author: bsiva
ms.openlocfilehash: aa7a32ace8354f615a8b8cc137f6bdf48fbadf48
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-target-vmware-to-azure"></a>Förbereda mål (VMware till Azure)
> [!div class="op_single_selector"]
> * [VMware till Azure](./site-recovery-prepare-target-vmware-to-azure.md)
> * [Fysiska till Azure](./site-recovery-prepare-target-physical-to-azure.md)

Den här artikeln beskriver hur du förbereder din Azure-miljön att starta replikering av fysiska servrar (x 64) som kör Windows eller Linux till Azure.

## <a name="prerequisites"></a>Krav

Artikeln förutsätter följande:
- Du har skapat en Återställningstjänstvalvet för att skydda din fysiska servrar. Du kan skapa ett Recovery Services-valv från den [Azure-portalen](http://portal.azure.com "Azure-portalen").
- Du har [konfigurering av din lokala miljö](./site-recovery-set-up-physical-to-azure.md) att replikera fysiska servrar till Azure.

## <a name="prepare-target"></a>Förbereda mål

När du har slutfört den **steg 1: Välj skyddsmål** och **steg 2: Förbered källa**, kommer du till **steg3: mål**

![Förbereda mål](./media/site-recovery-prepare-target-physical-to-azure/prepare-target-physical-to-azure.png)

1. **Prenumerationen:** från nedrullningsbara menyn, Välj den prenumeration som du vill replikera dina fysiska servrar till.
2. **Distributionsmodell:** Välj distributionsmodell (klassiska eller Resource Manager)

Baserat på den valda distributionsmodellen körs en verifiering för att säkerställa att du har minst en kompatibel storage-konto och virtuella nätverk i målprenumerationen för replikering och redundans din fysiska servrar med.

Klicka på OK för att gå till nästa steg när validering slutföras.

Om du inte har en kompatibel Resource Manager storage-konto eller ett virtuellt nätverk eller skulle vilja lägga till fler, kan du göra det genom att klicka på den **+ Lagringskonto** eller **+ nätverk** knappar överst på bladet.

## <a name="next-steps"></a>Nästa steg
[Konfigurera replikeringsinställningar](./site-recovery-setup-replication-settings-vmware.md).
