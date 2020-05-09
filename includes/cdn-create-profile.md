---
title: ta med fil
description: ta med fil
services: cdn
author: SyntaxC4
ms.service: azure-cdn
ms.topic: include
ms.date: 04/30/2020
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: d73a8d743a948cbd94a3af81fe2e77c45c0eeb67
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996206"
---
## <a name="create-a-new-cdn-profile"></a>Skapa en ny CDN-profil

En CDN-profil är en container för CDN-slutpunkter och den anger en prisnivå.

1. I Azure Portal väljer du **skapa en resurs** (längst upp till vänster). Fönstret **Ny** visas.
   
1. Sök efter och välj **CDN**och välj sedan **skapa**:
   
    ![Välj CDN-resurs](./media/cdn-create-profile/cdn-new-resource.png)

    Fönstret **CDN-profil** visas.

1. Ange följande värden:
   
    | Inställningen  | Värde |
    | -------- | ----- |
    | **Namn** | Ange *CDN-Profile-123* för ditt profil namn. Det här namnet måste vara globalt unikt. Ange ett annat namn om det redan används. |
    | **Prenumeration** | Välj en Azure-prenumeration i listrutan. |
    | **Resursgrupp** | Välj **Skapa ny** och ange *CDNQuickstart-RG* som resurs grupp namn eller Välj **Använd befintlig** och välj *CDNQuickstart-RG* om du redan har gruppen. | 
    | **Resursgruppsplats** | Välj en plats nära dig i list rutan. |
    | **Pris nivå** | Välj ett **standard** alternativ för Akamai i list rutan. (Distributions tiden för Akamai-nivån är ungefär en minut. Microsoft-nivån tar cirka 10 minuter och Verizon-nivåerna tar cirka 90 minuter.) |
    | **Skapa en ny CDN-slutpunkt nu** | Lämna det avmarkerat. |  
   
    ![Ny CDN-profil](./media/cdn-create-profile/cdn-new-profile.png)

1. Välj **Skapa** för att skapa profilen.

