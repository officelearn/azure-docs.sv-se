---
title: ta med fil
description: ta med fil
services: cdn
author: SyntaxC4
ms.service: cdn
ms.topic: include
ms.date: 04/13/2018
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: 0db5f571324694f0518ffc4e92af985e5125d755
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
## <a name="create-a-new-cdn-profile"></a>Skapa en ny CDN-profil

En CDN-profil är en behållare för CDN-slutpunkter och den anger en prisnivå.

1. Välj **Skapa en resurs** längst upp till vänster i Azure-portalen.
    
    Fönstret **Ny** visas.
   
2. Välj **Webb och mobilt**och välj **CDN**.
   
    ![Välj CDN-resurs](./media/cdn-create-profile/cdn-new-resource.png)

    Fönstret **CDN-profil** visas.

    Använd inställningarna i tabellen på bilden.
   
    ![Ny CDN-profil](./media/cdn-create-profile/cdn-new-profile.png)

    | Inställning  | Värde |
    | -------- | ----- |
    | **Namn** | Ange *my-cdn-profile-123* som profilnamn. Det här namnet måste vara globalt unikt. Om det redan används kan du ange ett annat namn. |
    | **Prenumeration** | Välj en Azure-prenumeration i listrutan.|
    | **Resursgrupp** | Välj **Skapa ny** och ange *my-resource-group-123* som namn på resursgruppen. Det här namnet måste vara globalt unikt. Om det redan används kan du ange ett annat namn. | 
    | **Resursgruppsplats** | Välj **USA, centrala** i listrutan. |
    | **Prisnivå** | Välj **Standard Verizon** i listrutan. |
    | **Skapa en ny CDN-slutpunkt nu** | Lämna det avmarkerat. |  
   
3. Spara profilen på instrumentpanelen när den har skapats genom att välja **Fäst på instrumentpanelen**.
    
4. Välj **Skapa** för att skapa profilen. 

