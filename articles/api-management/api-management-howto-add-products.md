---
title: Skapa och publicera en produkt i Azure API Management
description: Lär dig hur du skapar och publicerar produkter i Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: 4f5097f8555d0345add7de8a0f648190bd4e1e00
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2018
ms.locfileid: "41918016"
---
# <a name="create-and-publish-a-product"></a>Skapa och publicera en produkt  

En produkt innehåller en eller flera API:er samt en användningskvot och användningsvillkor i Azure API Management. När en produkt har publicerats kan utvecklarna prenumerera på produkten och börja använda produktens API: er.  

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa och publicera en produkt
> * Lägga till ett API till produkten

![Lägga till produkt](media/api-management-howto-add-products/added-product.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

+ Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).
+ Slutför även följande självstudie: [Importera och publicera ditt första API](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Skapa och publicera en produkt

1. Klicka på **Produkter** i menyn till vänster för att visa sidan **Produkter**.
2. Klicka på **+ Lägg till**.

    ![tillagd produkt](media/api-management-howto-add-products/add-product.png)

    När du lägger till en produkt måste du ange följande information: 

    | Namn                     | Beskrivning                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Visningsnamn             | Det namn som du vill ska visas i **utvecklarportalen**.                                                                                                                                                                                                                                                        |
    | Namn                     | Ett beskrivande namn på produkten.                                                                                                                                                                                                                                                                                      |
    | Beskrivning              | I fältet **Beskrivning** kan du ange detaljerad information om produkten som t.ex. syfte, API:er som den ger åtkomst till och annan användbar information.                                                                                                                                               |
    | Status                    | Tryck på **Publicerad** om du vill publicera produkten. Produkten måste vara publicerad innan API:er i en produkt kan anropa den. Som standard visas nya produkter som opublicerade och är bara synliga för gruppen **Administratörer**.                                                                                      |
    | Prenumeration krävs    | Markera **Kräv prenumeration** om det ska krävas att en användare prenumererar för att kunna använda produkten.                                                                                                                                                                                                                                   |
    | Godkännande krävs        | Markera **Kräv godkännande** om du vill att en administratör ska granska och godkänna eller avvisa prenumerationsförsök för produkten. Om rutan är avmarkerad godkänns prenumerationsförsök automatiskt.                                                                                                                         |
    | Antal tillåtna prenumerationer | Ange en prenumerationsgräns för att begränsa antalet flera samtidiga prenumerationer.                                                                                                                                                                                                                                |
    | Juridiska villkor              | Du kan inkludera användningsvillkor för produkten som prenumeranter måste godkänna för att kunna använda produkten.                                                                                                                                                                                                             |
    | API:er                     | Produkter är associationer med en eller flera API:er. Du kan inkludera flera API:er och erbjuda dem till utvecklare via utvecklarportalen. <br/> Du kan lägga till en befintlig API när produkten skapas. Du kan lägga till en API till produkten senare, antingen från sidan Produkter **Inställningar** eller när du skapar en API. |

3. Klicka på **Skapa** för att skapa den nya produkten.

### <a name="add-more-configurations"></a>Lägga till fler konfigurationer

Du kan fortsätta konfigurera produkten efter att du har sparat den genom att välja fliken **Inställningar**. 

Visa/lägg till prenumeranter till produkten från fliken **Prenumerationer**.

Ange synlighet för en produkt för utvecklare eller gäster på fliken **Åtkomstkontroll**.

## <a name="add-apis"> </a>Lägga till API:er till en produkt

Produkter är associationer med en eller flera API:er. Du kan inkludera flera API:er och erbjuda dem till utvecklare via utvecklarportalen. Du kan lägga till en befintlig API när produkten skapas. Du kan lägga till en API till produkten senare, antingen från sidan Produkter **Inställningar** eller när du skapar en API.

Utvecklare måste först prenumerera på en produkt för att få åtkomst till API:n. När de prenumererar få de en prenumerationsnyckel som går att använda till alla API:er i produkten. Om du har skapat APIM-instansen är du redan administratör, så du prenumererar på alla produkter som standard.

### <a name="add-an-api-to-an-existing-product"></a>Lägga till en API till en befintlig produkt

![Lägga till produkt-API](media/api-management-howto-add-products/add-product-api.png)

1. Från fliken **Produkter** väljer du en produkt.
2. Navigera till fliken **API:er**.
3. Klicka på **+ Lägg till**.
4. Välj ett API och klicka på **Välj**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa och publicera en produkt
> * Lägga till ett API till produkten

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Skapa en tom API och testa API-svaren](mock-api-responses.md)
