---
title: Skapa en närhets placerings grupp med hjälp av portalen
description: Lär dig hur du skapar en närhets placerings grupp med hjälp av Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: aaecfbd14289840e795c6323737877e267586e16
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82098653"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Skapa en närhets placerings grupp med hjälp av portalen

För att få virtuella datorer så nära som möjligt, vilket ger lägsta möjliga fördröjning, bör du distribuera dem i en [närhets placerings grupp](co-location.md#proximity-placement-groups).

En närhets placerings grupp är en logisk gruppering som används för att se till att Azure Compute-resurser är fysiskt placerade nära varandra. Placerings grupper för närhet är användbara för arbets belastningar där låg latens är ett krav.


## <a name="create-the-proximity-placement-group"></a>Skapa placerings gruppen närhet

1. Skriv **närhets placerings grupp** i sökningen.
1. Under **tjänster** i Sök resultaten väljer du **närhets placerings grupper**.
1. På sidan **närhets placerings grupper** väljer du **Lägg till**.
1. På fliken **grundläggande** , under **projekt information**, se till att rätt prenumeration är markerad.
1. I **resurs grupp** väljer du antingen **Skapa ny** för att skapa en ny grupp eller väljer en befintlig resurs grupp i list rutan.
1. I **region** väljer du den plats där du vill att placerings gruppen för närhet ska skapas.
1. I **närhet placerings grupp namn** skriver du ett namn och väljer sedan **Granska och skapa**.
1. När verifieringen är klar väljer du **skapa** för att skapa närhets gruppen.

    ![Skärm bild där du skapar en närhets placerings grupp](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Skapa en virtuell dator

1. När du skapar en virtuell dator i portalen går du till fliken **Avancerat** . 
1. I området **närhets placering** väljer du rätt placerings grupp. 

    ![Skärm bild av avsnittet närhets placerings grupp när du skapar en ny virtuell dator i portalen](./media/ppg/vm-ppg.png)

1. När du har gjort alla andra obligatoriska val väljer du **Granska + skapa**.
1. När verifieringen har godkänts väljer du **skapa** för att distribuera den virtuella datorn i placerings gruppen.




## <a name="next-steps"></a>Nästa steg

Du kan också använda [Azure PowerShell](proximity-placement-groups.md) för att skapa närhets grupper.

