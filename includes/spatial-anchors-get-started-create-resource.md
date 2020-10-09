---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: b2b3ca886359a0b4c906b89ed76f57486fc2c368
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "83638999"
---
## <a name="create-a-spatial-anchors-resource"></a>Skapa en Spatial Anchors-resurs

Gå till <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>.

I det vänstra navigerings fönstret i Azure Portal väljer du **skapa en resurs**.

Använd sökrutan för att söka efter **avstånds ankare**.

   ![Söka efter Spatial Anchors](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Välj **spatiala ankare**. I dialog rutan väljer du **skapa**.

I dialog rutan **spatiala ankare** :

- Ange ett unikt resurs namn med vanliga alfanumeriska tecken.
- Välj den prenumeration som du vill koppla resursen till.
- Skapa en resurs grupp genom att välja **Skapa ny**. Ge den namnet **myResourceGroup** och välj **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Välj en plats (region) där resursen ska placeras.
- Välj **Ny** för att börja skapa resursen.

   ![Skapa en resurs](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

När resursen har skapats visar Azure Portal att distributionen är klar. Klicka på **Gå till resurs**.

![Distributionen är klar](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Sedan kan du Visa resurs egenskaperna. Kopiera resursens **konto-ID-** värde till en text redigerare eftersom du behöver det senare.

   ![Resurs egenskaper](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Kopiera också resursens **konto** värde till en text redigerare eftersom du behöver det senare.

   ![Konto domän](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

Under **Inställningar**väljer du **nyckel**. Kopiera värdet för **primär nyckel** till en text redigerare. Det här värdet är `Account Key` . Du behöver det senare.

   ![Kontonyckel](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
