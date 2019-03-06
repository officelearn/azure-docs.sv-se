---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 9e232c5a8f1c2e38c04ea2236691554952f443e8
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752949"
---
## <a name="create-a-spatial-anchors-resource"></a>Skapa en Spatial Anchors-resurs

1. Navigera till <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>.

2. Från den vänstra menyn i Azure-portalen väljer du **Skapa en resurs**.

3. Sök efter ”Spatial Anchors” i sökfältet.

   ![Söka efter Spatial Anchors](./media/spatial-anchors-get-started-create-resource/portal-search.png)

4. Välj **Spatial Anchors (preview)** (förhandsversion) för att öppna en dialogruta, och välj **Skapa**.

5. I formuläret **Spatial Anchors Account** (Spatial Anchors-konto):

   1. Ange ett unikt resursnamn.
   2. Välj den prenumeration som resurs ska anslutas till.
   3. Skapa en resursgrupp genom att välja **Skapa ny** och ge resursgruppen namnet **myResourceGroup** och välj **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
   4. Välj en plats (region) där resursen ska placeras.
   5. Välj **Ny** för att börja skapa resursen.

   ![Skapa en resurs](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

6. När resursen har skapats kan resursegenskaperna visas. Anteckna värdet för resursens **konto-ID** eftersom det behövs senare.

   ![Visa resursegenskaper](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

7. Under **Inställningar** väljer du **Nyckel** och antecknar värdet för **Primärnyckel**. Det här värdet är `Account Key` och kommer att användas senare.

   ![Visa kontonyckel](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
