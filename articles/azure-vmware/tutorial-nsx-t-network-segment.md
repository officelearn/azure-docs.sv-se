---
title: 'Självstudie: skapa ett NSX-T-nätverks segment i Azure VMware-lösning (AVS)'
description: I den här självstudien skapade du de NSX-T-datasegment som används för virtuella datorer i vCenter
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: 5654fbb6a063d4dfeb541c20407f9a09dff1509f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101906"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution-avs"></a>Självstudie: skapa ett NSX-T-nätverks segment i Azure VMware-lösning (AVS)

Nätverks segment som skapats i NSX-T-hanteraren används som nätverk för virtuella datorer (VM: ar) i vCenter. De virtuella datorer som har skapats i vCenter placeras på de nätverks segment som skapats i NSX-T och visas i vCenter.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Navigera i NSX-T-hanteraren för att lägga till nätverks segment
> * Lägg till ett nytt nätverks segment
> * Observera det nya nätverks segmentet i vCenter

## <a name="prerequisites"></a>Förutsättningar

Ett privat moln moln med åtkomst till vCenter-och NSX-T Manager-hanterings gränssnitt krävs för att slutföra den här kursen. Se [självstudien: Konfigurera nätverk för ditt privata VMware-moln i Azure](tutorial-configure-networking.md).

## <a name="provision-a-network-segment-in-nsx-t"></a>Etablera ett nätverks segment i NSX-T

1. I vCenter för ditt privata moln väljer du **SDDC-datacenter > nätverk** och Observera att det inte finns några nätverk än.

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="Välj SDDC-datacenter > nätverk":::

1. I NSX-T Manager för ditt privata moln väljer du **nätverk**.

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="Välj nätverk för att navigera till vyn nätverks översikt för NSX-T-hanterare.":::

1. Välj **segment**.

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="Välj segment på sidan Nätverks översikt.":::

1. På sidan Översikt över NSX-T-segment väljer du **Lägg till segment**. Tre segment skapas som en del av den privata moln etableringen och kan inte användas för virtuella datorer.  Du måste lägga till ett nytt nätverks segment för det här ändamålet.

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="Välj Lägg till segment från översikts sidan för nätverks segment.":::

1. Namnge segmentet, Välj den förkonfigurerade 1-gatewayen (TNTxx-T1) som **ansluten Gateway**, lämna **typen** som flexibel, Välj den förkonfigurerade överliggande **transport zonen** (TNTxx-överlägg-TZ) och välj sedan ange undernät. Alla andra inställningar i det här avsnittet och **port** -och **segment profilerna** kan finnas kvar i standard, som är konfigurationen.

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="Ange segment namn, ansluten gateway och typ och transport zon och välj sedan "Ange undernät".":::

1. Ange IP-adressen för gatewayen för det nya segmentet och välj sedan **Lägg till**. Den IP-adress som du använder måste finnas i ett icke överlappande RFC1918-Adressblock, vilket garanterar att du kan ansluta till de virtuella datorerna i det nya segmentet.

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="Ange segment gatewayens IP-adress i CIDR-notation och välj Lägg till.":::

1. Använd det nya nätverks segmentet genom att välja **tillämpa** och spara konfigurationen med **Spara**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="Tillämpa det nya nätverks segmentet på NSX-T-konfigurationen med APPLY.":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="Spara det nya nätverks segmentet i NSX-T-konfigurationen med SAVE.":::

1. Det nya nätverks segmentet har nu skapats och du kommer att neka alternativet att fortsätta konfigurera segmentet genom att välja **Nej**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="Neka att ytterligare konfigurera det nyligen skapade nätverks segmentet genom att välja Nej.":::

1. Bekräfta att det nya nätverks segmentet finns i NSX-T genom att välja **nätverk > segment** och se det nya segmentet i listan (i det här fallet "ls01").

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Bekräfta att det nya nätverks segmentet finns i NSX-T.":::

1. Bekräfta att det nya nätverks segmentet finns i vCenter genom att välja **nätverks > SDDC-datacenter** och att Observera att det nya segmentet finns i listan (i det här fallet "ls01").

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="Bekräfta att det nya nätverks segmentet finns i vCenter.":::

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du de NSX-T-datasegment som används för virtuella datorer i vCenter. Nu kan du använda [självstudien: skapa ett innehålls bibliotek för att distribuera virtuella datorer i Azure VMware-lösningen (AVS)](tutorial-deploy-vm-content-library.md) för att skapa ett innehålls bibliotek i vCenter och tillhandahålla en virtuell dator i det nätverk som du skapade i den här självstudien.

<!-- LINKS - external-->

<!-- LINKS - internal -->
