---
title: Öppna portar till en virtuell dator med Azure portal | Microsoft Docs
description: Lär dig hur du öppnar en port / skapa en slutpunkt för din Windows-VM med hjälp av resource manager-distributionsmodellen i Azure Portal
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: cac17403425f53593d4f48692b4216a92c8624e3
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884393"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Hur du öppnar portar till en virtuell dator med Azure portal
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

1. Sök efter och välj resursgruppen för den virtuella datorn, väljer **Lägg till**, Sök sedan efter och välj **nätverkssäkerhetsgrupp**.

2. Välj **Skapa**.

    Den **skapa nätverkssäkerhetsgrupp** öppnas.

    ![Skapa en nätverkssäkerhetsgrupp](./media/nsg-quickstart-portal/create-nsg.png)

2. Ange ett namn för nätverkssäkerhetsgruppen. 

3. Välj eller skapa en resursgrupp och välj sedan en plats.

4. Välj **skapa** att skapa nätverkssäkerhetsgruppen.

## <a name="create-an-inbound-security-rule"></a>Skapa en inkommande säkerhetsregel

1. Välj nya nätverkssäkerhetsgruppen. 

2. Välj **ingående säkerhetsregler**och välj sedan **Lägg till**.

    ![Lägg till regel för inkommande trafik](./media/nsg-quickstart-portal/add-inbound-rule.png)

3. Välj **Avancerat**. 

4. Välj ett vanligt **Service** från den nedrullningsbara menyn som **HTTP**. Du kan också välja **anpassad** om du vill ange en specifik port som ska användas. 

5. Du kan också ändra den **prioritet** eller **namn**. Prioriteten påverkar den ordning som regler tillämpas: desto lägre det numeriska värdet, den tidigare regeln tillämpas.

6. Välj **Lägg till** att skapa regeln.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Associera nätverkssäkerhetsgruppen med ett undernät

Det sista steget är att associera nätverkssäkerhetsgruppen med ett undernät eller ett visst nätverksgränssnitt. I det här exemplet ska vi associera nätverkssäkerhetsgruppen med ett undernät. 

1. Välj **undernät**och välj sedan **associera**.

    ![Associera en nätverkssäkerhetsgrupp med ett undernät](./media/nsg-quickstart-portal/associate-subnet.png)

2. Välj ditt virtuella nätverk och välj sedan lämpligt undernät.

    ![Associera en nätverkssäkerhetsgrupp med virtuella nätverk](./media/nsg-quickstart-portal/select-vnet-subnet.png)

    Alla virtuella datorer som du ansluter till det undernätet är nu kan nås på port 80.

## <a name="additional-information"></a>Ytterligare information

Du kan också [utföra stegen i den här artikeln med hjälp av Azure PowerShell](nsg-quickstart-powershell.md).

De kommandon som beskrivs i den här artikeln kan du snabbt få trafiken som flödar till den virtuella datorn. Nätverkssäkerhetsgrupper ger många bra funktioner och kornighet för att styra åtkomst till resurser. Mer information finns i [filtrera nätverkstrafik med en nätverkssäkerhetsgrupp](../../virtual-network/tutorial-filter-network-traffic.md).

Överväg att placera dina virtuella datorer bakom en Azure load balancer för hög tillgänglighet webbprogram. Belastningsutjämnaren distribuerar trafik till virtuella datorer, med en nätverkssäkerhetsgrupp som tillhandahåller trafikfiltrering. Mer information finns i [Windows virtuella datorer i Azure för att skapa ett program med hög tillgänglighet för belastningsutjämning](tutorial-load-balancer.md).

## <a name="next-steps"></a>Nästa steg
I den här artikeln skapade du en nätverkssäkerhetsgrupp skapas en inkommande regel som tillåter HTTP-trafik på port 80 och sedan kopplade till regeln ett undernät. 

Du hittar information om hur du skapar mer detaljerad miljöer i följande artiklar:
- [Översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
- [Säkerhetsgrupper](../../virtual-network/security-overview.md)