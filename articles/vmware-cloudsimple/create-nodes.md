---
title: Etableringsnoder för VMware-lösning från CloudSimple - Azure
description: Lär dig hur du lägger till noder till din VMWare med CloudSimple-distribution
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: eb033425d18b472c9da1a2d6a1bb6f166702905e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024814"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>Etableringsnoder för Azure VMware-lösning per CloudSimple

Etableringsnoder i Azure-portalen. Sedan kan du ställa in pay-as-you go-kapacitet för din CloudSimple privata molnmiljö.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>Lägga till en nod i ditt Privata CloudSimple-moln

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple Noder**.

   ![Sök CloudSimple-noder](media/create-cloudsimple-node-search.png)

3. Välj **CloudSimple-noder**.
4. Klicka på **Lägg till** om du vill skapa noder.

    ![Lägg till CloudSimple-noder](media/create-cloudsimple-node-add.png)

5. Välj den prenumeration där du vill etablera CloudSimple-noder.
6. Välj resursgruppen för noderna. Om du vill lägga till en ny resursgrupp klickar du på **Skapa ny**.
7. Ange prefixet för att identifiera noderna.
8. Välj plats för nodresurserna.
9. Välj den dedikerade platsen som ska vara värd för nodresurserna.
10. Välj [nodtypen](cloudsimple-node.md).
11. Välj antalet noder som ska etableras.
12. Välj **Granska + Skapa**.
13. Granska inställningarna. Om du vill ändra inställningar klickar du på **Föregående**.
14. Välj **Skapa**.

## <a name="next-steps"></a>Nästa steg

* [Skapa privat moln](create-private-cloud.md)
