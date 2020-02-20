---
title: Importera en OpenAPI-specifikation som använder Azure Portal | Microsoft Docs
description: Läs om hur du importerar en OpenAPI-specifikation med API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: apimpm
ms.openlocfilehash: 57803ec9889cb6a19dae6d6d1070d8381577aff0
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77468396"
---
# <a name="import-an-openapi-specification"></a>Importera en OpenAPI-specifikation

I den här artikeln visas hur du importerar en OpenAPI-specifikation för serverdels-API som finns på https://conferenceapi.azurewebsites.net?format=json. Detta serverdels-API tillhandahålls av Microsoft och finns i Azure. Artikeln beskriver också hur du testar APIM-API.

> [!IMPORTANT]
> Det här [dokumentet](https://azure.microsoft.com/blog/announcing-the-preview-of-openapi-specification-v3-support-in-azure-api-management/) innehåller viktig information och tips om OpenAPI-import.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Importerar en OpenAPI-specifikation för serverdels-API
> * Testa API:et i Azure Portal
> * Testa API:et i utvecklarportalen

## <a name="prerequisites"></a>Förutsättningar

Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importera och publicera ett Server dels-API

1. Välj **API: er** under **API-HANTERING**.
2. Välj **OpenAPI-specifikation** i listan **Lägg till ett nytt API**.

    ![OpenAPI-specifikation](./media/import-api-from-oas/oas-api.png)
3. Ange lämpliga inställningar. Du kan ange alla API-värden när du skapar specifikationen. Du kan också ange en del värden senare på fliken **Inställningar**. <br/> Om du trycker på **Tabb** fylls några (eller alla) fält i med information från den angivna serverdelstjänsten.

    ![Skapa ett API](./media/api-management-get-started/create-api.png)

    |Inställning|Värde|Beskrivning|
    |---|---|---|
    |**OpenAPI-specifikation**|https://conferenceapi.azurewebsites.net?format=json|Refererar till tjänsten som implementerar API:et. API-hanteringen vidarebefordrar begäranden till den här adressen.|
    |**Visningsnamn**|*Demokonferens-API*|Om du trycker på fliken när du har angett tjänstens URL fyller APIM i det här fältet baserat på vad som finns i JSON. <br/>Det här namnet visas i Developer-portalen.|
    |**Namn**|*demo-conference-api*|Tillhandahåller ett unikt namn för API:et. <br/>Om du trycker på fliken när du har angett tjänstens URL fyller APIM i det här fältet baserat på vad som finns i JSON.|
    |**Beskrivning**|Ange en valfri beskrivning av API: et.|Om du trycker på fliken när du har angett tjänstens URL fyller APIM i det här fältet baserat på vad som finns i JSON.|
    |**API URL-suffix**|*konferens*|Suffixet läggs till i API-hanteringstjänstens bas-URL. API Management skiljer API:erna åt med hjälp av deras suffix, och suffixet måste därför vara unikt för alla API:er för en viss utgivare.|
    |**URL-schema**|*HTTPS*|Fastställer vilka protokoll som kan användas för att få åtkomst till API:et. |
    |**Produkter**|*Obegränsat*| Du kan publicera API:et genom att associera det med en produkt. Om du vill lägga till det nya API:et till en produkt anger du produktnamnet. Det här steget kan du upprepa flera gånger för om du ska lägga till API:et till flera produkter.<br/>Produkter är associationer med en eller flera API:er. Du kan inkludera flera API:er och erbjuda dem till utvecklare via utvecklarportalen. Utvecklare måste först prenumerera på en produkt för att få åtkomst till API:n. När de prenumererar få de en prenumerationsnyckel som går att använda till alla API:er i produkten. Om du har skapat APIM-instansen är du redan administratör, så du prenumererar på alla produkter som standard.<br/> Som standard medföljer två exempelprodukter varje API Management-instans: **Starter** och **Unlimited**. |

4. Välj **Skapa**.

> [!NOTE]
> Begränsningar för API-import finns dokumenterade i [en annan artikel](api-management-api-import-restrictions.md).

## <a name="test-the-new-api-in-the-azure-portal"></a>Testa det nya API: et i Azure Portal

![Testa API-karta](./media/api-management-get-started/01-import-first-api-01.png)

Du kan anropa åtgärder direkt från Azure Portal, vilket är ett enkelt sätt att visa och testa åtgärderna i ett API.

1. Välj det API som du skapade i föregående steg (från fliken **API:er**).
2. Tryck på fliken **Test**.
3. Klicka på **GetSpeakers**. På sidan visas fälten för frågeparametrar (inga i det här fallet) och huvuden. Ett av huvudena är Ocp-Apim-prenumeration-Key, för prenumerationsnyckeln till den produkt som är associerad med det här API:et. Nyckeln fylls i automatiskt.
4. Tryck på **Skicka**.

    Serverdelen svarar med **200 OK** och några data.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Omvandla och skydda ett publicerat API](transform-api.md)
