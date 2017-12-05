---
title: "Publicera versioner av din API med hjälp av Azure API Management | Microsoft Docs"
description: "Följ stegen i den här kursen lär dig hur du publicera flera versioner i API-hantering."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: d63bdd3110f5c5db3e7bfec424644fdbc8d8d90c
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="publish-multiple-versions-of-your-api"></a>Publicera flera versioner av ditt API 

Det finns tillfällen när det är opraktiska att anropare ska API-användning exakt samma version. Ibland vill du publicera nya eller andra API-funktioner till vissa användare, medan andra vill behålla med API som för närvarande fungerar för dessa. När anropare vill uppgradera till en senare version kan vill de kunna göra detta med hjälp av ett enkelt att förstå metod.  Vi kan göra detta med **versioner** i Azure API Management. Mer information finns i [versioner & revisioner](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/).

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Lägga till en ny version till en befintlig API
> * Välj ett schema med version
> * Lägg till versionen för en produkt
> * Bläddra developer-portalen för att se versionen

![Versionen som visas på developer-portalen](media/api-management-getstarted-publish-versions/azure_portal.PNG)

## <a name="prerequisites"></a>Krav

+ Slutför följande Snabbstart: [skapa en instans av Azure API Management](get-started-create-service-instance.md).
+ Dessutom slutföra följande kursen: [Import och publicera din första API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-a-new-version"></a>Lägg till en ny version

![Snabbmenyn för API - Lägg till version](media/api-management-getstarted-publish-versions/AddVersionMenu.png)

1. Välj **konferens API** från listan över API.
2. Välj snabbmenyn (**...** ) bredvid den.
3. Välj **+ Lägg till Version**.

    > [!TIP]
    > Versioner kan även aktiveras när du skapar ett nytt API - Välj **Version detta API?** på den **lägga till API** skärmen.

## <a name="choose-a-versioning-scheme"></a>Välj ett schema

Azure API Management kan du välja hur du tillåter anropare att ange vilken version av din API som de vill. Det gör du genom att välja en **schema**. Det här schemat kan vara antingen **sökväg, sidhuvud eller fråga sträng**. I vårt exempel kan vi använda sökväg.

![Lägga till version skärm](media/api-management-getstarted-publish-versions/AddVersion.PNG)

1. Lämna **sökväg** valda som din **schema**.
2. Lägg till **v1** som din **versions-ID**.

    > [!TIP]
    > Om du väljer **huvud** eller **frågesträng** som ett schema, måste du ange namnet på rubriken för ett ytterligare värde - eller frågesträngparametern.

3. Ange en beskrivning om du vill.
4. Välj **skapa** att ställa in den nya versionen.
5. Underneath **stor konferens API** i listan över API du nu se två distinkta API: erna - **ursprungliga**, och **v1**.

    ![Versioner som anges under en API i Azure-portalen](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > Om du lägger till en version inte är versionshanterad API kan vi skapa alltid ett **ursprungliga** - du svarar på standard-URL. Detta säkerställer att alla befintliga anropare inte delas av processen för att lägga till en version. Om du skapar ett nytt API med versioner som är aktiverad i början, skapas inte ett Original.

6. Du kan nu redigera och konfigurera **v1** som en API som skiljer sig till **ursprungliga**. Ändringar i en version påverkar inte en annan.

## <a name="add-the-version-to-a-product"></a>Lägg till versionen för en produkt

För att anropare ska kunna se den nya versionen, måste den läggas till en **produkten** (produkter inte ärvs från överordnade versioner).

1. Välj **produkter** sidan för hantering av tjänsten.
2. Välj **obegränsade**.
3. Välj **API: er**.
4. Välj **Lägg till**.
5. Välj **konferens API, Version v1**.
6. Återgå till sidan för hantering av tjänsten och välja **API: er**.

## <a name="browse-the-developer-portal-to-see-the-version"></a>Bläddra developer-portalen för att se versionen

1. Välj **Utvecklarportalen** på den översta menyn.
2. Välj **API: er**, Lägg märke till att **konferens API** visar **ursprungliga** och **v1** versioner.
3. Välj **v1**.
4. Observera den **begära URL** av den första åtgärden i listan. Det visar att API-URL-sökvägen innehåller **v1**.

    ![Snabbmenyn för API - Lägg till version](media/api-management-getstarted-publish-versions/developer_portal.png)

## <a name="next-steps"></a>Nästa steg

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Lägga till en ny version till en befintlig API
> * Välj ett schema med version 
> * Lägg till versionen för en produkt
> * Bläddra developer-portalen för att se versionen

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Uppgradera och skala](upgrade-and-scale.md)