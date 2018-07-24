---
title: Importera en funktionsapp som ett API med Azure Portal | Microsoft Docs
description: Den här självstudien visar hur du använder API Management (APIM) för att importera en funktionsapp som ett API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/15/2018
ms.author: apimpm
ms.openlocfilehash: 0ee83446bb08e66c7f325bdd5585b8cc0484a74e
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090936"
---
# <a name="import-a-function-app-as-an-api"></a>Importera en funktionsapp som ett API

Den här artikeln visar hur du importerar en funktionsapp som ett API. Artikeln beskriver också hur du testar APIM-API.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Importera en funktionsapp som ett API
> * Testa API:et i Azure Portal
> * Testa API:et i Developer-portalen

## <a name="prerequisites"></a>Nödvändiga komponenter

+ Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)
+ Kontrollera att det finns en Azure-funktionsapp i din prenumeration. Mer information finns i [Skapa en funktionsapp](../azure-functions/functions-create-first-azure-function.md#create-a-function-app)
+ [Skapa OpenAPI-definition](../azure-functions/functions-openapi-definition.md) för din Azure-funktionsapp

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importera och publicera ett serverdels-API

1. Välj **API: er** under **API-HANTERING**.
2. Välj **Funktionsapp** från listan **Lägg till ett nytt API**.

    ![Funktionsapp](./media/import-function-app-as-api/function-app-api.png)
3. Tryck på **Bläddra** för att se listan över funktionsappar i din prenumeration.
4. Välj appen. APIM hittar den swagger som är associerad med den valda appen, hämtar den och importerar den. 
5. Lägg till ett API URL-suffix. Suffixet är ett namn som identifierar det här specifika API:et i den här APIM-instansen. Det måste vara unikt i den här APIM-instansen.
6. Du kan publicera API:et genom att associera det med en produkt. I det här fallet används den *obegränsade* produkten.  Om du vill att API:et ska publiceras och vara tillgänglig för utvecklare, lägger du till det till en produkt. Du kan göra det vid API-skapandet eller ställa in det senare.

    Produkter är associationer med en eller flera API:er. Du kan inkludera flera API:er och erbjuda dem till utvecklare via utvecklarportalen. Utvecklare måste först prenumerera på en produkt för att få åtkomst till API:n. När de prenumererar få de en prenumerationsnyckel som går att använda till alla API:er i produkten. Om du har skapat APIM-instansen är du redan administratör, så du prenumererar på alla produkter som standard.

    Som standard medföljer två exempelprodukter varje API Management-instans:

    * **Starter**
    * **Obegränsat**   
7. Välj **Skapa**.

## <a name="populate-azure-functions-keys-in-azure-api-management"></a>Fyll i Azure-funktionsnycklar i Azure API Management

Om importerade Azure-funktioner skyddas av nycklar skapar Azure API Management automatiskt **namngivna värden** för dessa, men det fyller inte posterna med hemligheter. För varje post måste du utföra stegen nedan.  

1. Navigera till fliken **Namngivna värden** i API Management-instansen.
2. Klicka på en post och tryck på **Visa värde** i sidofältet.

    ![Namngivna värden](./media/import-function-app-as-api/apim-named-values.png)

3. Om innehållet liknar *kod för {Azure-funktionsnamn}* går du till den importerade Azure Functions-appen och navigera till din Azure-funktion.
4. Gå till avsnittet **Hantera** för önskad Azure-funktion och kopiera den relevanta nyckeln baserat på din Azure-funktions autentiseringsmetod.

    ![Funktionsapp](./media/import-function-app-as-api/azure-functions-app-keys.png)

5. Klistra in nyckeln i textrutan från den **Namngivna värden** och klicka på **Spara**.

    ![Funktionsapp](./media/import-function-app-as-api/apim-named-values-2.png)

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Testa det nya APIM API:et i Azure Portal

Du kan anropa åtgärder direkt från Azure Portal, vilket är ett enkelt sätt att visa och testa åtgärderna i ett API.  

1. Välj det API som du skapade i föregående steg.
2. Tryck på fliken **Test**.
3. Välj någon åtgärd.

    Sidan visar fält för frågeparametrar och fält för sidhuvudena. Ett av huvudena är Ocp-Apim-prenumeration-Key, för prenumerationsnyckeln till den produkt som är associerad med det här API:et. Om du skapade APIM-instansen är du redan administratör, vilket innebär att nyckeln fylls i automatiskt. 
1. Tryck på **Skicka**.

    Serverdelen svarar med **200 OK** och några data.

## <a name="call-operation"> </a>Anropa en åtgärd från utvecklarportalen

Åtgärder kan också anropas från **utvecklarportalen** för att testa API:er. 

1. Välj det API som du skapade i steget Importera och publicera ett serverdels-API.
2. Tryck på **Utvecklarportalen**.

    Webbplatsen Developer-portalen öppnas.
3. Välj det **API** som du skapade.
4. Klicka på den åtgärd som du vill testa.
5. Tryck på **Testa**.
6. Tryck på **Skicka**.
    
    När en åtgärd har anropats visas **svarsstatus**, **svarshuvuden** och eventuellt **svarsinnehåll** på utvecklarportalen.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Omvandla och skydda ett publicerat API](transform-api.md)