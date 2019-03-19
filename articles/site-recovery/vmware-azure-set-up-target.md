---
title: Förbereda målmiljö för VMware-replikering till Azure | Microsoft Docs
description: Den här artikeln beskriver hur du förbereder mål-Azure-miljön för VMware-VM-replikering till Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: e75d4b1701944e206fcf6ded2dcb6d1e1fbc77cb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57900564"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Förbereda målmiljö för haveriberedskap för virtuella VMware-datorer eller fysiska servrar till Azure

Den här artikeln beskriver hur du förbereder mål-Azure-miljön att börja replikera virtuella VMware-datorer eller fysiska servrar till Azure.

## <a name="prerequisites"></a>Förutsättningar

Artikeln förutsätter:
- Du har skapat ett Recovery Services-valv på [Azure-portalen](https://portal.azure.com "Azure-portalen") att skydda din källdatorer
- Du har konfigurerat din lokala miljö för att replikera källan [virtuella VMware-datorer](vmware-azure-set-up-source.md) eller [fysiska servrar](physical-azure-set-up-source.md) till Azure.

## <a name="prepare-target"></a>Förbered målet

När du har slutfört den **steg 1: Välj skyddsmål** och **steg 2: Förbered källa**, kommer du till **steg3: Mål**

![Förbered målet](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Prenumeration:** Välj den prenumeration som du vill replikera dina virtuella datorer eller fysiska servrar till från den nedrullningsbara menyn.
2. **Distributionsmodell:** Välj distributionsmodell (klassisk eller Resource Manager)

Baserat på den valda distributionsmodellen, utförs en verifiering för att säkerställa att du har minst ett virtuellt nätverk i målprenumerationen att replikera och redundansväxla din virtuella dator eller fysisk server till.

När verifieringar slutförs korrekt, klickar du på OK om du vill gå till nästa steg.

Om du inte har ett virtuellt nätverk kan du skapa en genom att klicka på den **+ nätverk** längst upp på sidan.

## <a name="next-steps"></a>Nästa steg
[Konfigurera replikeringsinställningar](vmware-azure-set-up-replication.md).
