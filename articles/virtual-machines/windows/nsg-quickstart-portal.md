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
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: 2820dcabf042d7463f9776b42f277a0457caf3b6
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929023"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Hur du öppnar portar till en virtuell dator med Azure portal
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Snabbkommandon
Du kan också [utföra dessa steg med Azure PowerShell](nsg-quickstart-powershell.md).

Börja med att skapa Nätverkssäkerhetsgruppen. Välj en resursgrupp i portalen, väljer **Lägg till**, Sök sedan efter och välj **nätverkssäkerhetsgrupp**:

![Lägg till en Nätverkssäkerhetsgrupp](./media/nsg-quickstart-portal/add-nsg.png)

Ange ett namn för din grupp, Välj eller skapa en resursgrupp och välj en plats. Välj **skapa** när du är klar:

![Skapa en Nätverkssäkerhetsgrupp](./media/nsg-quickstart-portal/create-nsg.png)

Välj nya Nätverkssäkerhetsgruppen. Välj ”ingående säkerhetsregler” och välj sedan den **Lägg till** för att skapa en regel:

![Lägg till en inkommande regel](./media/nsg-quickstart-portal/add-inbound-rule.png)

Om du vill skapa en regel tillåter som trafik:

- Välj den **grundläggande** knappen. Som standard den **Avancerat** fönstret innehåller några ytterligare konfigurationsalternativ som du definierar en viss källa IP block eller port adressintervall.
- Välj ett vanligt **Service** från den nedrullningsbara menyn som *HTTP*. Du kan också välja *anpassad* att tillhandahålla en specifik port som ska användas. 
- Om du vill kan du ändra prioritet eller namn. Prioriteten påverkar den ordning som regler tillämpas - Ju lägre det numeriska värdet, den tidigare regeln tillämpas.
- När du är klar väljer du **OK** att skapa regeln:

![Skapa en regel för inkommande trafik](./media/nsg-quickstart-portal/create-inbound-rule.png)

Det sista steget är att associera Nätverkssäkerhetsgruppen med ett undernät eller ett visst nätverksgränssnitt. Nu ska vi associera Nätverkssäkerhetsgruppen med ett undernät. Välj **undernät**, välj sedan **associera**:

![Associera en Nätverkssäkerhetsgrupp med ett undernät](./media/nsg-quickstart-portal/associate-subnet.png)

Välj det virtuella nätverket och välj sedan lämpligt undernät:

![Associera en Nätverkssäkerhetsgrupp med virtuella nätverk](./media/nsg-quickstart-portal/select-vnet-subnet.png)

Nu har du skapat en Nätverkssäkerhetsgrupp skapas en inkommande regel som tillåter trafik på port 80 och associerat den med ett undernät. Alla virtuella datorer som du ansluter till det undernätet kan nås på port 80.

## <a name="more-information-on-network-security-groups"></a>Mer information om Nätverkssäkerhetsgrupper
Snabbkommandon här kan du komma igång med trafiken som flödar till den virtuella datorn. Nätverkssäkerhetsgrupper ger många bra funktioner och kornighet för att styra åtkomst till resurser. Du kan läsa mer om [skapar en Nätverkssäkerhetsgrupp och ACL-regler här](../../virtual-network/tutorial-filter-network-traffic.md).

För webbprogram med hög tillgänglighet, bör du placera dina virtuella datorer bakom en belastningsutjämnare för Azure. Belastningsutjämnaren distribuerar trafik till virtuella datorer med en Nätverkssäkerhetsgrupp som tillhandahåller trafikfiltrering. Mer information finns i [läsa in belastningsutjämna Linux-datorer i Azure för att skapa ett program med hög tillgänglighet](tutorial-load-balancer.md).

## <a name="next-steps"></a>Nästa steg
I det här exemplet skapade du en enkel regel för att tillåta HTTP-trafik. Du hittar information om hur du skapar mer detaljerad miljöer i följande artiklar:

* [Översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Vad är en nätverkssäkerhetsgrupp?](../../virtual-network/security-overview.md)