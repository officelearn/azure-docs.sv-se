---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/25/2018
ms.author: tomfitz
ms.openlocfilehash: 03e4053b65cf39101e8cb5d35ce439a759ec11d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187730"
---
1. På bladet Inställningar för den resurs, resursgrupp eller prenumeration som du vill låsa upp väljer du **Lås**.
   
      ![välj lås](./media/resource-manager-lock-resources/select-lock.png)
2. Om du vill lägga till ett lås väljer du **Lägg till**. Om du vill skapa ett lås på en överordnad nivå väljer du den överordnade. Den markerade resursen ärver låset från den överordnade resursen. Du kan till exempel låsa resursgruppen för att använda ett lås på alla dess resurser.
   
      ![lägga till lås](./media/resource-manager-lock-resources/add-lock.png) 
3. Ge låset ett namn och låsnivå. Om du vill kan du lägga till anteckningar som beskriver låset.
   
      ![ställ in lås](./media/resource-manager-lock-resources/set-lock.png) 
4. Om du vill ta bort låset markerar du ellipsen och **Ta bort** från de tillgängliga alternativen.
   
      ![ta bort lås](./media/resource-manager-lock-resources/delete-lock.png) 

