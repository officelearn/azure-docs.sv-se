---
title: Självstudie – skala ett privat moln
description: I den här självstudien använder du Azure Portal för att skala ett privat moln i Azure VMware-lösningen.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: e1226eb98607a34869bda1f998c7cecea2e50919
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91254422"
---
# <a name="tutorial-scale-an-azure-vmware-solution-private-cloud"></a>Självstudie: skala ett privat moln i Azure VMware-lösningen

För att få ut mesta möjliga av din privata moln lösning i Azure VMware kan du skala klustren och värdarna så att de återspeglar det du behöver för planerade arbets belastningar. Du kan skala antalet kluster och antalet värdar i ett privat moln som krävs för din program arbets belastning. Begränsningar för prestanda och tillgänglighet för vissa tjänster måste åtgärdas på ett fall från fall till fall i din privata moln miljö i Azure VMware-lösningen. Klustret och värd gränserna anges i artikeln om [begrepp för privat moln](concepts-private-clouds-clusters.md) .

I den här självstudien använder du Azure Portal för att:

> [!div class="checklist"]
> * Lägga till ett kluster i ett befintligt privat moln
> * Lägg till värdar i ett befintligt kluster

## <a name="prerequisites"></a>Förutsättningar

Du behöver ett privat moln för att slutföra den här kursen. Om du ännu inte har skapat ett privat moln använder du [själv studie kursen skapa ett privat moln](tutorial-create-private-cloud.md) för att skapa ett nätverk för ditt privata VMware-moln i Azure och konfigurera det virtuella nätverket som krävs.

## <a name="add-a-new-cluster"></a>Lägg till ett nytt kluster

1. På sidan Översikt i ett befintligt privat moln väljer du **skala privat moln**under **Hantera**. Välj sedan **+ Lägg till ett kluster**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Välj Lägg till ett kluster" border="true":::

1. På sidan **Lägg till kluster** använder du skjutreglaget för att välja antalet värdar. Välj **Spara**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="På sidan Lägg till kluster använder du skjutreglaget för att välja antalet värdar. Välj Spara." border="true":::

   Distributionen av det nya klustret kommer att börja.

## <a name="scale-a-cluster"></a>Skala ett kluster 

1. På sidan Översikt i ett befintligt privat moln väljer du **skala privat moln** och väljer Penn ikonen för att redigera klustret.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Välj skala privat moln i Översikt" border="true":::

1. På sidan **Redigera kluster** använder du skjutreglaget för att välja antalet värdar. Välj **Spara**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="På sidan Redigera kluster använder du skjutreglaget för att välja antalet värdar. Välj Spara." border="true":::

   Tillägg av värdar till klustret börjar.

## <a name="next-steps"></a>Nästa steg

Om du behöver ett privat moln i Azure VMware-lösningen [skapar du ett annat privat moln](tutorial-create-private-cloud.md), efter samma nätverks krav, kluster och värd gränser.

<!-- LINKS - external-->

<!-- LINKS - internal -->
