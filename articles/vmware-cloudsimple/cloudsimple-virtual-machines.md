---
title: Översikt över virtuella datorer
titleSuffix: Azure VMware Solution by CloudSimple
description: Läs mer om CloudSimple virtuella datorer och deras fördelar.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 436a5ab6fb286ce206f981487b6a29d774ecd1a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024916"
---
# <a name="cloudsimple-virtual-machines-overview"></a>CloudSimple virtuella datorer översikt

CloudSimple kan du hantera virtuella VMware-datorer (VMs) från Azure-portalen.  Ett kluster eller en resurspool från ditt vSphere-kluster hanteras via Azure genom att mappa det till din prenumeration.

Om du vill skapa en CloudSimple-virtuell dator från Azure måste det finnas en VM-mall på ditt privata moln vCenter.  Mallen används för att anpassa operativsystemet och programmen.  Mallen VM kan härdas för att uppfylla företagets säkerhetsprinciper.  Du kan använda mallen för att skapa virtuella datorer och sedan använda dem från Azure-portalen med hjälp av en självbetjäningsmodell.

## <a name="benefits"></a>Fördelar

CloudSimple virtuella datorer från Azure Portal ger en självbetjäningsmekanism för användare att skapa och hantera virtuella VMware-datorer.

* Skapa en CloudSimple-vm på ditt privata moln vCenter
* Hantera vm-egenskaper
  * Lägga till/ta bort diskar
  * Lägga till/ta bort nätverkskort
* Power-drift av din CloudSimple-vm
  * Slå på och stänga av
  * Återställ virtuell dator
* Ta bort virtuell dator

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [använder virtuella virtuella datorer med VMware på Azure](quickstart-create-vmware-virtual-machine.md)
* Läs om hur du [kartlägger din Azure-prenumeration](azure-subscription-mapping.md)
