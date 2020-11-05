---
title: Självstudie – Använd revisioner i API Management för att göra icke-brytande API-ändringar på ett säkert sätt
titleSuffix: Azure API Management
description: Följ stegen i den här självstudien för att lära dig hur du gör ändringar som är bakåtkompatibla i API Management.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: apimpm
ms.openlocfilehash: 3804bfb2a269c431b1a00947f5c7613566a78f49
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377513"
---
# <a name="tutorial-use-revisions-to-make-non-breaking-api-changes-safely"></a>Självstudie: Använd revisioner för att göra icke-brytande API-ändringar på ett säkert sätt
När ditt API är klart och börjar användas av utvecklare, måste du så småningom göra ändringar för det API:et och samtidigt se till att du inte stör anropen till API:et. Det är också bra att informera utvecklarna om de ändringar du gjort. 

I Azure API Management använder du *revisioner* för att göra icke-BRYTAnde API-ändringar så att du kan modellera och testa ändringar på ett säkert sätt. När du är klar kan du göra en revidering aktuell och ersätta ditt nuvarande API. 

För bakgrunden, se [versioner & revisioner](https://azure.microsoft.com/blog/versions-revisions/) och [API-versioner med Azure API Management](https://azure.microsoft.com/blog/api-versioning-with-azure-api-management/).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till en ny version
> * Göra bakåtkompatibla ändringar till din revision
> * Aktualisera dina revisioner och lägga till en ändringsloggpost
> * Gå till utvecklarportalen för att se dina ändringar och ändringsloggen

:::image type="content" source="media/api-management-getstarted-revise-api/azure-portal.png" alt-text="API-revisioner i Azure Portal":::

## <a name="prerequisites"></a>Förutsättningar

+ Lär dig [Azure API Management-terminologin](api-management-terminology.md).
+ Slutför följande snabb start: [skapa en Azure API Management-instans](get-started-create-service-instance.md).
+ Slutför även följande självstudie: [Importera och publicera ditt första API](import-and-publish.md).

## <a name="add-a-new-revision"></a>Lägga till en ny version

1. Logga in på [Azure Portal](https://portal.azure.com)och navigera till API Management-instansen.
1. Välj **API:er**.
2. Välj **demo konferens-API** i API-listan (eller något annat API som du vill lägga till revisioner i).
3. Välj fliken **revisioner** .
4. Välj **+ Lägg till revision**.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-01-add-new-revision.png" alt-text="Lägga till API-granskning":::

    > [!TIP]
    > Du kan också välja **Lägg till revision** i snabb menyn ( **...** ) för API: et.

5. Ange en beskrivning för din nya revision så att du kommer ihåg vad den ska användas för.
6. Välj **skapa** ,
7. Nu skapas en ny version.

    > [!NOTE]
    > Det ursprungliga API:et ligger kvar i **Revision 1**. Det är den här revisionen dina användare fortsätter att anropa tills du väljer att aktualisera en annan revision aktuella.

## <a name="make-non-breaking-changes-to-your-revision"></a>Göra bakåtkompatibla ändringar till din revision

1. Välj **Demo Conference API** från listan över API.
1. Överst på skärmen väljer du fliken **Design**.
1. Observera att **revisionsväljaren** (direkt ovanför designfliken) **Revision 2** som aktuellt val.

    > [!TIP]
    > Använd revisionsväljaren för att växla mellan de revisioner du vill arbeta med.
1. Välj **+ Lägg till åtgärd**.
1. Ange att den nya åtgärden ska vara **POST** , och ange Name (namn), Display Name (visningsnamn) och URL för åtgärden som **test**.
1. **Spara** den nya åtgärden.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-02-make-changes.png" alt-text="Ändra granskning":::
1. Nu har du gjort en ändring i **revision 2**. Använd **revisions väljaren** längst upp på sidan för att växla tillbaka till **revision 1**.
1. Observera att den nya åtgärden inte syns i **Revision 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Aktualisera dina revisioner och lägga till en ändringsloggpost

1. Välj fliken **Revisioner** från menyn upptill på sidan.
1. Öppna snabbmenyn ( **...** ) för **revision 2**.
1. Välj **gör aktuell**.
1. Markera kryss rutan **publicera i offentlig ändrings logg för den här API: n** om du vill skicka anteckningar om den här ändringen. Ange en beskrivning av din ändring som utvecklarna ser, till exempel: **testa revisioner. Ny "test"-åtgärd har lagts till.**
1. Nu är **Revision 2** aktuell.

    :::image type="content" source="media/api-management-getstarted-revise-api/revisions-menu.png" alt-text="Revisions meny i fönstret revisioner":::


## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Gå till utvecklarportalen för att se dina ändringar och ändringsloggen

Om du har provat [Developer-portalen](api-management-howto-developer-portal-customize.md)kan du granska API-ändringarna och ändrings loggen där.

1. I Azure Portal väljer du **API: er**.
1. Välj **Developer-Portal** på den översta menyn.
1. I Developer-portalen väljer du **API: er** och väljer sedan **demo konferens-API**.
1. Lägg märke till att din nya **teståtgärd** nu är tillgänglig.
1. Välj **ändringsloggen** nära API-namnet.
1. Observera att din post i ändringsloggen visas i den här listan.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Lägga till en ny version
> * Göra bakåtkompatibla ändringar till din revision
> * Aktualisera dina revisioner och lägga till en ändringsloggpost
> * Gå till utvecklarportalen för att se dina ändringar och ändringsloggen

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Publicera flera versioner av ditt API](api-management-get-started-publish-versions.md)
