---
title: VMware-lösning av CloudSimple – översikt över virtuella Azure-datorer
description: Lär dig mer om CloudSimple Virtual Machines och deras fördelar.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0f4967bbd12107bf6a04cb80537d4425c75c5f46
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812487"
---
# <a name="cloudsimple-virtual-machines-overview"></a>Översikt över virtuella CloudSimple-datorer

Med CloudSimple kan du hantera virtuella VMware-datorer från Azure Portal.  Ett kluster eller en resurspool från ditt vSphere-kluster hanteras via Azure genom att mappas till din prenumeration.  CloudSimple Virtual Machine ger självbetjänings hantering av virtuella VMware-datorer från Azure Portal.  

För att skapa en virtuell CloudSimple-dator från Azure måste det finnas en VM-mall i ditt privata moln vCenter.  Mallen används för att anpassa operativ system och program.  Mallen VM kan vara skärpd för att uppfylla företagets säkerhets principer.  Du kan använda mallen för att skapa virtuella datorer och använda dem från Azure Portal med en självbetjänings modell.

## <a name="benefits"></a>Fördelar

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
* Lär dig hur du [mappar din Azure-prenumeration](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)