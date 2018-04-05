---
title: Importera och publicera ditt första API i Azure API Management | Microsoft Docs
description: Lär dig hur du importerar och publicerar ditt första API med API Management.
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
ms.date: 11/15/2017
ms.author: apimpm
ms.openlocfilehash: 26a5ab93c82b523063632c4899daadbde70dd7d8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="import-and-publish-your-first-api"></a>Importera och publicera ditt första API 

I den här kursen visas hur du importerar en OpenAPI-specifikation för serverdels-API som finns på http://conferenceapi.azurewebsites.net?format=json. Detta serverdels-API tillhandahålls av Microsoft och finns i Azure. 

När serverdels-API:et importeras i API Management (APIM) blir APIM-API:et en fasad för serverdels-API:et. När du importerar serverdels-API:et är käll-API:et och APIM-API:et identiska. Med APIM kan du anpassa fasaden efter dina behov utan att behöva röra serverdels-API:et. Mer information finns i [Transformera och skydda ditt API](transform-api.md). 

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Importera ditt första API
> * Testa API:et i Azure Portal
> * Testa API:et i utvecklarportalen

![Nytt API](./media/api-management-get-started/created-api.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importera och publicera ett serverdels-API

I det här avsnittet visas hur du importerar och publicerar en OpenAPI-specifikation för serverdels-API.
 
1. Välj **API: er** under **API-HANTERING**.
2. Välj **OpenAPI-specifikation** i listan.

    ![Skapa ett API](./media/api-management-get-started/create-api.png)

    Du kan konfigurera API-värdena under skapandet eller senare genom att gå till fliken **Inställningar**. Den röda stjärnan bredvid ett fält anger att fältet är obligatoriskt.

    |Inställning|Värde|Beskrivning|
    |---|---|---|
    |**OpenAPI-specifikation**|http://conferenceapi.azurewebsites.net?format=json|Refererar till tjänsten som implementerar API:et. API-hanteringen vidarebefordrar begäranden till den här adressen.|
    |**Visningsnamn**|*Demokonferens-API*|Om du trycker på fliken när du har angett tjänstens URL fyller APIM i det här fältet baserat på vad som finns i JSON. <br/>Det här namnet visas i utvecklarportalen.|
    |**Namn**|*demo-conference-api*|Tillhandahåller ett unikt namn för API:et. <br/>Om du trycker på fliken när du har angett tjänstens URL fyller APIM i det här fältet baserat på vad som finns i JSON.|
    |**Beskrivning**|Ange en valfri beskrivning av API: et.|Om du trycker på fliken när du har angett tjänstens URL fyller APIM i det här fältet baserat på vad som finns i JSON.|
    |**URL-schema**|*HTTPS*|Fastställer vilka protokoll som kan användas för att få åtkomst till API:et. |
    |**API URL-suffix**|*konferens*|Suffixet läggs till i API-hanteringstjänstens bas-URL. API Management skiljer API:erna åt med hjälp av deras suffix, och suffixet måste därför vara unikt för alla API:er för en viss utgivare.|
    |**Produkter**|*Obegränsat*|Produkter är associationer med en eller flera API:er. Du kan inkludera flera API:er i en produkt och erbjuda dem till utvecklare via utvecklarportalen. <br/>Du kan publicera API:et genom att associera det med en produkt (i det här exemplet *Unlimited*). Om du vill lägga till detta nya API till en produkt, så ange produktens namn (du kan också göra det senare från sidan **Inställningar**). Det här steget kan du upprepa flera gånger för om du ska lägga till API:et till flera produkter.<br/>För att få åtkomst till API:et måste utvecklarna först prenumerera på en produkt. När de prenumererar få de en prenumerationsnyckel som går att använda till alla API:er i produkten. <br/> Om du har skapat APIM-instansen är du redan administratör, vilket innebär att du prenumererar på alla produkter.<br/> Som standard medföljer två exempelprodukter varje API Management-instans: **Starter** och **Unlimited**. |
    |Vilken är versionen för det här API:et?||Om du vill ha mer versionsinformation kan du gå till [Publicera flera versioner av ditt API](api-management-get-started-publish-versions.md)|
    
    >[!NOTE]
    > Om du vill publicera API:et måste du associera det med en produkt. Du kan göra det från sidan **Inställningar**.
    
3. Välj **Skapa**.

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Testa det nya APIM API:et i Azure Portal

Du kan anropa åtgärder direkt från Azure Portal, vilket är ett enkelt sätt att visa och testa åtgärderna i ett API.  
1. Välj det API som du skapade i föregående steg (från fliken **API:er**).
2. Tryck på fliken **Test**.  ![Test-API](./media/api-management-get-started/test-api.png)
3. Klicka på **GetSpeakers**.
    Sidan visar fälten för frågeparametrar, men i det här fallet har vi inte några. Sidan visar även fält för sidhuvudena. Ett av huvudena är Ocp-Apim-prenumeration-Key, för prenumerationsnyckeln till den produkt som är associerad med det här API:et. Nyckeln fylls i automatiskt.
4. Tryck på **Skicka**.

    Serverdelen svarar med **200 OK** och några data.

## <a name="call-operation"> </a>Anropa en åtgärd från utvecklarportalen

Åtgärder kan också anropas från **utvecklarportalen** för att testa API:er. 

1. Välj **Demokonferens-API**.
2. Klicka på **GetSpeakers**.
    
    Sidan visar fälten för frågeparametrar, men i det här fallet har vi inte några. Sidan visar även fält för sidhuvudena. Ett av huvudena är Ocp-Apim-prenumeration-Key, för prenumerationsnyckeln till den produkt som är associerad med det här API:et. Om du skapade APIM-instansen är du redan administratör, vilket innebär att nyckeln fylls i automatiskt.
3. Tryck på **Testa**.
4. Tryck på **Skicka**.
    
    När en åtgärd har anropats visar utvecklarportalen svaren.  

## <a name="next-steps"> </a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Importera ditt första API
> * Testa API:et i Azure Portal
> * Testa API:et i utvecklarportalen

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Skapa och publicera en produkt](api-management-howto-add-products.md)
