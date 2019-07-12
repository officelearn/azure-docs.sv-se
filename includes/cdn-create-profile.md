---
title: ta med fil
description: ta med fil
services: cdn
author: SyntaxC4
ms.service: azure-cdn
ms.topic: include
ms.date: 05/24/2018
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: 8aa6cb3f10b86a6821cd93190ecc2135508739cb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593992"
---
## <a name="create-a-new-cdn-profile"></a>Skapa en ny CDN-profil

En CDN-profil är en container för CDN-slutpunkter och den anger en prisnivå.

1. Välj **Skapa en resurs** längst upp till vänster i Azure-portalen. 
    
    Fönstret **Ny** visas.
   
2. Välj **Webb och mobilt**och välj **CDN**.
   
    ![Välj CDN-resurs](./media/cdn-create-profile/cdn-new-resource.png)

    Fönstret **CDN-profil** visas.

3. Som CDN-profilinställningar använder du de värden som anges i följande tabell:
   
    | Inställning  | Value |
    | -------- | ----- |
    | **Namn** | Ange *my-cdn-profile-123* som profilnamn. Namnet måste vara globalt unikt. Om det redan används kan du ange ett annat namn. |
    | **Prenumeration** | Välj en Azure-prenumeration i listrutan. |
    | **Resursgrupp** | Välj **Skapa ny** och ange *my-resource-group-123* som namn på resursgruppen. Om det redan används kan du ange ett annat namn eller välja **Använd befintligt** och välja **min-resurs-grupp-123** från den nedrullningsbara listan. | 
    | **Resursgruppsplats** | Välj **USA, centrala** i listrutan. |
    | **prisnivå** | Välj **Standard Verizon** i listrutan. |
    | **Skapa en ny CDN-slutpunkt nu** | Lämna det avmarkerat. |  
   
    ![Ny CDN-profil](./media/cdn-create-profile/cdn-new-profile.png)

4. Spara profilen på instrumentpanelen när den har skapats genom att välja **Fäst på instrumentpanelen**.
    
5. Välj **Skapa** för att skapa profilen. 

    Enbart för **Azure CDN Standard från Microsoft**-profiler, profilen slutförs vanligtvis inom två timmar. 

