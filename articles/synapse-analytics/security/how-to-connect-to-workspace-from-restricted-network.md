---
title: Ansluta till arbets ytans resurser i Azure Synapse Analytics Studio från ett begränsat nätverk
description: I den här artikeln får du lära dig hur du ansluter till dina arbets ytans resurser från ett begränsat nätverk
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: 7cff2d8245095489fbba3b7af24b416885995e4d
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637140"
---
# <a name="connect-to-workspace-resources-from-a-restricted-network"></a>Ansluta till arbets ytans resurser från ett begränsat nätverk

Anta att du är IT-administratör som hanterar organisationens begränsade nätverk. Du vill aktivera nätverks anslutningen mellan Azure Synapse Analytics Studio och en arbets station inom det här begränsade nätverket. Den här artikeln visar hur du gör.

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration** : om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Synapse Analytics-arbetsyta** : du kan skapa en från Azure Synapse Analytics. Du behöver namnet på arbets ytan i steg 4.
* **Ett begränsat nätverk** : IT-administratören hanterar det begränsade nätverket för organisationen och har behörighet att konfigurera nätverks principen. Du behöver det virtuella nätverks namnet och dess undernät i steg 3.


## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>Steg 1: Lägg till nätverks utgående säkerhets regler i det begränsade nätverket

Du måste lägga till fyra säkerhets regler för utgående trafik med fyra service märken. 
* AzureResourceManager
* AzureFrontDoor. frontend
* AzureActiveDirectory
* AzureMonitor (den här typen av regel är valfri. Lägg bara till den när du vill dela data med Microsoft.)

Följande skärm bild visar information om den Azure Resource Manager utgående regeln.

![Skärm bild av Azure Resource Manager service tag-information.](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)

När du skapar de andra tre reglerna ersätter du värdet för **mål service tag gen** med **AzureFrontDoor. frontend** , **AzureActiveDirectory** eller **AzureMonitor** från listan.

Mer information finns i [Översikt över tjänst etiketter](/azure/virtual-network/service-tags-overview.md).

## <a name="step-2-create-private-link-hubs"></a>Steg 2: skapa privata länk hubbar

Skapa sedan privata länk hubbar från Azure Portal. Du hittar det här i portalen genom att söka efter *Azure Synapse Analytics (privata länk hubbar)* och sedan fylla i den information som krävs för att skapa den. 

> [!Note]
> Se till att **region** svärdet är samma som det där din Azure Synapse Analytics-arbetsyta är.

