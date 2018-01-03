---
title: Hur du skapar och publicera en produkt i Azure API Management
description: "Lär dig hur du skapar och publicerar produkter i Azure API Management."
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
ms.openlocfilehash: e6b11145506780f9a08799c4c9daf55ba17b366d
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2017
---
# <a name="create-and-publish-a-product"></a>Skapa och publicera en produkt  

En produkt innehåller en eller flera API: er som en kvot för användning och villkor för användning i Azure API Management. När en produkt har publicerats kan utvecklare prenumererar på produkten och börjar använda produktens API: er.  

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa och publicera en produkt
> * Lägga till ett API till produkten

![lagts till produkten](media/api-management-howto-add-products/added-product.png)

## <a name="prerequisites"></a>Förutsättningar

+ Slutför följande Snabbstart: [skapa en instans av Azure API Management](get-started-create-service-instance.md).
+ Dessutom slutföra följande kursen: [Import och publicera din första API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-and-publish-a-product"></a>Skapa och publicera en produkt

1. Klicka på **produkter** på menyn till vänster för att visa den **produkter** sidan.
2. Klicka på **+ produkten**.

    ![lagts till produkten](media/api-management-howto-add-products/add-product.png)

    När du lägger till en produkt måste du ange följande information: 

    |Namn|Beskrivning|
    |---|---|
    |Visningsnamn|Namnet som du vill att det ska visas i den **utvecklarportalen**.|
    |Namn|Ett beskrivande namn på produkten.|
    |Beskrivning|Den **beskrivning** fältet kan du ange detaljerad information om produkten som sitt syfte, API: er som den ger åtkomst till och annan användbar information.|
    |Status|Tryck på **publicerade** om du vill publicera produkten. Produkten måste publiceras innan API: er i en produkt kan anropas. Som standard nya produkter är opublicerad och visas bara för den **administratörer** grupp.|
    |Godkännande krävs|Kontrollera **kräver godkännande för prenumerationen** om du vill att en administratör för att granska och godkänna eller avvisa prenumeration försöker den här produkten. Om rutan är avmarkerad är prenumeration försök godkänts automatiskt. |
    |Antal tillåtna prenumerationer|Ange gränsen för att begränsa antalet flera samtidiga prenumerationer. |
    |Juridiska villkor|Du kan inkludera villkor för användning av produkten som prenumeranter måste acceptera för att kunna använda produkten.|
    |API:er|Produkter är kopplingar till en eller flera API: er. Du kan innehålla ett antal API: er och erbjuda utvecklare via developer-portalen. <br/> Du kan lägga till en befintlig API under skapande av produkten. Du kan lägga till en API produkten senare, antingen från produkterna **inställningar** sidan eller när du skapar en API.|<br/>Utvecklare måste först prenumerera på en produkt kan få åtkomst till API: et. När man prenumererar få de en prenumeration nyckel som är bra för API: er i produkten.<br/> Om du har skapat APIM instans är du administratör redan, så du prenumererar på varje produkt som standard.|

3. Klicka på **skapa** att skapa den nya produkten.

### <a name="add-more-configurations"></a>Lägger till flera konfigurationer

Du kan fortsätta konfigurera produkten när du har sparat genom att välja den **inställningar** fliken. 

Visa/lägga till prenumeranter till produkten från den **prenumerationer** fliken.

Ange en visning av en produkt för utvecklare eller gästen från den **åtkomstkontroll** fliken.

## <a name="add-apis"></a>Lägg till API: er för en produkt

Produkter är kopplingar till en eller flera API: er. Du kan innehålla ett antal API: er och erbjuda utvecklare via developer-portalen. Du kan lägga till en befintlig API under skapande av produkten. Du kan lägga till en API produkten senare, antingen från produkterna **inställningar** sidan eller när du skapar en API.

Utvecklare måste först prenumerera på en produkt kan få åtkomst till API: et. När man prenumererar få de en prenumeration nyckel som är bra för API: er i produkten. Om du har skapat APIM instans är du administratör redan, så du prenumererar på varje produkt som standard.

### <a name="add-an-api-to-an-existing-product"></a>Lägg till en API i en befintlig produkt

1. Välj en produkt.
2. Välj fliken API: er.
3. Klicka på **+ API**.
4. Välj en API och på **skapa**.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Using-Products/player]
> 
> 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa och publicera en produkt
> * Lägga till ett API till produkten

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Skapa tomma API och mock API-svar](mock-api-responses.md)
