---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: dc8d3c2d400204f53b05bb5536af95679541f3f6
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305224"
---
## <a name="create-a-spatial-anchors-resource"></a>Skapa en Spatial Anchors-resurs

Gå till <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>.

I det vänstra navigeringsfönstret i Azure-portalen, väljer **skapa en resurs**.

Använd sökrutan för att söka efter **Spatial ankare**.

   ![Söka efter Spatial Anchors](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Välj **Spatial ankare**. I dialogrutan Välj **skapa**.

I den **Spatial ankare konto** dialogrutan:

- Ange ett unikt resursnamn.
- Välj den prenumeration som du vill koppla till resursen.
- Skapa en resursgrupp genom att välja **Skapa ny**. Ge den namnet **myResourceGroup** och välj **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Välj en plats (region) där du vill placera resursen.
- Välj **Ny** för att börja skapa resursen.

   ![Skapa en resurs](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

När resursen har skapats kan visa du egenskaper för resursen. Kopiera resursens **konto-ID** värde i en textredigerare, eftersom du behöver senare.

   ![Resursegenskaper](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Under **inställningar**väljer **nyckeln**. Kopiera den **primärnyckel** värde i en textredigerare. Det här värdet är den `Account Key`. Du behöver det senare.

   ![Kontonyckel](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
