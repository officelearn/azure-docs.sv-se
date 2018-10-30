---
title: Förbereda målmiljö för VMware-replikering till Azure | Microsoft Docs
description: Den här artikeln beskriver hur du förbereder mål-Azure-miljön för VMware-VM-replikering till Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: a6f983b08415659b9a989ebed824cddd210396e1
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233437"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Förbereda målmiljö för haveriberedskap för virtuella VMware-datorer eller fysiska servrar till Azure

Den här artikeln beskriver hur du förbereder mål-Azure-miljön att börja replikera virtuella VMware-datorer eller fysiska servrar till Azure.

## <a name="prerequisites"></a>Förutsättningar

Artikeln förutsätter:
- Du har skapat ett Recovery Services-valv på [Azure-portalen](http://portal.azure.com "Azure-portalen") att skydda din källdatorer
- Du har konfigurerat din lokala miljö för att replikera källan [virtuella VMware-datorer](vmware-azure-set-up-source.md) eller [fysiska servrar](physical-azure-set-up-source.md) till Azure.

## <a name="prepare-target"></a>Förbered mål

När du har slutfört den **steg 1: Välj skyddsmål** och **steg 2: Förbered källa**, kommer du till **steg3: mål**

![Förbered mål](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Prenumeration:** från den nedrullningsbara menyn, Välj den prenumeration som du vill replikera dina virtuella datorer eller fysiska servrar till.
2. **Distributionsmodell:** Välj distributionsmodell (klassisk eller Resource Manager)

Baserat på den valda distributionsmodellen, utförs en verifiering för att säkerställa att du har minst ett kompatibelt lagringskonto och virtuellt nätverk i målprenumerationen för replikering och redundans din virtuella dator eller fysisk server till.

När verifieringar slutförs korrekt, klickar du på OK om du vill gå till nästa steg.

Om du inte har en kompatibel Resource Manager-konto eller ett virtuellt nätverk kan du skapa en genom att klicka på den **+ Lagringskonto** eller **+ nätverk** knappar längst upp på sidan.

## <a name="next-steps"></a>Nästa steg
[Konfigurera replikeringsinställningar](vmware-azure-set-up-replication.md).
