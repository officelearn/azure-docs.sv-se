---
title: Importera och publicera ditt första API i Azure API Management
description: Lär dig hur du importerar ett OpenAPI-specifikations-API till Azure API Management och testar ditt API i Azure-portalen.
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
ms.date: 02/27/2020
ms.author: apimpm
ms.openlocfilehash: 886063dcf886d79ac960814f20b3789e8e3b6839
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78163524"
---
# <a name="import-and-publish-your-first-api"></a>Importera och publicera ditt första API 

Den här självstudien visar hur du importerar ett OpenAPI-specifikations-serverd-API i JSON-format till Azure API Management. Microsoft tillhandahåller serverd-API:et och [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json)är värd för det på Azure på .

När du har importerat serverd-API:et till API-hantering blir API:et för API:et för API:et för API:et för API:et för serveringsstyrning en fasad för serverd-API:et. Du kan anpassa fasaden efter dina behov i API Management utan att vidröra serverd-API:et. Mer information finns i [Transformera och skydda ditt API](transform-api.md). 

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Importera ett API till API-hantering
> * Testa API:et i Azure Portal

![Nytt API](./media/api-management-import-and-publish/created-api.png)

## <a name="prerequisites"></a>Krav

- Förstå [Azure API Management terminologi](api-management-terminology.md).
- [Skapa en Azure API Management-instans](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a><a name="create-api"> </a>Importera och publicera ett serverd-API

I det här avsnittet visas hur du importerar och publicerar ett OpenAPI Specification-serverdels-API.
 
1. I den vänstra navigeringen av API Management-instansen väljer du **API:er** från avsnittet **API Management.**
1. Markera **OpenAPI-panelen** och välj sedan **Full** på popup-skärmen.
1. På skärmen **Skapa från OpenAPI-specifikation** använder du värdena från följande tabell för att skapa ditt API.
   
   En röd stjärna bredvid ett fält i formuläret anger att fältet krävs. Du kan ange API-värden när du skapar eller senare genom att gå till fliken **Inställningar.** 
   
   ![Skapa ett API](./media/api-management-import-and-publish/create-api.png)
   
   |Inställning|Värde|Beskrivning|
   |-------|-----|-----------|
   |**OpenAPI-specifikation**|*https:\//conferenceapi.azurewebsites.net?format=json*|Tjänsten som implementerar API:et. API-hanteringen vidarebefordrar begäranden till den här adressen.|
   |**Visningsnamn**|När du har angett föregående tjänst-URL fyller API Management i det här fältet baserat på JSON.|Namnet som visas i utvecklarportalen.|
   |**Namn**|När du har angett föregående tjänst-URL fyller API Management i det här fältet baserat på JSON.|Ett unikt namn för API: et.|
   |**Beskrivning**|När du har angett föregående tjänst-URL fyller API Management i det här fältet baserat på JSON.|En valfri beskrivning av API:et.|
   |**URL-schema**|**HTTPS**|Vilka protokoll som kan användas för att komma åt API:et.|
   |**API URL-suffix**|*konferens*|Suffixet läggs till i bas-URL:en för API Management-tjänsten. API Management skiljer API:er efter suffixet, så suffixet måste vara unikt för varje API för en viss utgivare.|
   |**Produkter**|**Unlimited**|Sammanslutning av en eller flera API:er. Varje API Management-instans levereras med två exempelprodukter: **Starter** och **Unlimited**. Du publicerar ett API genom att associera API:et med en produkt, **Unlimited** i det här exemplet.<br/>Du kan inkludera flera API:er i en produkt och erbjuda dem till utvecklare via utvecklarportalen. Om du vill lägga till det här API:et i en annan produkt skriver du eller väljer produktnamnet. Upprepa det här steget om du vill lägga till API:et i flera produkter. Du kan också lägga till API:er i produkter senare på sidan **Inställningar.**<br/>För att få åtkomst till API:et måste utvecklarna först prenumerera på en produkt. När de prenumererar får de en prenumerationsnyckel som är bra för alla API i den produkten. <br/>Om du redan har skapat API Management-instansen är du administratör, så du prenumererar på alla produkter i instansen.|
   |**Taggar**| |Taggar för att ordna API:er för sökning, gruppering eller filtrering.|
   |**Vilken är versionen för det här API:et?**|Markera eller avmarkera|Mer information om versionshantering finns i [Publicera flera versioner av ditt API](api-management-get-started-publish-versions.md).|
   
   > [!NOTE]
   > Om du vill publicera API:t måste du associera det med en produkt. Det kan du göra på sidan **Inställningar.**
   
1. Välj **Skapa**.

Om du har problem med att importera en API-definition läser du [listan över kända problem och begränsningar](api-management-api-import-restrictions.md).

## <a name="test-the-new-api-in-the-azure-portal"></a>Testa det nya API:et i Azure-portalen

Du kan anropa API-åtgärder direkt från Azure-portalen, vilket är ett bekvämt sätt att visa och testa åtgärderna.

1. I den vänstra navigeringen av API Management-instansen väljer du **API:er** från avsnittet **API Management** och väljer sedan **Api för demokonferens**.
1. Välj fliken **Testa** och välj sedan **GetSpeakers**. Sidan visar **Frågeparametrar** och **rubriker**, om sådana finns. **Ocp-Apim-Subscription-Key** fylls i automatiskt för prenumerationsnyckeln som är associerad med det här API:et.
1. Välj **Skicka**.
   
   ![Testa API-karta](./media/api-management-import-and-publish/01-import-first-api-01.png)
   
   Serverdelen svarar med **200 OK** och några data.

## <a name="next-steps"></a><a name="next-steps"> </a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Importera ditt första API
> * Testa API:et i Azure Portal

Gå vidare till nästa handledning för att lära dig hur du skapar och publicerar en produkt:

> [!div class="nextstepaction"]
> [Skapa och publicera en produkt](api-management-howto-add-products.md)
