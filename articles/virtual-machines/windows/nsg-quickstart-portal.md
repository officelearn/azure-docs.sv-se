---
title: Öppna portar till en virtuell dator med Azure-portalen
description: Lär dig hur du öppnar en port /skapar en slutpunkt till din Virtuella Windows-dator med Azure Portal
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 1844236a77b688819832b3fe0bf6736beea4bfae
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865493"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Så här öppnar du portar till en virtuell dator med Azure-portalen
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

1. Sök efter och välj resursgruppen för den virtuella datorn, välj **Lägg till**och sök sedan efter och välj **Nätverkssäkerhetsgrupp**.

2. Välj **Skapa**.

    Fönstret **Skapa nätverkssäkerhetsgrupp** öppnas.

    ![Skapa en nätverkssäkerhetsgrupp](./media/nsg-quickstart-portal/create-nsg.png)

2. Ange ett namn på nätverkssäkerhetsgruppen. 

3. Markera eller skapa en resursgrupp och välj sedan en plats.

4. Välj **Skapa** om du vill skapa nätverkssäkerhetsgruppen.

## <a name="create-an-inbound-security-rule"></a>Skapa en säkerhetsregel för inkommande

1. Välj din nya nätverkssäkerhetsgrupp. 

2. Välj **Regler för inkommande säkerhet**och välj sedan Lägg **till**.

    ![Lägga till inkommande regel](./media/nsg-quickstart-portal/add-inbound-rule.png)

3. Välj **Avancerat**. 

4. Välj en gemensam **tjänst** på den nedrullningsvänliga menyn, till exempel **HTTP**. Du kan också välja **Anpassad** om du vill ange en specifik port som ska användas. 

5. Du kan också ändra **prioritet** eller **namn**. Prioriteten påverkar i vilken ordning regler tillämpas: ju lägre numeriskt värde, desto tidigare tillämpas regeln.

6. Välj **Lägg till** om du vill skapa regeln.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Associera nätverkssäkerhetsgruppen med ett undernät

Det sista steget är att associera nätverkssäkerhetsgruppen med ett undernät eller ett visst nätverksgränssnitt. I det här exemplet associerar vi nätverkssäkerhetsgruppen med ett undernät. 

1. Välj **Undernät**och välj sedan **Associera**.

    ![Associera en nätverkssäkerhetsgrupp med ett undernät](./media/nsg-quickstart-portal/associate-subnet.png)

2. Välj ditt virtuella nätverk och välj sedan lämpligt undernät.

    ![Associera en nätverkssäkerhetsgrupp med virtuella nätverk](./media/nsg-quickstart-portal/select-vnet-subnet.png)

    Alla virtuella datorer som du ansluter till det undernätet kan nu nås på port 80.

## <a name="additional-information"></a>Ytterligare information

Du kan också [utföra stegen i den här artikeln med hjälp av Azure PowerShell](nsg-quickstart-powershell.md).

Med kommandona som beskrivs i den här artikeln kan du snabbt få trafik att flöda till den virtuella datorn. Nätverkssäkerhetsgrupper ger många bra funktioner och granularitet för att kontrollera åtkomsten till dina resurser. Mer information finns i [Filtrera nätverkstrafik med en nätverkssäkerhetsgrupp](../../virtual-network/tutorial-filter-network-traffic.md).

För webbprogram med hög tillgänglig tillgång bör du placera dina virtuella datorer bakom en Azure-belastningsutjämnare. Belastningsutjämnaren distribuerar trafik till virtuella datorer, med en nätverkssäkerhetsgrupp som tillhandahåller trafikfiltrering. Mer information finns i [Läsbelöningsappning av virtuella datorer i Windows i Azure för att skapa ett program med hög tillgänglig tillgång](tutorial-load-balancer.md).

## <a name="next-steps"></a>Nästa steg
I den här artikeln skapade du en nätverkssäkerhetsgrupp, skapade en inkommande regel som tillåter HTTP-trafik på port 80 och sedan associerade den regeln med ett undernät. 

Du hittar information om hur du skapar mer detaljerade miljöer i följande artiklar:
- [Översikt över Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Säkerhetsgrupper](../../virtual-network/security-overview.md)