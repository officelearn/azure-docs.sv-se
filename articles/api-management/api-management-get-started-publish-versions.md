---
title: "Publicera versioner av din API med hjälp av Azure API Management | Microsoft Docs"
description: "Följ stegen i den här kursen lär dig hur du publicera flera versioner i API-hantering."
services: api-management
documentationcenter: 
author: mattfarm
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2017
ms.author: apimpm
ms.openlocfilehash: 7c355e2feb5ebe5971d8391b326422a1abec1497
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="publish-multiple-versions-of-your-api-in-a-predictable-way"></a>Publicera flera versioner av din API i ett förutsägbart sätt
Den här självstudiekursen beskrivs hur du ställer in din API-versioner och välja hur de anropas av API-utvecklare.

## <a name="prerequisites"></a>Krav
Den här kursen måste du skapa en API Management-tjänst och har en befintlig API som du kan ändra (i stället för konferens API).

## <a name="about-versions"></a>Om versioner
Ibland är opraktiska att anropare till ditt API använda exakt samma version. Ibland vill du publicera nya eller andra API-funktioner till vissa användare, medan andra vill behålla med API som för närvarande fungerar för dessa. När anropare vill uppgradera till en senare version kan vill de kunna göra detta med hjälp av ett enkelt att förstå metod.  Vi kan göra detta med **versioner** i Azure API Management.

## <a name="walkthrough"></a>Genomgång
I den här genomgången vi lägga till en ny version till en befintlig API, välja en versionshantering schema och version identifierare.

## <a name="add-a-new-version"></a>Lägg till en ny version
![Snabbmenyn för API - Lägg till version](media/api-management-getstarted-publish-versions/AddVersionMenu.png)
1. Bläddra till den **API: er** sida i API Management-tjänsten i Azure-portalen.
2. Välj **konferens API** i API-listan, Välj snabbmenyn (**...** ) bredvid den.
3. Välj **+ Lägg till Version**.

    > [!TIP]
    > Versioner kan även aktiveras när du skapar ett nytt API - Välj **Version detta API?** på den **lägga till API** skärmen.

## <a name="choose-a-versioning-scheme"></a>Välj ett schema
Azure API Management kan du välja hur du tillåter anropare att ange vilken version av din API som de vill. Det gör du genom att välja en **schema**. Det här schemat kan vara antingen **sökväg, sidhuvud eller fråga sträng**. I vårt exempel kan vi använda sökväg.
![Lägga till version skärm](media/api-management-getstarted-publish-versions/AddVersion.PNG)
1. Lämna **sökväg** valda som din **schema**.
2. Lägg till **v1** som din **versions-ID**.

    > [!TIP]
    > Om du väljer **huvud** eller **frågesträng** som ett schema, behöver du ange namnet på rubriken för ett ytterligare värde - eller frågesträngparametern.

3. Ange en beskrivning om du vill.
4. Välj **skapa** att ställa in den nya versionen.
5. Underneath **stor konferens API** i listan över API du nu se två distinkta API: erna - **ursprungliga**, och **v1**.
![Versioner som anges under en API i Azure-portalen](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > Om du lägger till en version inte är versionshanterad API kan vi skapa alltid ett **ursprungliga** - du svarar på standard-URL. Detta säkerställer att alla befintliga anropare inte delas av processen för att lägga till en version. Om du skapar ett nytt API med versioner som är aktiverad i början, skapas inte ett Original.

6. Du kan nu redigera och konfigurera **v1** som en API som är helt separat till **ursprungliga**. Ändringar i en version påverkar inte en annan.

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
![Versionen som visas på developer-portalen](media/api-management-getstarted-publish-versions/VersionDevPortal.PNG)
