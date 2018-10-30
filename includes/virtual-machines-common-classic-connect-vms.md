---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2e72d669abcc784fe8159fd4c54bd074dc60299c
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226496"
---
![Virtuella datorer i en fristående molntjänst](./media/virtual-machines-common-classic-connect-vms/CloudServiceExample.png)

Om du placerar dina virtuella datorer i ett virtuellt nätverk kan du bestämma hur många molntjänster som du vill använda för läsa in belastningsutjämning och tillgänglighetsuppsättningar. Dessutom kan du sortera de virtuella datorerna på undernät på samma sätt som ditt lokala nätverk och anslut det virtuella nätverket till ditt lokala nätverk. Här är ett exempel:

![Virtuella datorer i ett virtuellt nätverk](./media/virtual-machines-common-classic-connect-vms/VirtualNetworkExample.png)

Virtuella nätverk är det rekommendera sättet att ansluta virtuella datorer i Azure. Det bästa sättet är att konfigurera varje nivå av ditt program i en separat molntjänst. Du kan dock behöva kombinera vissa virtuella datorer från olika programnivåer i samma molntjänst ska finnas kvar i upp till 200 molntjänster per prenumeration. Det här och andra begränsningar finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../articles/azure-subscription-service-limits.md).

## <a name="connect-vms-in-a-virtual-network"></a>Ansluta virtuella datorer i ett virtuellt nätverk
Att ansluta virtuella datorer i ett virtuellt nätverk:

1. Skapa det virtuella nätverket i den [Azure-portalen](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md) och ange ”klassisk distribution”.
2. Skapa uppsättning molntjänster för distributionen för att återspegla din design för tillgänglighet och belastningsutjämning. I Azure-portalen klickar du på **skapa en resurs > Compute > molntjänst** för varje tjänst i molnet.

  När du fyller i molntjänstinformation Välj samma _resursgrupp_ används med det virtuella nätverket.

3. För att skapa varje ny virtuell dator, klickar du på **skapa en resurs > Compute**, Välj en lämplig VM-avbildning från den **aktuella appar**.

  I den virtuella datorn **grunderna** bladet Välj samma _resursgrupp_ används med det virtuella nätverket.

  ![Grunderna i VM-bladet när du använder ett virtuellt nätverk](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_VN.png)

4. När du fyller den virtuella datorn **inställningar**, Välj rätt _molntjänst_ eller _virtuellt nätverk_ för den virtuella datorn.

  Azure väljer det andra objektet baserat på ditt val.

  ![Bladet för VM-inställningar när du använder ett virtuellt nätverk](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_VN.png)


## <a name="connect-vms-in-a-standalone-cloud-service"></a>Ansluta virtuella datorer i en fristående molntjänst
Att ansluta virtuella datorer i en fristående molntjänst:

1. Skapa molntjänst i den [Azure-portalen](http://portal.azure.com). Klicka på **New > Compute > molntjänst**. Du kan också skapa Molntjänsten för din distribution när du skapar din första virtuella dator.
2. När du skapar virtuella datorer väljer du samma resursgrupp som används med Molntjänsten.

  ![Lägg till en virtuell dator i en befintlig molntjänst](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_SA.png)

3.  Välj namnet på Molntjänsten som skapats i det första steget när du fyller VM-information.

  ![Att välja en molnbaserad tjänst för en virtuell dator](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_SA.png)
