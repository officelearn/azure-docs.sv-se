---
title: Hantera dina nycklar i THOMAS | Microsoft Docs
description: Använd språket förstå (THOMAS) för att hantera programmässiga API, slutpunkt och externa nycklar.
titleSuffix: Azure
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/21/2018
ms.author: v-geberr
ms.openlocfilehash: b1d0d01621769a6bfc0e14e703d7f988403703c6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "35355746"
---
# <a name="manage-your-luis-keys"></a>Hantera THOMAS-nycklar
En nyckel kan du skapa och publicera en app i THOMAS eller fråga din slutpunkt. 

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
## <a name="key-concepts"></a>Viktiga begrepp
Se [nycklar i THOMAS](luis-concept-keys.md) att förstå THOMAS redigering och slutpunkten viktiga begrepp.

<a name="create-and-use-an-endpoint-key"></a>
## <a name="assign-endpoint-key"></a>Tilldela slutpunktsnyckel
På den **publicera appen** sidan, det finns redan en nyckel i den **resurser och nycklar** tabell. Detta är nyckeln redigering (Start). 

1. Skapa en THOMAS nyckel på den [Azure-portalen](https://portal.azure.com). Ytterligare instruktioner finns i [skapar en prenumeration nyckel med hjälp av Azure](luis-how-to-azure-subscription.md).
 
2. För att lägga till THOMAS nyckeln som skapades i föregående steg klickar du på den **Lägg till nyckel** för att öppna den **tilldela en nyckel till din app** dialogrutan. 

    ![Tilldela en nyckel till din app](./media/luis-manage-keys/assign-key.png)
3. Välj en klient i dialogrutan. 
 
    > [!Note]
    > En klient representerar i Azure, Azure Active Directory ID för klient eller organisation som är associerad med en tjänst. Om du tidigare har registrerat dig för en Azure-prenumeration med ditt individuella Microsoft Account, har du redan en klient! När du loggar in på Azure-portalen, loggas du automatiskt till [din standard-klient](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant). Du kan välja att använda den här innehavaren men du kanske vill skapa ett administratörskonto för din organisation.

4. Välj Azure-prenumerationen som är associerade med Azure THOMAS nyckel som du vill lägga till.

5. Välj Azure THOMAS-konto. Region för kontot visas inom parentes. 

    ![Välj nyckeln](./media/luis-manage-keys/assign-key-filled-out.png)

6. När du tilldelar den här slutpunkten nyckeln, kan du använda den i alla endpoint-frågor. 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

## <a name="publishing-regions"></a>Publishing regioner
Lär dig mer om hur du publicerar [regioner](luis-reference-regions.md) inklusive publicering i [Europa](luis-reference-regions.md#publishing-to-europe), och [Australien](luis-reference-regions.md#publishing-to-australia). Publishing regioner skiljer sig från redigering regioner. Kontrollera att du skapar en app i authoring region motsvarande publishing regionen som du vill använda.

## <a name="unassign-key"></a>Ta bort tilldelning av nyckeln

* I den **lista över resurser och nycklar**, klicka på Papperskorgen bin bredvid den enhet du vill ta bort tilldelningen. Klicka på **OK** i bekräftelsemeddelandet att bekräfta borttagningen.
 
    ![Ta bort tilldelning entitet](./media/luis-manage-keys/unassign-key.png)

> [!NOTE]
> Tar bort nyckeln THOMAS tar inte bort den från din Azure-prenumeration.

## <a name="next-steps"></a>Nästa steg

Använda din nyckel för att publicera din app i den **publicera appen** sidan. Mer information om publicering finns [publicera appen](PublishApp.md).

[LUIS]: luis-reference-regions.md#luis-website