![Skärm bild av skapa Synapse Private Link Hub.](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-a-private-endpoint-for-your-gateway"></a>Steg 3: skapa en privat slut punkt för din gateway

För att få åtkomst till Azure Synapse Analytics Studio-gatewayen måste du skapa en privat slut punkt från Azure Portal. Sök efter *privat länk* för att hitta det här i portalen. I det **privata länk centret** väljer du **skapa privat slut punkt** och fyller sedan i den information som krävs för att skapa den. 

> [!Note]
> Se till att **region** svärdet är samma som det där din Azure Synapse Analytics-arbetsyta är.

![Skärm bild av fliken Skapa en privat slut punkt, grundläggande information.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

På fliken **resurs** väljer du den privata länk hubben som du skapade i steg 2.

![Skärm bild av fliken Skapa en privat slut punkt, resurs.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

På fliken **konfiguration** : 
* För **virtuellt nätverk** väljer du det begränsade virtuella nätverks namnet.
* För **undernät** väljer du under nätet för det begränsade virtuella nätverket. 
* Välj **Ja** om du vill **integrera med en privat DNS-zon**.

![Skärm bild av fliken Skapa en privat slut punkt, konfiguration.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

När den privata länk slut punkten har skapats kan du komma åt inloggnings sidan i webb verktyget för Azure Synapse Analytics Studio. Men du kan inte komma åt resurserna i din arbets yta ännu. För det måste du slutföra nästa steg.

## <a name="step-4-create-private-endpoints-for-your-workspace-resource"></a>Steg 4: skapa privata slut punkter för arbets ytans resurs

Du måste skapa följande för att få åtkomst till resurserna i Azure Synapse Analytics Studio-arbetsytans resurs:

- Minst en privat länk slut punkt med en **dev** -typ för **mål under resurs**.
- Två andra valfria privata länk slut punkter med typer av **SQL** eller **SqlOnDemand** , beroende på vilka resurser i arbets ytan som du vill få åtkomst till.

Att skapa dessa liknar hur du skapar slut punkten i föregående steg.  

På fliken **resurs** :

* För **resurs typ** väljer du **Microsoft. Synapse/arbetsytes**.
* För **resurs** väljer du namnet på arbets ytan som du skapade tidigare.
* För **mål under resurs** väljer du typ av slut punkt:
  * **SQL** är för SQL-FRÅGEKÖRNINGEN i SQL-poolen.
  * **SqlOnDemand** är för inbyggd SQL-körning av frågor.
  * **Dev** är till för att få åtkomst till allt annat i Azure Synapse Analytics Studio-arbetsytor. Du måste skapa minst en privat länk slut punkt av den här typen.

![Skärm bild av skapa en privat slut punkt, fliken resurs, arbets yta.](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)


## <a name="step-5-create-private-endpoints-for-workspace-linked-storage"></a>Steg 5: skapa privata slut punkter för den länkade lagrings platsen

Du måste skapa en privat slut punkt för att få åtkomst till den länkade lagringen med lagrings Utforskaren i Azure Synapse Analytics Studio-arbetsytan. Stegen för detta liknar dem i steg 3. 

På fliken **resurs** :
* För **resurs typ** väljer du **Microsoft. Synapse/storageAccounts**.
* För **resurs** väljer du det lagrings konto namn som du skapade tidigare.
* För **mål under resurs** väljer du typ av slut punkt:
  * **BLOB** är för Azure Blob Storage.
  * **DFS** är för Azure Data Lake Storage Gen2.

![Skärm bild av skapa en privat slut punkt, fliken resurs, lagring.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-storage.png)

Nu kan du komma åt den länkade lagrings resursen. I ditt virtuella nätverk kan du använda lagrings Utforskaren i Azure Synapse Analytics Studio-arbetsytan för att få åtkomst till den länkade lagrings resursen.

Du kan aktivera ett hanterat virtuellt nätverk för din arbets yta, som visas i den här skärm bilden:

![Skärm bild av alternativet Skapa Synapse-arbetsyta med alternativet Aktivera hanterat virtuellt nätverk markerat.](./media/how-to-connect-to-workspace-from-restricted-network/ws-network-config.png)

Om du vill att din bärbara dator ska ha åtkomst till de länkade lagrings resurserna under ett visst lagrings konto lägger du till hanterade privata slut punkter under Azure Synapse Analytics Studio. Lagrings kontots namn bör vara det som din bärbara dator behöver åtkomst till. Mer information finns i [skapa en hanterad privat slut punkt för din data källa](./how-to-create-managed-private-endpoints.md).

När du har skapat den här slut punkten visar godkännande tillståndet status **väntar**. Begär godkännande från ägaren av det här lagrings kontot, på fliken **anslutningar för privata slut punkter** för det här lagrings kontot i Azure Portal. När den har godkänts kan din bärbara dator komma åt de länkade lagrings resurserna under det här lagrings kontot.

Nu är all uppsättning. Du kan komma åt din resurs för Azure Synapse Analytics Studio-arbetsytan.

## <a name="next-steps"></a>Nästa steg

Läs mer om det [virtuella nätverket för hanterade arbets ytor](./synapse-workspace-managed-vnet.md).

Läs mer om [hanterade privata slut punkter](./synapse-workspace-managed-private-endpoints.md).
