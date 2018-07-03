---
title: Hantera dina nycklar i LUIS | Microsoft Docs
description: Använda Språkförståelse (LUIS) för att hantera din programmatisk API, slutpunkt och externa nycklar.
titleSuffix: Azure
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/21/2018
ms.author: v-geberr
ms.openlocfilehash: 80064584468c109d0bad49f1a22c485fa9cce846
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344526"
---
# <a name="manage-your-luis-keys"></a>Hantera LUIS-nycklar
En nyckel kan du skapa och publicera LUIS-appen eller fråga din slutpunkt. 

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
## <a name="key-concepts"></a>Viktiga begrepp
Se [nycklar i LUIS](luis-concept-keys.md) att förstå LUIS redigering och slutpunkt viktiga begrepp.

<a name="create-and-use-an-endpoint-key"></a>
## <a name="assign-endpoint-key"></a>Tilldela slutpunktsnyckel
På den **publicera app** sidan, det finns redan en nyckel i den **resurser och nycklar** tabell. Det här är nyckeln redigering (Start). 

1. Skapa en LUIS-nyckel på den [Azure-portalen](https://portal.azure.com). Ytterligare instruktioner finns i [skapar en slutpunktsnyckel med hjälp av Azure](luis-how-to-azure-subscription.md).
 
2. För att lägga till LUIS-nyckeln som skapades i föregående steg klickar du på den **Lägg till nyckel** knappen för att öppna den **tilldela en nyckel till appen** dialogrutan. 

    ![Tilldela en nyckel till din app](./media/luis-manage-keys/assign-key.png)
3. Välj en klient i dialogrutan. 
 
    > [!Note]
    > En klient representerar i Azure, Azure Active Directory ID av klienten eller organisation som är associerad med en tjänst. Om du tidigare har registrerat sig för en Azure-prenumeration med ditt individuella Microsoft Account, har du redan en klient! När du loggar in på Azure Portal, loggas du automatiskt att [din standard-klientorganisation](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant). Du kan använda den här klienten men du kanske vill skapa ett administratörskonto för din organisation.

4. Välj den Azure-prenumeration som är associerade med Azure LUIS-nyckel som du vill lägga till.

5. Välj Azure LUIS-konto. Region för kontot visas inom parentes. 

    ![Välj nyckeln](./media/luis-manage-keys/assign-key-filled-out.png)

6. När du har tilldelat den här slutpunktsnyckeln använda den i alla endpoint-frågor. 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

## <a name="publishing-regions"></a>Publicera regioner
Läs mer om hur du publicerar [regioner](luis-reference-regions.md) inklusive publicering i [Europa](luis-reference-regions.md#publishing-to-europe), och [Australien](luis-reference-regions.md#publishing-to-australia). Publicera regioner skiljer sig från redigering regioner. Kontrollera att du skapar en app i redigering region motsvarande att publicera region som du vill.

## <a name="unassign-key"></a>Ta bort tilldelning av nyckel

* I den **listan över resurser och nycklar**, klicka på Papperskorgen bredvid den entitet som du vill ta bort. Klicka sedan på **OK** i bekräftelsemeddelandet att bekräfta borttagningen.
 
    ![Ta bort entiteten](./media/luis-manage-keys/unassign-key.png)

> [!NOTE]
> Tar bort LUIS-nyckel tar det inte bort från Azure-prenumerationen.

## <a name="next-steps"></a>Nästa steg

Använd din nyckel för att publicera din app i den **publicera app** sidan. Mer information om publicering finns i [publicera app](luis-how-to-publish-app.md).

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website