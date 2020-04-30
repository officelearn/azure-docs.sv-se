---
title: Snabbstart – Skapa en Azure CDN-profil och -slutpunkt | Microsoft Docs
description: Den här snabbstarten visar hur du aktiverar Azure CDN genom att skapa en ny CDN-profil och CDN-slutpunkt.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 05/24/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: e11e6e4cc0194991b322d591049d7156b9979c45
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81254061"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint"></a>Snabbstart: Skapa en Azure CDN-profil och CDN-slutpunkt
I den här snabbstarten aktiverar du Azure Content Delivery Network (CDN) genom att skapa en ny CDN-profil och CDN-slutpunkt. När du har skapat en profil och en slutpunkt kan du börja leverera innehåll till dina kunder.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav
För den här snabbstarten måste du ha skapat ett lagringskonto med namnet *mystorageacct123*, som du använder för ursprungets värdnamn. Mer information finns i [integrera ett Azure Storage-konto med Azure CDN](cdn-create-a-storage-account-with-cdn.md).

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal
Logga in på [Azure Portal](https://portal.azure.com) med ditt Azure-konto.

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Skapa en ny CDN-slutpunkt

När du har skapat en CDN-profil kan använda du den för att skapa en slutpunkt.

1. På instrumentpanelen i Azure Portal väljer du den CDN-profil som du skapade. Om du inte hittar den väljer du **Alla tjänster** och sedan **CDN-profiler**. På sidan **CDN-profiler** markerar du den profil som du vill använda. 
   
    CDN-profilsidan visas.

2. Välj **slut punkt**.
   
    ![CDN-profil](./media/cdn-create-new-endpoint/cdn-select-endpoint.png)
   
    Fönstret **Lägg till slutpunkt** visas.

3. Använd värdena som anges i följande tabell som slutpunktsinställningar:

    | Inställning | Värde |
    | ------- | ----- |
    | **Namn** | Ange *my-endpoint-123* som slutpunktens värdnamn. Det här namnet måste vara globalt unikt. Om det redan används kan du ange ett annat namn. Det här namnet används för att komma åt dina cachelagrade resurser på domänens _ &lt;slut punkts namn&gt;_. azureedge.net.|
    | **Ursprungstyp** | Välj **lagring**. | 
    | **Ursprungets värdnamn** | Ange *mystorageacct123.blob.core.windows.net* som värdnamn. Det här namnet måste vara globalt unikt. Om det redan används kan du ange ett annat namn. |
    | **Sökväg till ursprung** | Lämna tomt. |
    | **Ursprungsvärdadress** | Använd det standardgenererade värdet. |  
    | **Protokollhanterare** | Använd de valda standardalternativen för **HTTP** och **HTTPS**. |
    | **Ursprungsport** | Använd standardportvärdena. | 
    | **Optimerat för** | Använd standardvalet **Allmän webbleverans**. |

    ![Fönstret Lägg till slutpunkt](./media/cdn-create-new-endpoint/cdn-add-endpoint.png)

3. Välj **Lägg till** för att skapa den nya slutpunkten.
   
   När slutpunkten har skapats, visas den i en lista över slutpunkter för profilen.
    
   ![CDN-slutpunkt](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
   Slutpunkten kan inte användas direkt, eftersom det tar tid för registreringen att sprida sig: 
   - För **Azure CDN Standard från Microsoft**-profiler slutförs spridningen vanligtvis inom 10 minuter. 
   - För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
   - För **Azure CDN Standard från Verizon**- och **Azure CDN Premium från Verizon**-profiler slutförs spridningen vanligtvis inom 90 minuter. 

## <a name="clean-up-resources"></a>Rensa resurser
I föregående steg skapade du en CDN-profil och en CDN-slutpunkt i en resursgrupp. Spara dessa resurser om du vill gå till [Nästa steg](#next-steps) och lära dig hur du lägger till en anpassad domän i din slutpunkt. Men om du inte tänker använda de här resurserna i framtiden kan du ta bort dem genom att ta bort resursgruppen (på så sätt undviker du ytterligare kostnader):

1. På den vänstra menyn i Azure Portal väljer du **Resursgrupper** och sedan **my-resource-group-123**.

2. På sidan **Resursgrupp** väljer du **Ta bort resursgrupp**, skriver *my-resource-group-123* i textrutan och väljer **Ta bort**.

    Den här åtgärden tar bort resursgruppen, profilen och slutpunkten som du skapade i den här snabbstarten.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du lägger till en anpassad domän i din CDN-slutpunkt finns i följande självstudiekurs:

> [!div class="nextstepaction"]
> [Självstudier: Lägga till en anpassad domän i Azure CDN-slutpunkten](cdn-map-content-to-custom-domain.md)


