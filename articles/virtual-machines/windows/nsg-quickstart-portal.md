---
title: Öppna portar till en virtuell dator med hjälp av Azure Portal
description: Lär dig hur du öppnar en port/skapar en slut punkt för din virtuella Windows-dator med Resource Manager-distributions modellen i Azure Portal
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 130d3315b5a9a6f175bd3d67ed33a034ab5f8dda
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75371419"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Så här öppnar du portar till en virtuell dator med Azure Portal
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

1. Sök efter och Välj resurs gruppen för den virtuella datorn, Välj **Lägg till**och Sök sedan efter och välj **nätverks säkerhets grupp**.

2. Välj **Skapa**.

    Fönstret **skapa nätverks säkerhets grupp** öppnas.

    ![Skapa en nätverkssäkerhetsgrupp](./media/nsg-quickstart-portal/create-nsg.png)

2. Ange ett namn för nätverks säkerhets gruppen. 

3. Välj eller skapa en resurs grupp och välj sedan en plats.

4. Välj **skapa** för att skapa nätverks säkerhets gruppen.

## <a name="create-an-inbound-security-rule"></a>Skapa en inkommande säkerhets regel

1. Välj den nya nätverks säkerhets gruppen. 

2. Välj **inkommande säkerhets regler**och välj sedan **Lägg till**.

    ![Lägg till regel för inkommande trafik](./media/nsg-quickstart-portal/add-inbound-rule.png)

3. Välj **Avancerat**. 

4. Välj en gemensam **tjänst** i den nedrullningsbara menyn, till exempel **http**. Du kan också välja **anpassad** om du vill ange en speciell port som ska användas. 

5. Du kan också ändra **prioritet** eller **namn**. Prioriteten påverkar ordningen i vilken reglerna tillämpas: det lägre numeriska värdet, som är det tidigare regeln tillämpas.

6. Välj **Lägg till** för att skapa regeln.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Associera nätverks säkerhets gruppen med ett undernät

Det sista steget är att associera nätverkssäkerhetsgruppen med ett undernät eller ett visst nätverksgränssnitt. I det här exemplet associerar vi nätverks säkerhets gruppen med ett undernät. 

1. Välj **undernät**och välj sedan **associera**.

    ![Associera en nätverks säkerhets grupp med ett undernät](./media/nsg-quickstart-portal/associate-subnet.png)

2. Välj ditt virtuella nätverk och välj sedan lämpligt undernät.

    ![Associera en nätverks säkerhets grupp med virtuella nätverk](./media/nsg-quickstart-portal/select-vnet-subnet.png)

    Alla virtuella datorer som du ansluter till det under nätet är nu tillgängliga på port 80.

## <a name="additional-information"></a>Ytterligare information

Du kan också [utföra stegen i den här artikeln med hjälp av Azure PowerShell](nsg-quickstart-powershell.md).

Kommandona som beskrivs i den här artikeln gör att du snabbt kan få trafik som flödar till den virtuella datorn. Nätverks säkerhets grupper ger många fantastiska funktioner och granularitet för att kontrol lera åtkomsten till dina resurser. Mer information finns i [filtrera nätverks trafik med en nätverks säkerhets grupp](../../virtual-network/tutorial-filter-network-traffic.md).

För webb program med hög tillgänglighet bör du överväga att placera dina virtuella datorer bakom en Azure Load Balancer. Belastningsutjämnaren distribuerar trafik till virtuella datorer, med en nätverks säkerhets grupp som tillhandahåller trafik filtrering. Mer information finns i [belastningsutjämna virtuella Windows-datorer i Azure för att skapa ett program med hög](tutorial-load-balancer.md)tillgänglighet.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du skapat en nätverks säkerhets grupp, skapat en regel för inkommande trafik som tillåter HTTP-trafik på port 80 och sedan associerar regeln med ett undernät. 

Du hittar information om att skapa mer detaljerade miljöer i följande artiklar:
- [Översikt över Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Säkerhets grupper](../../virtual-network/security-overview.md)