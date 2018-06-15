---
title: Förbereda mål (fysisk till Azure) | Microsoft Docs
description: Den här artikeln beskriver hur du förbereder din Azure-miljön att starta replikering av fysiska servrar som kör Windows eller Linux till Azure.
services: site-recovery
documentationcenter: ''
author: bsiva
manager: abhemraj
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 02/22/2018
ms.author: bsiva
ms.openlocfilehash: 6704ddc24db8415051a6064bbde79a6fc527871a
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2018
ms.locfileid: "29768419"
---
# <a name="prepare-target-physical-to-azure"></a>Förbereda mål (fysisk till Azure)
> [!div class="op_single_selector"]
> * [VMware till Azure](./site-recovery-prepare-target-vmware-to-azure.md)
> * [Fysiska till Azure](./site-recovery-prepare-target-physical-to-azure.md)

Den här artikeln beskriver hur du förbereder din Azure-miljön att starta replikering av fysiska servrar (x 64) som kör Windows eller Linux till Azure.

## <a name="prerequisites"></a>Förutsättningar

Artikeln förutsätter:
- Du har skapat en Återställningstjänstvalvet för att skydda din fysiska servrar. Du kan skapa ett Recovery Services-valv från den [Azure-portalen](http://portal.azure.com "Azure-portalen").
- Du har [konfigurering av din lokala miljö](./site-recovery-set-up-physical-to-azure.md) att replikera fysiska servrar till Azure.

## <a name="prepare-target"></a>Förbereda mål

När du har slutfört den **steg 1: Välj skyddsmål** och **steg 2: Förbered källa**, kommer du till **steg3: mål**

![Förbereda mål](./media/site-recovery-prepare-target-physical-to-azure/prepare-target-physical-to-azure.png)

1. **Prenumerationen:** Välj den prenumeration som du vill replikera dina fysiska servrar till i den nedrullningsbara menyn.
2. **Distributionsmodell:** Välj distributionsmodell (klassiska eller Resource Manager)

Baserat på den valda distributionsmodellen körs en verifiering för att säkerställa att du har minst en kompatibel storage-konto och virtuella nätverk i målprenumerationen för replikering och redundans din fysiska servrar med.

Klicka på OK för att gå till nästa steg när validering slutföras.

Om du inte har en kompatibel Resource Manager storage-konto eller ett virtuellt nätverk, kan du skapa en genom att klicka på den **+ Lagringskonto** eller **+ nätverk** knappar överst på sidan.

## <a name="next-steps"></a>Nästa steg
[Konfigurera replikeringsinställningar](./site-recovery-setup-replication-settings-vmware.md).
