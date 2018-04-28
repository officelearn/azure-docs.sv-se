---
title: Publicera versioner av ditt API med Azure API Management | Microsoft Docs
description: Följ stegen i den här självstudien för att lära dig hur du publicerar flera versioner i API Management.
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 7d61fa25f29f1380fe58069dbc25ed9ce1fff53c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="publish-multiple-versions-of-your-api"></a>Publicera flera versioner av ditt API 

Ibland måste man kunna låta anropare använda olika versioner av ditt API. Om anropare vill uppgradera till en senare version ska de kunna göra det på ett enkelt sätt. Detta går att göra genom att använda **versioner** i Azure API Management. Mer information finns i avsnittet om [versioner & revisioner](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till en ny version till ett befintligt API
> * Välja en version av ett schema
> * Lägga till versionen till en produkt
> * Gå till utvecklarportalen för att se versionen

![Versionen som visas på utvecklarportalen](media/api-management-getstarted-publish-versions/azure_portal.PNG)

## <a name="prerequisites"></a>Nödvändiga komponenter

* Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).
* Slutför även följande självstudie: [Importera och publicera ditt första API](import-and-publish.md).

## <a name="add-a-new-version"></a>Lägga till en ny version

![Snabbmenyn för API – lägg till version](media/api-management-getstarted-publish-versions/AddVersionMenu.png)

1. Välj **Conference API** (konferens-API) från listan över API.
2. Välj snabbmenyn (**...** ) bredvid.
3. Välj **+ Lägg till version**.

    > [!TIP]
    > Versioner kan även aktiveras första gången du skapar ett nytt API – välj **Version this API?** (vilken är versionen för det här API:et?) på fliken **Lägg till API**.

## <a name="choose-a-versioning-scheme"></a>Välja ett versionsschema

Med Azure API Management kan du välja hur du låter dina anropare ange vilken version av ditt API de vill använda. Du kan ange vilken API-version som ska användas genom att välja en **versionsschema**. Det här schemat kan vara antingen en **sökväg, rubrik eller frågesträng**. I följande exempel används sökvägen för att välja versionsschema.

![Skärmen Lägg till version](media/api-management-getstarted-publish-versions/AddVersion.PNG)

1. Låt **sökvägen** vara **versionsschemat**.
2. Lägg till **v1** som **versions-ID**.

    > [!TIP]
    > Om du väljer **rubrik** eller **frågesträng** som versionsschema, måste du ange ytterligare ett värde – namnet på rubriken eller frågesträngsparametern.

3. Ange en beskrivning om du vill.
4. Välj **Skapa** för att ställa in den nya versionen.
5. Under **Big Conference API** (storkonferens API) i listan över API:er visas nu två distinkta API:er – **Ursprungliga** och **v1**.

    ![Versioner som listas under API i Azure Portal](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > Om du lägger till en version till ett API som inte är versionshanterat, skapas en **ursprunglig version** automatiskt, som svarar på en standard-URL. Detta säkerställer att eventuella befintliga anrop inte störs när ytterligare en version läggs till. Om du skapar ett nytt API med versioner som är aktiverat i början, skapas inte en ursprunglig version.

6. Du kan nu redigera och konfigurera **v1** som ett API som skiljer sig från det **ursprungliga**. Ändringar i de olika versionerna påverkar inte varandra.

## <a name="add-the-version-to-a-product"></a>Lägga till versionen till en produkt

För att en anropare ska kunna se den nya versionen, måste den läggas till en **produkt**.

1. Välj **produkter** från sidan för den klassiska distributionsmodellen.
2. Välj **Obegränsad**.
3. Välj **API:er**.
4. Välj **Lägg till**.
5. Välj **Conference API, Version v1** (Konferens-API, version v1).
6. Navigera till sidan för hantering av tjänsten och markera **API:er**.

## <a name="browse-the-developer-portal-to-see-the-version"></a>Gå till utvecklarportalen för att se versionen

1. Välj **Utvecklarportal** i den översta menyn.
2. Välj **API:er**, kontrollera att **konferens-API** visar versionerna **Ursprunglig** och **v1**.
3. Välj **v1**.
4. Observera **Fråge-URL** för den första åtgärden i listan. Det visar att URL-sökvägen för API:et innehåller **v1**.

    ![Snabbmenyn för API – lägg till version](media/api-management-getstarted-publish-versions/developer_portal.png)

## <a name="next-steps"></a>Nästa steg

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till en ny version till ett befintligt API
> * Välja en version av ett schema 
> * Lägga till versionen till en produkt
> * Gå till utvecklarportalen för att se versionen

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Uppgradera och skala](upgrade-and-scale.md)