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
ms.custom: mvc
ms.topic: tutorial
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: 686d6e2ff01fd4d8283350ab8097e1f73c914af0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261004"
---
# <a name="create-and-publish-a-product"></a>Skapa och publicera en produkt  

En produkt innehåller en eller flera API:er samt en användningskvot och användningsvillkor i Azure API Management. När en produkt har publicerats kan utvecklarna prenumerera på produkten och börja använda produktens API: er.  

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa och publicera en produkt
> * Lägga till ett API till produkten

![Lägga till produktsjälvstudie](media/api-management-howto-add-products/added-product.png)

## <a name="prerequisites"></a>Krav

+ Lär dig [Azure API Management-terminologin](api-management-terminology.md).
+ Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).
+ Slutför även följande självstudie: [Importera och publicera ditt första API](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Skapa och publicera en produkt

![Lägg till produkt](media/api-management-howto-add-products/02-create-publish-product-01.png)

1. Klicka på **Produkter** i menyn till vänster för att visa sidan **Produkter**.
2. Klicka på **+ Lägg till**.

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
    | API:er                     | Produkter är associationer med en eller flera API:er. Du kan inkludera flera API:er och erbjuda dem till utvecklare via utvecklarportalen. <br/> Du kan lägga till en befintlig API när produkten skapas. Du kan lägga till ett API i produkten senare, antingen från sidan **Produktinställningar** eller när du skapar ett API. |

3. Klicka på **Skapa** för att skapa den nya produkten.

### <a name="add-more-configurations"></a>Lägga till fler konfigurationer

Du kan fortsätta konfigurera produkten efter att du har sparat den genom att välja fliken **Inställningar**. 

Visa/lägg till prenumeranter till produkten från fliken **Prenumerationer**.

Ange synlighet för en produkt för utvecklare eller gäster från fliken **Åtkomstkontroll.**

## <a name="add-apis-to-a-product"></a><a name="add-apis"> </a>Lägga till API:er till en produkt

Produkter är associationer med en eller flera API:er. Du kan inkludera flera API:er och erbjuda dem till utvecklare via utvecklarportalen. Du kan lägga till en befintlig API när produkten skapas. Du kan lägga till en API till produkten senare, antingen från sidan Produkter **Inställningar** eller när du skapar en API.

Utvecklare måste först prenumerera på en produkt för att få åtkomst till API:n. När de prenumererar få de en prenumerationsnyckel som går att använda till alla API:er i produkten. Om du har skapat APIM-instansen är du redan administratör, så du prenumererar på alla produkter som standard.

### <a name="add-an-api-to-an-existing-product"></a>Lägga till en API till en befintlig produkt

![Lägga till produkt-API](media/api-management-howto-add-products/02-create-publish-product-02.png)

1. Från fliken **Produkter** väljer du en produkt.
2. Navigera till fliken **API:er**.
3. Klicka på **+ Lägg till**.
4. Välj ett API och klicka på **Välj**.

> [!TIP]
> Du kan skapa eller uppdatera användarens prenumeration till en *produkt* med anpassade prenumerationsnycklar [via REST API](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/subscription/createorupdate) eller PowerShell-kommando.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa och publicera en produkt
> * Lägga till ett API till produkten

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Skapa en tom API och testa API-svaren](mock-api-responses.md)
