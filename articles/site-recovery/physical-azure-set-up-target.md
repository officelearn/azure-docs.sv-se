---
title: Konfigurera målmiljön för haveriberedskap för lokala fysiska servrar till Azure | Microsoft Docs
description: Den här artikeln beskriver hur du ställer in mål Azure-miljön för haveriberedskap för fysiska servrar med Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 41220ccdca945610d7d8ca87af0857114e2cef85
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60949089"
---
# <a name="prepare-target-vmware-to-azure"></a>Förbered mål (VMware till Azure)

Den här artikeln beskriver hur du förbereder Azure-miljön att börja replikera fysiska servrar (x 64) som kör Windows eller Linux till Azure.

## <a name="prerequisites"></a>Nödvändiga komponenter

Artikeln förutsätter:
- Du har skapat ett Recovery Services-valv för att skydda din fysiska servrar. Du kan skapa ett Recovery Services-valv från den [Azure-portalen](https://portal.azure.com "Azure-portalen").
- Du har [konfigurering av din lokala miljö](physical-azure-disaster-recovery.md) att replikera fysiska servrar till Azure.

## <a name="prepare-target"></a>Förbered målet

När du har slutfört den **steg 1: Välj skyddsmål** och **steg 2: Förbered källa**, kommer du till **steg3: Mål**

![Förbered målet](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Prenumeration:** Välj den prenumeration som du vill replikera dina fysiska servrar till från den nedrullningsbara menyn.
2. **Distributionsmodell:** Välj distributionsmodell (klassisk eller Resource Manager)

Baserat på den valda distributionsmodellen, utförs en verifiering för att säkerställa att du har minst ett kompatibelt lagringskonto och virtuellt nätverk i målprenumerationen för replikering och redundans din fysiska servrar till.

När verifieringar slutförs korrekt, klickar du på OK om du vill gå till nästa steg.

Om du inte har en kompatibel Resource Manager-konto eller ett virtuellt nätverk kan du skapa en genom att klicka på den **+ Lagringskonto** eller **+ nätverk** knappar längst upp på sidan.

## <a name="next-steps"></a>Nästa steg
[Konfigurera replikeringsinställningar](vmware-azure-set-up-replication.md).
