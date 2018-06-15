---
title: Förbereda mål (fysisk till Azure) | Microsoft Docs
description: Den här artikeln beskriver hur du förbereder din Azure-miljön att starta replikering av fysiska servrar som kör Windows eller Linux till Azure.
services: site-recovery
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: bsiva
ms.openlocfilehash: a2465bb3397a175b6ad8b8be0de933dfae1dee5b
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
ms.locfileid: "29812630"
---
# <a name="prepare-target-vmware-to-azure"></a>Förbereda mål (VMware till Azure)

Den här artikeln beskriver hur du förbereder din Azure-miljön att starta replikering av fysiska servrar (x 64) som kör Windows eller Linux till Azure.

## <a name="prerequisites"></a>Förutsättningar

Artikeln förutsätter:
- Du har skapat en Återställningstjänstvalvet för att skydda din fysiska servrar. Du kan skapa ett Recovery Services-valv från den [Azure-portalen](http://portal.azure.com "Azure-portalen").
- Du har [konfigurering av din lokala miljö](physical-azure-disaster-recovery.md) att replikera fysiska servrar till Azure.

## <a name="prepare-target"></a>Förbereda mål

När du har slutfört den **steg 1: Välj skyddsmål** och **steg 2: Förbered källa**, kommer du till **steg3: mål**

![Förbereda mål](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Prenumerationen:** Välj den prenumeration som du vill replikera dina fysiska servrar till i den nedrullningsbara menyn.
2. **Distributionsmodell:** Välj distributionsmodell (klassiska eller Resource Manager)

Baserat på den valda distributionsmodellen körs en verifiering för att säkerställa att du har minst en kompatibel storage-konto och virtuella nätverk i målprenumerationen för replikering och redundans din fysiska servrar med.

Klicka på OK för att gå till nästa steg när validering slutföras.

Om du inte har en kompatibel Resource Manager storage-konto eller ett virtuellt nätverk, kan du skapa en genom att klicka på den **+ Lagringskonto** eller **+ nätverk** knappar överst på sidan.

## <a name="next-steps"></a>Nästa steg
[Konfigurera replikeringsinställningar](vmware-azure-set-up-replication.md).
