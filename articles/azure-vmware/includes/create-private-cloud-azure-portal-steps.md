---
title: Distribuera Azure VMware Solution
description: Steg för att distribuera en Azure VMware-lösning med hjälp av Azure Portal.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 4d0881721cd8c13d1b6c9fb3a29e4cdb6d6a753f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578331"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **skapa en ny resurs**. I text rutan **Sök i Marketplace** `Azure VMware Solution` och välj **Azure VMware-lösning** i listan. I fönstret **Azure VMware-lösning** väljer du **skapa**.

1. Ange värden för fälten på fliken **grundläggande** . I följande tabell visas egenskaperna för fälten.

   | Field   | Värde  |
   | ---| --- |
   | **Prenumeration** | Prenumerationen som du planerar att använda för distributionen.|
   | **Resursgrupp** | Resurs gruppen för dina privata moln resurser. |
   | **Plats** | Välj en plats, t. ex. **USA, östra**.|
   | **Resurs namn** | Namnet på ditt privata moln i Azure VMware-lösningen. |
   | **SKU** | Välj följande SKU-värde: AV36 |
   | **Värdar** | Antalet värdar som ska läggas till i det privata moln klustret. Standardvärdet är 3, som kan höjas eller sänkas efter distributionen.  |
   | **administratörs lösen ord för vCenter** | Ange ett lösen ord för moln administratören. |
   | **Lösen ord för NSX-T Manager** | Ange ett NSX-T-administratörs lösen ord. |
   | **Adress block** | Ange ett IP-adressblock för CIDR-nätverket för det privata molnet, till exempel 10.175.0.0/22. |
   | **Virtual Network** | Välj en Virtual Network eller skapa en ny för det privata molnet Azure VMware-lösning.  |

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Ange värden för fälten på fliken grundläggande." border="true":::

1. När du är färdig väljer du **Granska + skapa**. Kontrol lera att informationen har angetts på nästa skärm. Om informationen är korrekt väljer du **skapa**.

   > [!NOTE]
   > Det här steget tar ungefär två timmar. 

1. Kontrol lera att distributionen har slutförts. Navigera till resurs gruppen som du skapade och välj ditt privata moln.  Du ser statusen **slutförd** när distributionen har slutförts. 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Ange värden för fälten på fliken grundläggande." border="true":::