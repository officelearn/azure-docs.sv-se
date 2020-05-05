---
title: 'Självstudie: skala ett privat moln'
description: I den här självstudien använder du Azure Portal för att skala en Azure VMware-lösning (AVS) för hands version av ett privat moln.
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 70d8c1c555badd6102f4b2547492bdea54e55783
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740292"
---
# <a name="tutorial-scale-an-azure-vmware-solution-avs-preview-private-cloud"></a>Självstudie: skala en Azure VMware-lösning (AVS) för hands version av ett privat moln

För att få ut mesta möjliga av din AVS Preview-upplevelse av ditt privata moln kan du skala klustren och värdarna så att de återspeglar de planerade arbets belastningarna. Eftersom AVS inte stöder din lokala vCenter under för hands versionen måste du använda det du redan har skapat via Azure Portal.

Du kan skala antalet kluster och antalet värdar i ett privat moln som krävs för din program arbets belastning. Begränsningar för prestanda och tillgänglighet för vissa tjänster måste åtgärdas på ett fall från fall till fall i moln miljön för för hands versionen. Klustret och värd gränserna i ett privat moln finns i artikeln om [begrepp för privat moln](concepts-private-clouds-clusters.md).

I den här självstudien använder du Azure Portal för att:

> [!div class="checklist"]
> * Lägga till ett kluster i ett befintligt privat moln
> * Lägg till värdar i ett befintligt kluster

## <a name="prerequisites"></a>Krav

Du behöver ett privat moln för att slutföra den här kursen. Om du ännu inte har skapat ett privat moln använder du [själv studie kursen skapa ett privat moln](tutorial-create-private-cloud.md) för att skapa ett privat moln och konfigurera nätverk för ditt privata VMware-moln i Azure för att konfigurera det virtuella nätverket som krävs.

## <a name="add-a-new-cluster"></a>Lägg till ett nytt kluster

På sidan Översikt i ett befintligt privat moln väljer du **skala privat moln**under **Hantera**. Välj sedan **+ Lägg till ett kluster**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Välj Lägg till ett kluster" border="true":::

På sidan **Lägg till kluster** använder du skjutreglaget för att välja antalet värdar. Välj **Spara**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Konfigurera ett nytt privat moln kluster" border="true":::

Distributionen av det nya klustret kommer att börja.

## <a name="scale-a-cluster"></a>Skala ett kluster 

På sidan Översikt i ett befintligt privat moln väljer du **skala privat moln** och väljer Penn ikonen för att redigera klustret.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Välj skala privat moln i Översikt" border="true":::

På sidan **Redigera kluster** använder du skjutreglaget för att välja antalet värdar. Välj **Spara**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Konfigurera ett nytt privat moln kluster" border="true":::

Tillägg av värdar till klustret börjar.

## <a name="next-steps"></a>Nästa steg

Om du behöver ett annat moln privat moln kan du [skapa ett annat privat moln](tutorial-create-private-cloud.md), efter samma nätverks krav, kluster och värd gränser...

<!-- LINKS - external-->

<!-- LINKS - internal -->
