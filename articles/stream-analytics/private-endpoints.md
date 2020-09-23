---
title: Skapa och ta bort privata slut punkter i ett Azure Stream Analytics kluster
description: Lär dig hur du hanterar privata slut punkter i ett Azure Stream Analytics kluster.
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: baf80e3d543bee455dd8dfa5bc09bc5bf43c3453
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947878"
---
# <a name="create-and-delete-private-endpoints-in-an-azure-stream-analytics-cluster"></a>Skapa och ta bort privata slut punkter i ett Azure Stream Analytics kluster

Du kan ansluta dina Azure Stream Analytics jobb som körs på ett kluster för att mata in och mata ut resurser som ligger bakom en brand vägg eller en Azure-Virtual Network (VNet). Först skapar du en privat slut punkt för en resurs, till exempel Azure Event Hub eller Azure SQL Database, i Stream Analytics klustret. Godkänn sedan den privata slut punkts anslutningen från dina indata eller utdata.

När du godkänner anslutningen har alla jobb som körs i ditt Stream Analytics-kluster åtkomst till resursen via den privata slut punkten. Den här artikeln visar hur du skapar och tar bort privata slut punkter i ett Stream Analytics kluster.

## <a name="create-private-endpoint-in-stream-analytics-cluster"></a>Skapa privat slut punkt i Stream Analytics kluster

I det här avsnittet får du lära dig hur du skapar en privat slut punkt i ett Stream Analytics kluster.

1. Leta upp och välj ditt Stream Analytics-kluster i Azure Portal.

1. Under **Inställningar**väljer du **privata slut punkter**.

1. Välj **Lägg till privat slut punkt** och ange följande information för att välja den resurs som du vill komma åt säkert via en privat slut punkt.

   |Inställningen|Värde|
   |---|---|
   |Namn|Ange ett namn för din privata slut punkt. Om det här namnet tas skapar du ett unikt namn.|
   |Anslutningsmetod|Välj **Anslut till en Azure-resurs i min katalog**.<br><br>Du kan välja en av dina resurser för att på ett säkert sätt ansluta till med hjälp av den privata slut punkten, eller så kan du ansluta till någon annans resurs genom att använda ett resurs-ID eller alias som de har delat med dig.|
   |Prenumeration|Välj din prenumeration.|
   |Resurstyp|Välj den [resurs typ som mappar till din resurs](../private-link/private-endpoint-overview.md#private-link-resource).|
   |Resurs|Välj den resurs som du vill ansluta till med hjälp av privat slut punkt.|
   |Mål under resurs|Typ av under resurs för den resurs som väljs ovan som din privata slut punkt kommer att ha åtkomst till.|

   ![Skapa upplevelse för privat slut punkt](./media/private-endpoints/create-private-endpoint.png)

1. Godkänn anslutningen från mål resursen. Om du till exempel har skapat en privat slut punkt till en Azure SQL Database instans i föregående steg, bör du gå till den här SQL Database instansen och se en väntande anslutning som ska godkännas. Det kan ta några minuter innan anslutningsbegäran visas.

    ![Godkänn privat slut punkt](./media/private-endpoints/approve-private-endpoint.png)

1. Du kan gå tillbaka till ditt Stream Analytics-kluster för att se tillstånds ändringen från **väntande kund godkännande** till en **väntande DNS-installation** som **slutförs** på några minuter.

## <a name="delete-a-private-endpoint-in-a-stream-analytics-cluster"></a>Ta bort en privat slut punkt i ett Stream Analytics kluster

1. Leta upp och välj ditt Stream Analytics-kluster i Azure Portal.

1. Under **Inställningar**väljer du **privata slut punkter**.

1. Välj den privata slut punkt som du vill ta bort och välj **ta bort**.

   ![ta bort privat slut punkt](./media/private-endpoints/delete-private-endpoint.png)

## <a name="next-steps"></a>Nästa steg

Nu har du en översikt över hur du hanterar privata slut punkter i ett Azure Stream Analytics kluster. Sedan kan du lära dig hur du skalar kluster och Kör jobb i klustret:

* [Skala ett Azure Stream Analytics kluster](scale-cluster.md)
* [Hantera Stream Analytics jobb i ett Stream Analytics kluster](manage-jobs-cluster.md)