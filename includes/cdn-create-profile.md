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
ms.openlocfilehash: e431e7c45dc2cb41db5b9fa762abc908cc76b375
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34665298"
---
## <a name="create-a-new-cdn-profile"></a>Skapa en ny CDN-profil

En CDN-profil är en behållare för CDN-slutpunkter och den anger en prisnivå.

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

