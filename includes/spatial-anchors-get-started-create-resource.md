---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 32f4545a45eda8acddd7c93cc4917dbadca9ad4d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681369"
---
## <a name="create-a-spatial-anchors-resource"></a>Skapa en Spatial Anchors-resurs

Gå till <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>.

I det vänstra navigeringsfönstret i Azure-portalen, väljer **skapa en resurs**.

Använd sökrutan för att söka efter **Spatial ankare**.

   ![Söka efter Spatial Anchors](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Välj **Spatial ankare**. I dialogrutan Välj **skapa**.

I den **Spatial ankare konto** dialogrutan:

- Ange ett unikt resursnamn, med hjälp av vanliga alfanumeriska tecken.
- Välj den prenumeration som du vill koppla till resursen.
- Skapa en resursgrupp genom att välja **Skapa ny**. Ge den namnet **myResourceGroup** och välj **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Välj en plats (region) där du vill placera resursen.
- Välj **Ny** för att börja skapa resursen.

   ![Skapa en resurs](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

När resursen har skapats visar Azure-portalen att distributionen har slutförts. Klicka på **Gå till resurs**.

![Distributionen är klar](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Du kan sedan visa egenskaper för resursen. Kopiera resursens **konto-ID** värde i en textredigerare, eftersom du behöver senare.

   ![Resursegenskaper](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Under **inställningar**väljer **nyckeln**. Kopiera den **primärnyckel** värde i en textredigerare. Det här värdet är den `Account Key`. Du behöver det senare.

   ![Kontonyckel](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
