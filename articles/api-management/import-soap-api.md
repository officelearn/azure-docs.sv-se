---
title: Importera SOAP API med Azure Portal | Microsoft Docs
description: Läs hur du importerar SOAP API med API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: 359b90cc434dad04fc0296c54fcc762f3a75062d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74107656"
---
# <a name="import-soap-api"></a>Importera SOAP API

Den här artikeln visar hur du importerar en standard XML-representation av ett SOAP API. Artikeln beskriver också hur du testar APIM-API.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Importera SOAP API
> * Testa API:et i Azure Portal
> * Testa API:et i Developer-portalen

## <a name="prerequisites"></a>Krav

Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importera och publicera ett serverdels-API

1. Välj **API: er** under **API-HANTERING**.
2. Välj **WSDL** från listan **Lägg till ett nytt API**.

    ![SOAP API](./media/import-soap-api/wsdl-api.png)
3. I **WSDL-specifikationen**, anger du URL:en till ditt SOAP API.
4. Alternativknappen **SOAP-direkt** är markerad som standard. Med det här valet, kommer API:t att visas som SOAP. Kunden måste använda SOAP-regler. Om du vill ”restify” API:et, följer du stegen i [Importera ett SOAP API och konvertera det till REST](restify-soap-api.md).

    ![Direkt](./media/import-soap-api/pass-through.png)
5. Tryck tabb.

    Följande fält fyllas i med informationen från SOAP API:t: visningsnamn, namn, beskrivning.
6. Lägg till ett API URL-suffix. Suffixet är ett namn som identifierar det här specifika API:et i den här APIM-instansen. Det måste vara unikt i den här APIM-instansen.
9. Du kan publicera API:et genom att associera det med en produkt. I det här fallet används den *obegränsade* produkten.  Om du vill att API:et ska publiceras och vara tillgänglig för utvecklare, lägger du till det till en produkt. Du kan göra det vid API-skapandet eller ställa in det senare.

    Produkter är associationer med en eller flera API:er. Du kan inkludera flera API:er och erbjuda dem till utvecklare via utvecklarportalen. Utvecklare måste först prenumerera på en produkt för att få åtkomst till API:n. När de prenumererar få de en prenumerationsnyckel som går att använda till alla API:er i produkten. Om du har skapat APIM-instansen är du redan administratör, så du prenumererar på alla produkter som standard.

    Som standard medföljer två exempelprodukter varje API Management-instans:

    * **Starter**
    * **Unlimited**   
10. Välj **Skapa**.

### <a name="test-the-new-api-in-the-administrative-portal"></a>Testa det nya API:et i den administrativa portalen

Åtgärder kan anropas direkt från administratörsportalen, vilket ger ett bekvämt sätt att visa och testa åtgärderna för ett API.  

1. Välj det API som du skapade i föregående steg.
2. Tryck på fliken **Test**.
3. Välj någon åtgärd.

    Sidan visar fält för frågeparametrar och fält för sidhuvudena. Ett av huvudena är Ocp-Apim-prenumeration-Key, för prenumerationsnyckeln till den produkt som är associerad med det här API:et. Om du skapade APIM-instansen är du redan administratör, vilket innebär att nyckeln fylls i automatiskt. 
1. Tryck på **Skicka**.

    Serverdelen svarar med **200 OK** och några data.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Omvandla och skydda ett publicerat API](transform-api.md)