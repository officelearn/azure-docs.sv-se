---
title: "Öppna portar till en virtuell dator med hjälp av Azure portal | Microsoft Docs"
description: "Lär dig hur du öppnar en port / skapa en slutpunkt för din Windows-VM med hjälp av resource manager-distributionsmodellen i Azure Portal"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: 08f0af6ecdb45b263d39c3d2d6442f4ed555e3c3
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2018
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Hur du öppnar portar till en virtuell dator med Azure-portalen
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Snabbkommandon
Du kan också [utför dessa steg med hjälp av Azure PowerShell](nsg-quickstart-powershell.md).

Först skapa en säkerhetsgrupp för ditt nätverk. Välj en resursgrupp i portalen, Välj **Lägg till**, och Sök efter och välj **nätverkssäkerhetsgruppen**:

![Lägg till en Nätverkssäkerhetsgrupp](./media/nsg-quickstart-portal/add-nsg.png)

Ange ett namn för din Nätverkssäkerhetsgruppen, Välj eller skapa en resursgrupp och välja en plats. Välj **skapa** när du är klar:

![Skapa en säkerhetsgrupp för nätverk](./media/nsg-quickstart-portal/create-nsg.png)

Välj en ny Nätverkssäkerhetsgrupp. Välj 'Inkommande säkerhetsregler' och välj sedan den **Lägg till** för att skapa en regel:

![Lägg till en inkommande regel](./media/nsg-quickstart-portal/add-inbound-rule.png)

Om du vill skapa en regel tillåter som trafik:

- Välj den **grundläggande** knappen. Som standard den **Avancerat** fönstret innehåller några ytterligare konfigurationsalternativ såsom för att definiera en specifik käll-IP-block eller port adressintervall.
- Välj en gemensam **Service** i den nedrullningsbara menyn som *HTTP*. Du kan också välja *anpassad* att tillhandahålla en specifik port som ska användas. 
- Om du vill ändra prioritet eller namn. Prioriteten påverkar den ordning som regler - Ju lägre det numeriska värdet, den tidigare regeln tillämpas.
- När du är klar väljer du **OK** att skapa regeln:

![Skapa en regel för inkommande trafik](./media/nsg-quickstart-portal/create-inbound-rule.png)

Det sista steget är att associera säkerhetsgrupp för nätverk med ett undernät eller ett visst nätverksgränssnitt. Vi koppla Nätverkssäkerhetsgruppen till ett undernät. Välj **undernät**, Välj **associera**:

![Associera en Nätverkssäkerhetsgrupp med ett undernät](./media/nsg-quickstart-portal/associate-subnet.png)

Välj det virtuella nätverket och väljer sedan rätt undernät:

![Associera en Nätverkssäkerhetsgrupp med virtuella nätverk](./media/nsg-quickstart-portal/select-vnet-subnet.png)

Nu har du skapat en Nätverkssäkerhetsgrupp skapas en regel för inkommande trafik som tillåter trafik på port 80 och som är associerade med ett undernät. Alla virtuella datorer som du ansluter till det undernätet kan nås på port 80.

## <a name="more-information-on-network-security-groups"></a>Mer information om Nätverkssäkerhetsgrupper
Snabb kommandon här kan du komma igång med trafik som flödar till den virtuella datorn. Nätverkssäkerhetsgrupper ger många bra funktioner och granularitet för att styra åtkomst till resurser. Du kan läsa mer om [skapar en säkerhetsgrupp för nätverk och ACL-regler här](../../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Du bör placera dina virtuella datorer bakom en belastningsutjämnare i Azure för hög tillgänglighet webbprogram. Belastningsutjämnaren distribuerar trafik till virtuella datorer med en Nätverkssäkerhetsgrupp som tillhandahåller trafikfiltrering. Mer information finns i [hur du läser in balansera Linux-datorer i Azure för att skapa ett program med hög tillgänglighet](tutorial-load-balancer.md).

## <a name="next-steps"></a>Nästa steg
I det här exemplet skapas en enkel regel för att tillåta HTTP-trafik. Du kan hitta information om hur du skapar mer detaljerad miljöer i följande artiklar:

* [Översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Vad är en nätverkssäkerhetsgrupp (NSG)?](../../virtual-network/virtual-networks-nsg.md)