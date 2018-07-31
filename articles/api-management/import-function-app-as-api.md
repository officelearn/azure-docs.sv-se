---
title: Importera en Azure Functions-app som ett API i Azure Portal | Microsoft Docs
description: Den här självstudien visar hur du använder Azure API Management för att importera en Azure Functions-app som ett API.
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
ms.openlocfilehash: 670fa58de7155028b0f72f1f819b9f269e07b9eb
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239060"
---
# <a name="import-an-azure-functions-app-as-an-api"></a>Importera en Azure Functions-app som ett API

Den här artikeln visar hur du importerar en Azure Functions-app som ett API. Artikeln beskriver också hur du testar Azure API Management API.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Importera en Functions-app som ett API
> * Testa API:et i Azure Portal
> * Testa API:et i utvecklarportalen

## <a name="prerequisites"></a>Nödvändiga komponenter

+ Slutför snabbstarten [Skapa en Azure API Management-instans](get-started-create-service-instance.md).
+ Se till att du har en Azure Funktions-app i din prenumeration. Mer information finns i [Skapa en Functions-app](../azure-functions/functions-create-first-azure-function.md#create-a-function-app).
+ [Skapa en OpenAPI-definition](../azure-functions/functions-openapi-definition.md) för din Azure Functions-app.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"></a>Importera och publicera ett serverdels-API

1. Under **API MANAGEMENT** (API-HANTERING) väljer du **APIs** (API:er).
2. In the listan **Lägg till ett nytt API** väljer du **Functions App** (Functions-app).

    ![Functions-app](./media/import-function-app-as-api/function-app-api.png)
3. Välj **Bläddra** för att se listan över Functions-appar i din prenumeration.
4. Välj appen. API Management hittar den swagger som är associerad med den valda appen, hämtar den och importerar den. 
5. Lägg till ett API URL-suffix. Suffixet är ett namn som identifierar det här specifika API:et i den här API Management-instansen. Suffixet måste vara unikt i den här API Management-instansen.
6. Du kan publicera API:et genom att associera det med en produkt. I det här fallet används produkten **Unlimited** (Obegränsat). Om du vill att API:et ska publiceras och vara tillgängligt för utvecklare lägger du till API:et till en produkt. Du kan lägga till API:et till en produkt när du skapar API:et, eller så kan du lägga till det senare.

    Produkter är associationer med en eller flera API:er. Du kan inkludera flera API:er och erbjuda dem till utvecklare via utvecklarportalen. Utvecklare måste först prenumerera på en produkt för att få åtkomst till API:n. En utvecklare som prenumererar får en prenumerationsnyckel som går att använda till alla API:er i den produkten. Om du skapade API Management-instansen är du administratör. Administratörer prenumererar på alla produkter som standard.

    Som standard medföljer två exempelprodukter varje API Management-instans:

    * **Starter**
    * **Obegränsat**   
7. Välj **Skapa**.

## <a name="populate-azure-functions-keys-in-azure-api-management"></a>Fyll i Azure-funktionsnycklar i Azure API Management

Om importerade Azure Functions-apparna skyddas av nycklar skapar API Management automatiskt *namngivna värden* för nycklarna. API Management fyller inte i posterna med hemligheter. Utför följande steg för varje post:  

1. I API Management-instansen väljer du fliken **Namngivna värden**.
2. Välj en post och välj **Visa värde** i sidofältet.

    ![Namngivna värden](./media/import-function-app-as-api/apim-named-values.png)

3. Om den text som visas i rutan **Värde** liknar **koden för \<Azure Functions-namnet\>** går du till **Functions Apps** (Funktionsappar) och går sedan till **Functions** (Funktioner).
4. Välj **Hantera** och kopiera sedan den relevanta nyckeln baserat på din autentiseringsmetoden för din app.

    ![Functions-app – kopiera nycklar](./media/import-function-app-as-api/azure-functions-app-keys.png)

5. Klistra in nyckeln i rutan **Värde** välj sedan **Spara**.

    ![Functions-app – klistra in nyckelvärden](./media/import-function-app-as-api/apim-named-values-2.png)

## <a name="test-the-new-api-management-api-in-the-azure-portal"></a>Testa det nya API Management-API:et i Azure-portalen

Du kan anropa åtgärder direkt från Azure-portalen. Azure-portalen är ett praktiskt sätt att visa och testa åtgärderna i ett API.  

1. Välj det API som du skapade i föregående avsnitt.
2. Välj fliken **Test**.
3. Välj en åtgärd.

    Sidan visar fält för frågeparametrar och fält för sidhuvudena. Ett av huvudena är **Ocp-Apim-Subscription-Key**, för prenumerationsnyckeln till den produkt som är associerad med det här API:et. Om du skapade API Management-instansen är du redan administratör, vilket innebär att nyckeln fylls i automatiskt. 
4. Välj **Skicka**.

    Serverdelen svarar med **200 OK** och några data.

## <a name="call-operation"></a>Anropa en åtgärd från utvecklarportalen

Du kan även anropa åtgärder från utvecklarportalen för att testa API:er. 

1. Välj det API som du skapade i [Importera och publicera ett serverdels-API](#create-api).
2. Välj **Developer portal** (Utvecklarportal).

    Webbplatsen för utvecklarportalen öppnas.
3. Välj det **API** som du skapade.
4. Välj den åtgärd som du vill testa.
5. Välj **Prova**.
6. Välj **Skicka**.
    
    När en åtgärd har anropats visas **svarsstatus**, **svarshuvuden** och eventuellt **svarsinnehåll** på utvecklarportalen.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Omvandla och skydda ett publicerat API](transform-api.md)