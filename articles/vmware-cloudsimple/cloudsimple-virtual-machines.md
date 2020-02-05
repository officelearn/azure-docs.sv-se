---
title: Översikt över Azure VMware-lösningar (AVS) – virtuella datorer
description: Lär dig mer om virtuella datorer i AVS och deras fördelar.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e6cc6d51e37cea98cc553a236295311d21f595a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024916"
---
# <a name="avs-virtual-machines-overview"></a>Översikt över AVS-virtuella datorer

Med AVS kan du hantera virtuella VMware-datorer (VM) från Azure Portal. Ett kluster eller en resurspool från ditt vSphere-kluster hanteras via Azure genom att mappas till din prenumeration.

Om du vill skapa en virtuell AVS-dator från Azure måste det finnas en VM-mall i ditt molns privata moln vCenter. Mallen används för att anpassa operativ system och program. Mallen VM kan vara skärpd för att uppfylla företagets säkerhets principer. Du kan använda mallen för att skapa virtuella datorer och sedan använda dem från Azure Portal med en självbetjänings modell.

## <a name="benefits"></a>Erbjudande

Virtuella AVS-datorer från Azure Portal tillhandahåller en självbetjänings funktion som användare kan använda för att skapa och hantera virtuella VMware-datorer.

* Skapa en virtuell dator i AVS-molnet i din moln-vCenter
* Hantera VM-egenskaper
  * Lägg till/ta bort diskar
  * Lägg till/ta bort nätverkskort
* Energi åtgärder för din AVS-VM
  * Ström och avstängning
  * Återställ virtuell dator
* Ta bort virtuell dator

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [använder virtuella VMware-datorer i Azure](quickstart-create-vmware-virtual-machine.md)
* Lär dig hur du [mappar din Azure-prenumeration](azure-subscription-mapping.md)
