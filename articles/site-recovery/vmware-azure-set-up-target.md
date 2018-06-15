---
title: Förbereda målmiljön för VMware-replikering till Azure | Microsoft Docs
description: Den här artikeln beskriver hur du förbereder dina mål Azure-miljön för VMware VM replikering till Azure.
services: site-recovery
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: bsiva
ms.openlocfilehash: 5f14bbed7ae59737f62fb736591775cb7ba495c5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
ms.locfileid: "29812637"
---
# <a name="prepare-the-target-environment-for-vmware-replication-to-azure"></a>Förbereda målmiljön för VMware-replikering till Azure

Den här artikeln beskriver hur du förbereder dina mål Azure-miljön att starta replikera virtuella VMware-datorer till Azure.

## <a name="prerequisites"></a>Förutsättningar

Artikeln förutsätter:
- Du har skapat ett Recovery Services-valv för att skydda din virtuella VMware-datorer. Du kan skapa ett Recovery Services-valv från den [Azure-portalen](http://portal.azure.com "Azure-portalen").
- Du har [konfigurering av din lokala miljö](vmware-azure-set-up-source.md) att replikera virtuella VMware-datorer till Azure.

## <a name="prepare-target"></a>Förbereda mål

När du har slutfört den **steg 1: Välj skyddsmål** och **steg 2: Förbered källa**, kommer du till **steg3: mål**

![Förbereda mål](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Prenumerationen:** Välj den prenumeration som du vill replikera dina virtuella datorer till i den nedrullningsbara menyn.
2. **Distributionsmodell:** Välj distributionsmodell (klassiska eller Resource Manager)

Baserat på den valda distributionsmodellen utförs en verifiering för att säkerställa att du har minst en kompatibel storage-konto och virtuella nätverk i målprenumerationen att replikera och växling vid fel den virtuella datorn till.

Klicka på OK för att gå till nästa steg när validering slutföras.

Om du inte har en kompatibel Resource Manager storage-konto eller ett virtuellt nätverk kan du skapa på genom att klicka på den **+ Lagringskonto** eller **+ nätverk** knappar överst på sidan.

## <a name="next-steps"></a>Nästa steg
[Konfigurera replikeringsinställningar](vmware-azure-set-up-replication.md).
