---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: cee446e54a37ce47fc82d72724e831b8270f3769
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57908492"
---
## <a name="create-a-spatial-anchors-resource"></a>Skapa en Spatial Anchors-resurs

1. Navigera till <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>.

2. Från den vänstra menyn i Azure-portalen väljer du **Skapa en resurs**.

3. Sök efter ”Spatial Anchors” i sökfältet.

   ![Söka efter Spatial Anchors](./media/spatial-anchors-get-started-create-resource/portal-search.png)

4. Välj **Spatial ankare** att öppna en dialogruta och välja **skapa**.

5. I formuläret **Spatial Anchors Account** (Spatial Anchors-konto):

   1. Ange ett unikt resursnamn.
   2. Välj den prenumeration som resurs ska anslutas till.
   3. Skapa en resursgrupp genom att välja **Skapa ny** och ge resursgruppen namnet **myResourceGroup** och välj **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
   4. Välj en plats (region) där resursen ska placeras.
   5. Välj **Ny** för att börja skapa resursen.

   ![Skapa en resurs](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

6. När resursen har skapats kan resursegenskaperna visas. Kopiera resursens **konto-ID** värde i en textredigerare, eftersom den nödvändiga senare.

   ![Visa resursegenskaper](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

7. Under **inställningar**väljer **nyckel**, och kopierar till en textredigerare den **primärnyckel** värde. Det här värdet är `Account Key` och kommer att användas senare.

   ![Visa kontonyckel](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
