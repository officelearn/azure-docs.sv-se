---
title: Anslut till resursen för Synapse Studio-arbetsytan från ett begränsat nätverk
description: I den här artikeln får du lära dig hur du ansluter till dina resurser i Azure Synapse Studio-arbetsytan från ett begränsat nätverk
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: 5d28b8f2ff3045c9fdf5e8a866419a22bfbc6504
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321821"
---
# <a name="connect-to-synapse-studio-workspace-resources-from-a-restricted-network"></a>Ansluta till resurser i Synapse Studio-arbetsytan från ett begränsat nätverk

Den här artikelns mål läsare är den företags IT-administratör som hanterar företagets begränsade nätverk. IT-administratören är på väg att aktivera nätverks anslutningen mellan Azure Synapse Studio och arbets stationen i det här begränsade nätverket.

I den här artikeln får du lära dig hur du ansluter till din Azure Synapse-arbetsyta från en begränsad nätverks miljö. 

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration** : om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Synapse-arbetsyta** : om du inte har någon Synapse Studio skapar du en Synapse-arbetsyta från Azure Synapse Analytics. Namnet på arbets ytan kommer att krävas i följande steg 4.
* **Ett begränsat nätverk** : det begränsade nätverket underhålls av företagets IT-administratör. IT-administratören har behörighet att konfigurera nätverks principen. Det virtuella nätverks namnet och dess undernät kommer att behövas i följande steg 3.



## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>Steg 1: Lägg till nätverks utgående säkerhets regler i det begränsade nätverket

Du måste lägga till fyra säkerhets regler för utgående trafik med fyra service märken. Läs mer om [service tag-översikt](/azure/virtual-network/service-tags-overview.md) 
* AzureResourceManager
* AzureFrontDoor. frontend
* AzureActiveDirectory
* AzureMonitor (valfritt. Lägg endast till den här typen av regel när du vill dela data till Microsoft.)

**Azure Resource Manager** utgående regel Detaljer enligt nedan. När du skapar de andra tre reglerna ersätter du värdet för " **mål service tag** " med att välja service tag-namnet " **AzureFrontDoor. frontend** ", " **AzureActiveDirectory** ", " **AzureMonitor** " från List rutan.

![AzureResourceManager](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)


## <a name="step-2-create-azure-synapse-analytics-private-link-hubs"></a>Steg 2: Skapa Azure Synapse Analytics (privata länk hubbar)

Du måste skapa en Azure Synapse Analytics-tjänst (Private Link Hub) från Azure Portal. Sök efter " **Azure Synapse Analytics (Private Link Hub)** " via Azure Portal och fyll sedan i det nödvändiga fältet och skapa det. 

> [!Note]
> Regionen måste vara samma som den där din Synapse-arbetsyta är.

![Skapa privata länk hubbar för Synapse Analytics](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-private-link-endpoint-for-synapse-studio-gateway"></a>Steg 3: skapa en privat länk-slutpunkt för Synapse Studio-Gateway

För att få åtkomst till Synapse Studio-gatewayen måste du skapa en privat länk slut punkt från Azure Portal. Sök efter " **privat länk** " via Azure Portal. Välj " **skapa privat slut punkt** " i " **privat länk Center** " och fyll sedan i det nödvändiga fältet och skapa det. 

> [!Note]
> Regionen måste vara samma som den där din Synapse-arbetsyta är.

![Skapar privat slut punkt för Synapse Studio 1](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

På nästa flik i " **resurs** " väljer du den privata länk hubben som skapades i steg 2 ovan.

![Skapar privat slut punkt för Synapse Studio 2](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

På nästa flik i " **konfiguration** ", 
* Välj det begränsade virtuella nätverks namn du har för " **virtuellt nätverk** ".
* Välj under nätet för det begränsade virtuella nätverket för " **undernät** ". 
* Välj " **Ja** " om du vill **integrera med privat DNS-zon**.

![Skapar privat slut punkt för Synapse Studio 3](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

När du har skapat den privata länk slut punkten kan du komma åt inloggnings sidan för Synapse Studio-webbverktyget. Men du kommer inte att kunna komma åt resurserna inuti din Synapse-arbetsyta förrän du måste slutföra nästa steg.

## <a name="step-4-create-private-link-endpoints-for-synapse-studio-workspace-resource"></a>Steg 4: skapa privata länk slut punkter för Synapse Studio-arbetsytans resurs

För att få åtkomst till resurserna inuti din resurs för Synapse Studio-arbetsytan måste du skapa minst en privat länk-slutpunkt med " **dev** "-typen " **mål under resurs** " och två andra valfria privata länk slut punkter med typerna " **SQL** " eller " **SqlOnDemand** " beroende på vilka resurser i Synapse Studio-arbetsytan som du vill ha åtkomst till. Den här slut punkten för att skapa en privat länk för Synapse Studio-arbetsytan liknar att skapa en slut punkt.  

Observera följande områden på fliken " **resurs** ":
* Välj " **Microsoft. Synapse/arbetsytes** " till " **resurs typ** ".
* Välj " **YourWorkSpaceName** " till " **resurs** " som du har skapat tidigare.
* Välj typ av slut punkt i " **mål under resurs** ":
  * **SQL** : gäller SQL-FRÅGEKÖRNINGEN i SQL-poolen.
  * **SqlOnDemand** : är för inbyggd SQL-fråga.
  * **Dev** : är till för att få åtkomst till allt annat i Synapse Studio-arbetsytor. Du måste skapa minst den privata länk slut punkten för med den här typen.

![Skapar privat slut punkt för Synapse Studio-arbetsytan](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)

Nu är all uppsättning. Du kan komma åt din resurs för Synapse Studio-arbetsytan.

## <a name="next-steps"></a>Nästa steg

Läs mer om [hanterad arbets yta Virtual Network](./synapse-workspace-managed-vnet.md)

Läs mer om [hanterade privata slut punkter](./synapse-workspace-managed-private-endpoints.md)
