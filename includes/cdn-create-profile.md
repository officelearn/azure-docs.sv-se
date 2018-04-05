---
title: ta med fil
description: ta med fil
services: cdn
author: dksimpson
ms.service: cdn
ms.topic: include
ms.date: 04/04/2018
ms.author: rli; v-deasim
ms.custom: include file
ms.openlocfilehash: 692364e9b2e78b3bd1f63137148dfbc680364737
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
## <a name="create-a-new-cdn-profile"></a>Skapa en ny CDN-profil

En CDN-profil är en behållare för CDN-slutpunkter och anger en prisnivå.

1. Välj i Azure-portalen i det övre vänstra **skapar du en resurs**.
    
    Den **ny** visas.
   
2. Välj **webb + mobilt**, sedan **CDN**.
   
    ![Välj CDN-resurs](./media/cdn-create-profile/cdn-new-resource.png)

    Den **CDN-profilen** visas.

    Använd de inställningar som anges i tabellen enligt bilden.
   
    ![Ny CDN-profil](./media/cdn-create-profile/cdn-new-profile.png)

    | Inställning  | Värde |
    | -------- | ----- |
    | **Namn** | Ange *min-cdn-profil-123* för ditt profilnamn. Det här namnet måste vara globalt unika. Om den redan används, kan du ange en annan. |
    | **Prenumeration** | Välj en Azure-prenumeration från den nedrullningsbara listan.|
    | **Resursgrupp** | Välj **Skapa nytt** och ange *min-resurs-grupp-123* för din resursgruppens namn. Det här namnet måste vara globalt unika. Om den redan används, kan du ange en annan. | 
    | **Resursgruppens plats** | Välj **centrala USA** från den nedrullningsbara listan. |
    | **prisnivå** | Välj **Standard Verizon** från den nedrullningsbara listan. |
    | **Skapa en ny CDN-slutpunkt nu** | Lämna inte är markerad. |  
   
3. Välj **fäst på instrumentpanelen** att spara profilen på instrumentpanelen när den har skapats.
    
4. Välj **skapa** att skapa profilen. 

