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
ms.openlocfilehash: d94ee3145fb073dae982019fd4096cc2ceb7cd86
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578339"
---
# <a name="connect-to-synapse-studio-workspace-resources-from-a-restricted-network"></a>Ansluta till resurser i Synapse Studio-arbetsytan från ett begränsat nätverk

Den här artikelns mål läsare är den företags IT-administratör som hanterar företagets begränsade nätverk. IT-administratören är på väg att aktivera nätverks anslutningen mellan Azure Synapse Studio och arbets stationen i det här begränsade nätverket.

I den här artikeln får du lära dig hur du ansluter till din Azure Synapse-arbetsyta från en begränsad nätverks miljö. 

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration** : om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Synapse-arbetsyta** : om du inte har någon Synapse Studio skapar du en Synapse-arbetsyta från Azure Synapse Analytics. Namnet på arbets ytan kommer att krävas i följande steg 4.
* **Ett begränsat nätverk** : det begränsade nätverket underhålls av företagets IT-administratör. IT-administratören har behörighet att konfigurera nätverks principen. Det virtuella nätverks namnet och dess undernät kommer att behövas i följande steg 3.


## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>Steg 1: Lägg till nätverks utgående säkerhets regler i det begränsade nätverket

Du måste lägga till fyra säkerhets regler för utgående trafik med fyra service märken. Läs mer om [service tag-översikt](/azure/virtual-network/service-tags-overview) 
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

## <a name="step-3-create-private-endpoint-for-synapse-studio-gateway"></a>Steg 3: skapa en privat slut punkt för Synapse Studio-Gateway

För att få åtkomst till Synapse Studio-gatewayen måste du skapa en privat slut punkt från Azure Portal. Sök efter " **privat länk** " via Azure Portal. Välj " **skapa privat slut punkt** " i " **privat länk Center** " och fyll sedan i det nödvändiga fältet och skapa det. 

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

## <a name="step-4-create-private-endpoints-for-synapse-studio-workspace-resource"></a>Steg 4: skapa privata slut punkter för Synapse Studio-arbetsytans resurs

För att få åtkomst till resurserna inuti din resurs för Synapse Studio-arbetsytan måste du skapa minst en privat länk-slutpunkt med " **dev** "-typen " **mål under resurs** " och två andra valfria privata länk slut punkter med typerna " **SQL** " eller " **SqlOnDemand** " beroende på vilka resurser i Synapse Studio-arbetsytan som du vill ha åtkomst till. Den här slut punkten för att skapa en privat länk för Synapse Studio-arbetsytan liknar att skapa en slut punkt.  

Observera följande områden på fliken " **resurs** ":
* Välj " **Microsoft. Synapse/arbetsytes** " till " **resurs typ** ".
* Välj " **YourWorkSpaceName** " till " **resurs** " som du har skapat tidigare.
* Välj typ av slut punkt i " **mål under resurs** ":
  * **SQL** : gäller SQL-FRÅGEKÖRNINGEN i SQL-poolen.
  * **SqlOnDemand** : är för inbyggd SQL-fråga.
  * **Dev** : är till för att få åtkomst till allt annat i Synapse Studio-arbetsytor. Du måste skapa minst den privata länk slut punkten för med den här typen.

![Skapar privat slut punkt för Synapse Studio-arbetsytan](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)


## <a name="step-5-create-private-endpoints-for-synapse-studio-workspace-linked-storage"></a>Steg 5: skapa privata slut punkter för den länkade lagringen i Synapse Studio-arbetsytan

För att få åtkomst till den länkade lagrings platsen med lagrings Utforskaren i Synapse Studio-arbetsytan måste du skapa en privat slut punkt med liknande steg i steg 3. 

Observera följande områden på fliken " **resurs** ":
* Välj " **Microsoft. Synapse/storageAccounts** " som " **resurs typ** ".
* Välj " **YourWorkSpaceName** " till " **resurs** " som du har skapat tidigare.
* Välj typ av slut punkt i " **mål under resurs** ":
  * **BLOB** : är för Azure Blob Storage.
  * **DFS** : är för Azure Data Lake Storage Gen2.

![Skapar privat slut punkt för länkad lagring i Synapse Studio-arbetsytan](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-storage.png)

Nu kan du komma åt den länkade lagrings resursen från Storage Explorer på din Synapse Studio-arbetsyta i vNet.

Om din arbets yta har " **Aktivera hanterat virtuellt nätverk** " när arbets ytan skapas enligt nedan,

![Skapar privat slut punkt för länkad lagring i Synapse Studio-arbetsytan 1](./media/how-to-connect-to-workspace-from-restricted-network/ws-network-config.png)

Och du vill att din bärbara dator ska ha åtkomst till de länkade lagrings resurserna under ett visst lagrings konto, måste du lägga till en " **hanterad privat slut punkt** " under Synapse Studio. " **Lagrings konto namnet** " ska vara det som din bärbara dator behöver åtkomst till. Lär dig mer om hur du [skapar en hanterad privat slut punkt för data källan](./how-to-create-managed-private-endpoints.md).

När den här slut punkten har skapats blir " **godkännande tillstånd** " " **väntar** ", du måste begära att det här lagrings kontots ägare ska godkänna det på fliken " **anslutningar för privata slut punkter** " i det här lagrings kontot i Azure Portal. När den har godkänts kommer din bärbara dator kunna komma åt de länkade lagrings resurserna under det här lagrings kontot.

Nu är all uppsättning. Du kan komma åt din resurs för Synapse Studio-arbetsytan.

## <a name="next-steps"></a>Nästa steg

Läs mer om [hanterad arbets yta Virtual Network](./synapse-workspace-managed-vnet.md)

Läs mer om [hanterade privata slut punkter](./synapse-workspace-managed-private-endpoints.md)
