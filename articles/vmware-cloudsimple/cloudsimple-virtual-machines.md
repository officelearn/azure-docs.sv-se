---
title: Översikt över virtuella datorer
titleSuffix: Azure VMware Solution by CloudSimple
description: Lär dig mer om CloudSimple Virtual Machines och deras fördelar.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 436a5ab6fb286ce206f981487b6a29d774ecd1a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75372797"
---
# <a name="cloudsimple-virtual-machines-overview"></a>Översikt över virtuella CloudSimple-datorer

Med CloudSimple kan du hantera virtuella VMware-datorer (VM) från Azure Portal.  Ett kluster eller en resurspool från ditt vSphere-kluster hanteras via Azure genom att mappas till din prenumeration.

För att skapa en virtuell CloudSimple-dator från Azure måste det finnas en VM-mall i ditt privata moln vCenter.  Mallen används för att anpassa operativ system och program.  Mallen VM kan vara skärpd för att uppfylla företagets säkerhets principer.  Du kan använda mallen för att skapa virtuella datorer och sedan använda dem från Azure Portal med en självbetjänings modell.

## <a name="benefits"></a>Erbjudande

CloudSimple virtuella datorer från Azure Portal ger en självbetjänings funktion som användare kan använda för att skapa och hantera virtuella VMware-datorer.

* Skapa en virtuell CloudSimple-dator i ditt privata moln vCenter
* Hantera VM-egenskaper
  * Lägg till/ta bort diskar
  * Lägg till/ta bort nätverkskort
* Energi åtgärder för din virtuella CloudSimple-dator
  * Ström och avstängning
  * Återställ virtuell dator
* Ta bort virtuell dator

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [använder virtuella VMware-datorer i Azure](quickstart-create-vmware-virtual-machine.md)
* Lär dig hur du [mappar din Azure-prenumeration](azure-subscription-mapping.md)
