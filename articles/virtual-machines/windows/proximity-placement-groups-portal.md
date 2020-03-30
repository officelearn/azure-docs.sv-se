---
title: Skapa en närhetsplaceringsgrupp med hjälp av portalen
description: Lär dig hur du skapar en närhetsplaceringsgrupp med Azure-portalen.
services: virtual-machines
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 8512d9b701242dc686d49bbe56e8a2059f14ee3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73180256"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Skapa en närhetsplaceringsgrupp med hjälp av portalen

Om du vill få virtuella datorer så nära som möjligt och uppnå lägsta möjliga svarstid bör du distribuera dem inom en [närhetsplaceringsgrupp](co-location.md#proximity-placement-groups).

En närhetsplaceringsgrupp är en logisk gruppering som används för att se till att Azure-beräkningsresurser är fysiskt placerade nära varandra. Närhetsplaceringsgrupper är användbara för arbetsbelastningar där låg svarstid är ett krav.


## <a name="create-the-proximity-placement-group"></a>Skapa proximity placement-gruppen

1. Skriv **proximity placement-gruppen** i sökningen.
1. Under **Tjänster** i sökresultaten väljer du **Grupper för placering av närhet**.
1. På sidan **Grupper för närhetsplacering** väljer du **Lägg till**.
1. Kontrollera att rätt prenumeration är markerad under **Projektinformation**på fliken **Grunderna.**
1. I **resursgruppen** väljer du antingen **Skapa ny** om du vill skapa en ny grupp eller väljer en befintlig resursgrupp i listrutan.
1. I **Region** väljer du den plats där du vill att närhetsplaceringsgruppen ska skapas.
1. I Namn **på närhetsplaceringsgrupp** skriver du ett namn och väljer sedan **Granska + skapa**.
1. När valideringen har gått väljer du **Skapa** för att skapa gruppen närhetsplacering.

    ![Skärmbild av hur du skapar en närhetsplaceringsgrupp](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Skapa en virtuell dator

1. När du skapar en virtuell dator i portalen går du till fliken **Avancerat.** 
1. Välj rätt placeringsgrupp i gruppvalet **Närhetsplacering.** 

    ![Skärmbild av avsnittet närhetsplaceringsgrupp när du skapar en ny virtuell dator i portalen](./media/ppg/vm-ppg.png)

1. När du är klar med alla andra obligatoriska val väljer du **Granska + skapa**.
1. När valideringen har godkänts väljer du **Skapa** för att distribuera den virtuella datorn i placeringsgruppen.




## <a name="next-steps"></a>Nästa steg

Du kan också använda [Azure PowerShell](proximity-placement-groups.md) för att skapa närhetsplaceringsgrupper.

