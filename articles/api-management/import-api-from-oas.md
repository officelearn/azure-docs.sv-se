---
title: "Importera en OpenAPI-specifikation som använder Azure portal | Microsoft Docs"
description: "Lär dig hur du importerar en OpenAPI specifikation med API-hantering."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: f0c77c6e959ca99698b3ea704756a6abf36147f3
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="import-an-openapi-specification"></a>Importera en OpenAPI specifikation

Den här artikeln visar hur du importerar en ”OpenAPI specifikationen” backend-API som finns på http://conferenceapi.azurewebsites.net?format=json. Detta backend-API som tillhandahålls av Microsoft och i Azure. Artikeln beskriver också hur du testar APIM-API.

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Importera en ”OpenAPI specifikationen” backend-API
> * Testa API: et i Azure-portalen
> * Testa API: N i Developer-portalen

## <a name="prerequisites"></a>Krav

Slutför följande Snabbstart: [skapa en instans av Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"></a>Import och publicera en backend-API

1. Välj **API: er** från under **API MANAGEMENT**.
2. Välj **OpenAPI specifikationen** från den **lägga till ett nytt API** lista.
    ![OpenAPI specifikation](./media/import-api-from-oas/oas-api.png)
3. Ange lämpliga inställningar. Du kan ange värdena för API när du skapar. Alternativt kan du ange några av dem senare genom att gå till den **inställningar** fliken. <br/> Om du trycker på **fliken** några (eller alla) fält hämta fyllas med information från den angivna backend-tjänsten.

    ![Skapa ett API](./media/api-management-get-started/create-api.png)

    |Inställning|Värde|Beskrivning|
    |---|---|---|
    |**OpenAPI specifikation**|http://conferenceapi.azurewebsites.NET?format=JSON|Refererar till tjänsten implementerar API: et. API-hantering vidarebefordrar begäranden till den här adressen.|
    |**Visningsnamn**|*Demo konferens API*|Om du trycker på fliken när du har angett tjänstens Webbadress, ska APIM fylla i det här fältet baserat på vad som finns i json. <br/>Det här namnet visas i Developer-portalen.|
    |**Namn**|*demo-konferens-api*|Ger ett unikt namn för API: et. <br/>Om du trycker på fliken när du har angett tjänstens Webbadress, ska APIM fylla i det här fältet baserat på vad som finns i json.|
    |**Beskrivning**|Ange en valfri beskrivning av API: et.|Om du trycker på fliken när du har angett tjänstens Webbadress, ska APIM fylla i det här fältet baserat på vad som finns i json.|
    |**URL för API-suffix**|*konferens*|Suffixet läggs till en bas-URL för API management-tjänsten. API Management särskiljer API: er av deras suffix och därmed suffixet måste vara unikt för alla API: et för en viss utgivare.|
    |**URL-schema**|*HTTPS*|Anger vilket protokoll som kan användas för åtkomst till API. |
    |**Produkter**|*Obegränsat*| Publicera API: et genom att associera API: et med en produkt. Du kan också lägga till detta nya Programmeringsgränssnitt för en produkt, ange produktnamn. Det här steget kan upprepas flera gånger för att lägga till API: et på flera produkter.<br/>Produkter är kopplingar till en eller flera API: er. Du kan innehålla ett antal API: er och erbjuda utvecklare via developer-portalen. Utvecklare måste först prenumerera på en produkt kan få åtkomst till API: et. När man prenumererar få de en prenumeration nyckel som är bra för API: er i produkten. Om du har skapat APIM instans är du administratör redan, så du prenumererar på varje produkt som standard.<br/> Som standard varje API Management-instans som levereras med två exempel produkter: **Starter** och **obegränsad**. |

4. Välj **Skapa**.

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Testa den nya APIM API i Azure-portalen

Åtgärder kan anropas direkt från Azure-portalen, vilket ger ett bekvämt sätt att visa och testa driften av ett API.  

1. Välj API som du skapade i föregående steg.
2. Tryck på den **Test** fliken.

    ![Test-API](./media/api-management-get-started/test-api.png)
1. Klicka på **GetSpeakers**.

    Sidan visar fälten för frågeparametrar men i detta fall det finns inte något. Sidan visar även fält för sidhuvudena. Ett av huvudena är ”Ocp-Apim-prenumeration-Key”, för nyckeln prenumeration av produkten som är associerad med detta API. Om du har skapat APIM instans är du administratör redan så nyckeln fylls i automatiskt. 
4. Tryck på **skicka**.

    Backend svarar med **200 OK** och vissa data.

## <a name="call-operation"></a>Anropa en åtgärd från Developer-portalen

Åtgärder kan också kallas **utvecklarportalen** att testa API: er. 

1. Välj API som du skapade i den ”Import och publicera en backend-API” steg.
2. Tryck på **utvecklarportalen**.

    ![Testa i Developer-portalen](./media/api-management-get-started/developer-portal.png)

    Webbplatsen ”Developer portal” öppnas.
3. Välj **API**.
4. Välj **demonstration konferens API**.
5. Klicka på **GetSpeakers**.
    
    Sidan visar fälten för frågeparametrar men i detta fall det finns inte något. Sidan visar även fält för sidhuvudena. Ett av huvudena är ”Ocp-Apim-prenumeration-Key”, för nyckeln prenumeration av produkten som är associerad med detta API. Om du har skapat APIM instans är du administratör redan så nyckeln fylls i automatiskt.
6. Tryck på **prova**.
7. Tryck på **skicka**.
    
    När en åtgärd har anropats visas **svarsstatus**, **svarshuvuden** och eventuellt **svarsinnehåll** på utvecklarportalen.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Transformera och skydda publicerade API](transform-api.md)
