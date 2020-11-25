---
title: Självstudie – Skapa och publicera en produkt i Azure API Management
description: 'I den här självstudien skapar du och publicerar en produkt i Azure API Management. När den har publicerats kan utvecklare börja använda produktens API: er.'
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 2f298f240d8aa7a38b42a8c78ee3c90fe3423d10
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993558"
---
# <a name="tutorial-create-and-publish-a-product"></a>Självstudie: skapa och publicera en produkt  

I Azure API Management innehåller en [*produkt*](api-management-terminology.md#term-definitions) en eller flera API: er samt användnings kvot och användnings villkor. När en produkt har publicerats kan utvecklarna prenumerera på produkten och börja använda produktens API: er.  

I de här självstudierna får du lära dig att

> [!div class="checklist"]
> * Skapa och publicera en produkt
> * Lägga till ett API till produkten

:::image type="content" source="media/api-management-howto-add-products/added-product.png" alt-text="API Management produkter i portalen":::


## <a name="prerequisites"></a>Förutsättningar

+ Lär dig [Azure API Management-terminologin](api-management-terminology.md).
+ Slutför följande snabb start: [skapa en Azure API Management-instans](get-started-create-service-instance.md).
+ Slutför även följande självstudie: [Importera och publicera ditt första API](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Skapa och publicera en produkt

1. Logga in på Azure Portal och navigera till API Management-instansen.
1. I det vänstra navigerings fältet väljer du **produkter**  >  **+ Lägg till**.
1.  I fönstret **Lägg till produkt** anger du de värden som beskrivs i följande tabell för att skapa din produkt.

    :::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-01.png" alt-text="Lägg till produkt i portalen":::

    | Name                     | Beskrivning                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Visningsnamn             | Namnet som du vill att det ska visas i [Developer-portalen](api-management-howto-developer-portal.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Description              | Ange information om produkten, till exempel dess syfte, de API: er som ger åtkomst till och annan information.                                                                                                                                               |
    | Tillstånd                    | Välj **publicerad** om du vill publicera produkten. Produkten måste vara publicerad innan API:er i en produkt kan anropa den. Som standard är nya produkter opublicerade och visas endast i gruppen  **Administratörer** .                                                                                      |
    | Prenumeration krävs    | Välj om en användare måste prenumerera för att använda produkten.                                                                                                                                                                                                                                   |
    | Godkännande krävs        | Välj om du vill att en administratör ska granska och godkänna eller avvisa prenumerations försök till den här produkten. Om du inte väljer det här godkänns prenumerations försök automatiskt.                                                                                                                         |
    | Antal tillåtna prenumerationer | Du kan också begränsa antalet flera samtidiga prenumerationer.                                                                                                                                                                                                                                |
    | Juridiska villkor              | Du kan inkludera användningsvillkor för produkten som prenumeranter måste godkänna för att kunna använda produkten.                                                                                                                                                                                                             |
    | API:er                     | Välj en eller flera API: er. Du kan också lägga till API: er när du har skapat produkten. Mer information finns i [lägga till API: er till en produkt](#add-apis-to-a-product) senare i den här artikeln. |

3. Välj **skapa** för att skapa den nya produkten.

### <a name="add-more-configurations"></a>Lägga till fler konfigurationer

Fortsätt att konfigurera produkten när du har sparat den. I API Management-instansen väljer du produkten i fönstret **produkter** . Lägg till eller uppdatera:


|Objekt   |Beskrivning  |
|---------|---------|
|Inställningar     |    Produktens metadata och tillstånd     |
|API:er     |  API: er kopplade till produkten       |
|[Principer](api-management-howto-policies.md)     |  Principer som tillämpas på produkt-API: er      |
|Åtkomstkontroll     |  Produkt synlighet för utvecklare eller gäster       |
|[Prenumerationer](api-management-subscriptions.md)    |    Produkt prenumeranter     |

## <a name="add-apis-to-a-product"></a>Lägga till API:er till en produkt

Produkter är associationer med en eller flera API:er. Du kan inkludera flera API:er och erbjuda dem till utvecklare via utvecklarportalen. När produkten skapas kan du lägga till en eller flera befintliga API: er. Du kan också lägga till API: er till produkten senare, antingen från sidan produkt **Inställningar** eller när du skapar ett API.

Utvecklare måste först prenumerera på en produkt för att få åtkomst till API:n. När de prenumererar få de en prenumerationsnyckel som går att använda till alla API:er i produkten. Om du har skapat APIM-instansen är du redan administratör, så du prenumererar på alla produkter som standard.

### <a name="add-an-api-to-an-existing-product"></a>Lägga till en API till en befintlig produkt


1. I det vänstra navigerings fönstret på API Management-instansen väljer du **produkter**.
1. Välj en produkt och välj sedan **API: er**.
1. Välj **+ Lägg till**.
1. Välj en eller flera API: er och **Välj** sedan.

:::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-02.png" alt-text="Lägg till API i befintlig produkt":::

> [!TIP]
> Du kan skapa eller uppdatera en användares prenumeration på en produkt med anpassade prenumerations nycklar via ett [REST API](/rest/api/apimanagement/2019-12-01/subscription/createorupdate) -eller PowerShell-kommando.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa och publicera en produkt
> * Lägga till ett API till produkten

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Skapa en tom API och testa API-svaren](mock-api-responses.md)
