---
title: ta med fil
description: ta med fil
services: cdn
author: SyntaxC4
ms.service: cdn
ms.topic: include
ms.date: 05/24/2018
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: c58b226c0f3bd63cb2a54bd3d8c91eb750a26f0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684681"
---
## <a name="create-a-new-cdn-profile"></a>Skapa en ny CDN-profil

En CDN-profil är en container för CDN-slutpunkter och den anger en prisnivå.

1. Välj **Skapa en resurs** längst upp till vänster i Azure-portalen. 
    
    Fönstret **Ny** visas.
   
2. Välj **Webb och mobilt**och välj **CDN**.
   
    ![Välj CDN-resurs](./media/cdn-create-profile/cdn-new-resource.png)

    Fönstret **CDN-profil** visas.

3. Som CDN-profilinställningar använder du de värden som anges i följande tabell:
   
    | Inställning  | Värde |
    | -------- | ----- |
    | **Namn** | Ange *my-cdn-profile-123* som profilnamn. Namnet måste vara globalt unikt. Om det redan används kan du ange ett annat namn. |
    | **Prenumeration** | Välj en Azure-prenumeration i listrutan. |
    | **Resursgrupp** | Välj **Skapa ny** och ange *my-resource-group-123* som namn på resursgruppen. Det här namnet måste vara globalt unikt. Om det redan används kan du ange ett annat namn eller välja **Använd befintligt** och välja **min-resurs-grupp-123** från den nedrullningsbara listan. | 
    | **Resursgruppsplats** | Välj **USA, centrala** i listrutan. |
    | **prisnivå** | Välj **Standard Verizon** i listrutan. |
    | **Skapa en ny CDN-slutpunkt nu** | Lämna det avmarkerat. |  
   
    ![Ny CDN-profil](./media/cdn-create-profile/cdn-new-profile.png)

4. Spara profilen på instrumentpanelen när den har skapats genom att välja **Fäst på instrumentpanelen**.
    
5. Välj **Skapa** för att skapa profilen. 

    Enbart för **Azure CDN Standard från Microsoft**-profiler, profilen slutförs vanligtvis inom två timmar. 

