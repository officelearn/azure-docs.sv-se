---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 32f4545a45eda8acddd7c93cc4917dbadca9ad4d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "67134993"
---
## <a name="create-a-spatial-anchors-resource"></a>Skapa en Spatial Anchors-resurs

Gå till <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>.

Välj **Skapa en resurs**i det vänstra navigeringsfönstret i Azure-portalen .

Använd sökrutan för att söka efter **spatiala ankare**.

   ![Söka efter Spatial Anchors](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Välj **Rumsliga fästpunkter**. Välj **Skapa**i dialogrutan .

I dialogrutan **Konto för rumsliga fästpunkter:**

- Ange ett unikt resursnamn med vanliga alfanumeriska tecken.
- Välj den prenumeration som du vill koppla resursen till.
- Skapa en resursgrupp genom att välja **Skapa ny**. Namnge den **myResourceGroup** och välj **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Välj en plats (region) där resursen ska placeras.
- Välj **Ny** för att börja skapa resursen.

   ![Skapa en resurs](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

När resursen har skapats visar Azure Portal att distributionen är klar. Klicka på **Gå till resurs**.

![Distributionen är klar](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Sedan kan du visa resursegenskaperna. Kopiera resursens **konto-ID-värde** till en textredigerare eftersom du behöver det senare.

   ![Resursegenskaper](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Under **Inställningar**väljer du **Nyckel**. Kopiera **primärnyckelvärdet** till en textredigerare. Det här `Account Key`värdet är . Du behöver det senare.

   ![Kontonyckel](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
