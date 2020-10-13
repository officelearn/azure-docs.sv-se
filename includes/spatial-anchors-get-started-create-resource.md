---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: b83951cd743afc7e249e3ee37182007bbe40cb5c
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971393"
---
## <a name="create-a-spatial-anchors-resource"></a>Skapa en Spatial Anchors-resurs

Gå till <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>.

I den vänstra rutan väljer du **skapa en resurs**.

Använd sökrutan för att söka efter **avstånds ankare**.

![Skärm bild som visar resultatet av en sökning efter avstånds ankare.](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Välj **spatiala ankare**och välj sedan **skapa**.

Gör följande i **konto fönstret spatiala ankare** :

* Ange ett unikt resurs namn genom att använda vanliga alfanumeriska tecken.  
* Välj den prenumeration som du vill koppla resursen till.  
* Skapa en resurs grupp genom att välja **Skapa ny**. Ge den namnet **myResourceGroup**och välj sedan **OK**.  

  [!INCLUDE [resource group intro text](resource-group.md)]
  
* Välj en plats (region) där resursen ska placeras.  
* Välj **Ny** för att börja skapa resursen.

![Skärm bild av fönstret spatiala ankare för att skapa en resurs.](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

När resursen har skapats visar Azure Portal att distributionen är slutförd. 
   
![Skärm bild som visar att resurs distributionen är klar.](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Välj **Gå till resurs**. Nu kan du Visa resurs egenskaperna. 
   
Kopiera resursens **konto-ID-** värde till en text redigerare för senare användning.

![Skärm bild av fönstret resurs egenskaper.](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Kopiera också resursens **konto** värde till en text redigerare för senare användning.

![Skärm bild som visar resursens konto värde.](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

Under **Inställningar**väljer du **nyckel**. Kopiera värdet för **primär nyckel** , **konto nyckel**, till en text redigerare för senare användning.

![Skärm bild av fönstret nycklar för kontot.](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
