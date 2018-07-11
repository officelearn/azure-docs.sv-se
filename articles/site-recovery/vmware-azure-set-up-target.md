---
title: Förbereda målmiljö för VMware-replikering till Azure | Microsoft Docs
description: Den här artikeln beskriver hur du förbereder mål-Azure-miljön för VMware-VM-replikering till Azure.
services: site-recovery
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: bsiva
ms.openlocfilehash: 4b428dff1cebf353cc081696649494e6e4ec9b92
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37921118"
---
# <a name="prepare-the-target-environment-for-vmware-replication-to-azure"></a>Förbereda målmiljö för VMware-replikering till Azure

Den här artikeln beskriver hur du förbereder mål-Azure-miljön att börja replikera virtuella VMware-datorer till Azure.

## <a name="prerequisites"></a>Förutsättningar

Artikeln förutsätter:
- Du har skapat ett Recovery Services-valv för att skydda dina virtuella VMware-datorer. Du kan skapa ett Recovery Services-valv från den [Azure-portalen](http://portal.azure.com "Azure-portalen").
- Du har [konfigurering av din lokala miljö](vmware-azure-set-up-source.md) att replikera virtuella VMware-datorer till Azure.

## <a name="prepare-target"></a>Förbered mål

När du har slutfört den **steg 1: Välj skyddsmål** och **steg 2: Förbered källa**, kommer du till **steg3: mål**

![Förbered mål](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Prenumeration:** från den nedrullningsbara menyn, Välj den prenumeration som du vill replikera dina virtuella datorer till.
2. **Distributionsmodell:** Välj distributionsmodell (klassisk eller Resource Manager)

Baserat på den valda distributionsmodellen, utförs en verifiering för att säkerställa att du har minst ett kompatibelt lagringskonto och virtuellt nätverk i målprenumerationen för replikering och redundans den virtuella datorn till.

När verifieringar slutförs korrekt, klickar du på OK om du vill gå till nästa steg.

Om du inte har en kompatibel Resource Manager-konto eller ett virtuellt nätverk kan du skapa på genom att klicka på den **+ Lagringskonto** eller **+ nätverk** knappar längst upp på sidan.

## <a name="next-steps"></a>Nästa steg
[Konfigurera replikeringsinställningar](vmware-azure-set-up-replication.md).
