---
title: Ange eller redigera Azure API Management-principer | Microsoft Docs
description: Det här avsnittet visar hur du ange eller redigera principer för Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2018
ms.author: apimpm
ms.openlocfilehash: 3d1847b6001ef8e32f00a4e1cd9728d5ca0662f8
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51008274"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Ange eller redigera Azure API Management-principer

Principdefinitionen är ett XML-dokument som beskriver en sekvens av inkommande och utgående uttryck. XML-filen kan redigeras direkt i definitionsfönstret. Du kan också välja en fördefinierad princip i listan som har angetts till höger om fönstret principen. Instruktionerna som gäller för den aktuella omfattningen är aktiverade och markerat. Att klicka på en aktiverad instruktion lägger till rätt XML-filen på platsen för markören i vyn definition. 

Detaljerad information om principer finns i [principer i Azure API Management](api-management-howto-policies.md).

## <a name="set-or-edit-a-policy"></a>Ange eller redigera en princip

Om du vill ange eller redigera en princip, gör du:

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Bläddra till APIM-instansen.
3. Klicka på fliken **API:er**.

    ![Redigera princip](./media/set-edit-policies/code-editor.png)

4. Välj ett av de API:er som du tidigare har importerat.
5. Välj fliken **Design**.
6. Välj en åtgärd som du vill tillämpa principen. Om du vill tillämpa principen på alla åtgärder väljer **alla åtgärder**.
7. Välj den **</>** (Kodredigerare)-ikonen i den **inkommande bearbetning** eller **utgående bearbetning** avsnittet.
8. Klistra in koden önskad princip i en av de lämpliga blocken.
         
        <policies>
             <inbound>
                 <base />
             </inbound>
             <backend>
                 <base />
             </backend>
             <outbound>
                 <base />
             </outbound>
             <on-error>
                 <base />
             </on-error>
         </policies>
 
## <a name="configure-scope"></a>Konfigurera omfång

Principer kan konfigureras globalt eller på omfånget för en produkt, API: et eller åtgärden. Om du vill börja konfigurera en princip, måste du först välja området som principen ska gälla.

Princip för scope utvärderas i följande ordning:

1. Global omfattning
2. Produktomfattningsnivå
3. API-omfång
4. Åtgärdsomfång

Instruktioner inom principer utvärderas enligt placeringen av den `base` element, om den finns. Global princip har ingen överordnad princip och använder den `<base>` elementet i den har ingen effekt.

Klicka för att visa principer i den aktuella omfattningen i principredigeraren **beräkna om gällande princip för valda omfång**.

### <a name="global-scope"></a>Global omfattning

Globala omfång har konfigurerats för **alla API: er** i APIM-instansen.

1. Logga in på den [Azure-portalen](https://portal.azure.com/) och navigera till APIM-instansen.
2. Klicka på **alla API: er**.

    ![Global omfattning](./media/api-management-howto-policies/global-scope.png)

3. Klicka på triangeln.
4. Välj **Kodredigeraren**.
5. Lägg till eller redigera principer.
6. Tryck på **Spara**. 

    Ändringarna har spridits till API Management-gatewayen omedelbart.

### <a name="product-scope"></a>Produktomfattningsnivå

Produktomfattningsnivå har konfigurerats för den valda produkten.

1. Klicka på **produkter**.

    ![Produktomfattningsnivå](./media/api-management-howto-policies/product-scope.png)

2. Välj den produkt som du vill att tillämpa principer.
3. Klicka på **principer**.
4. Lägg till eller redigera principer.
5. Tryck på **Spara**. 

### <a name="api-scope"></a>API-omfång

API-omfång har konfigurerats för **alla åtgärder** för den valda API: er.

1. Välj den **API** du vill tillämpa principer för.

    ![API-omfång](./media/api-management-howto-policies/api-scope.png)

2. Välj **Alla åtgärder**
3. Klicka på triangeln.
4. Välj **Kodredigeraren**.
5. Lägg till eller redigera principer.
6. Tryck på **Spara**. 

### <a name="operation-scope"></a>Åtgärdsomfång 

Åtgärdsomfång har konfigurerats för den valda åtgärden.

1. Välj en **API**.
2. Markera den åtgärd som du vill tillämpa principer för.

    ![Åtgärdsomfång](./media/api-management-howto-policies/operation-scope.png)

3. Klicka på triangeln.
4. Välj **Kodredigeraren**.
5. Lägg till eller redigera principer.
6. Tryck på **Spara**. 

## <a name="next-steps"></a>Nästa steg

Se följande relaterade ämnen:

+ [Transformera API: er](transform-api.md)
+ [Principreferens för](api-management-policy-reference.md) för en fullständig lista över principrapporter och deras inställningar
+ [Princip-exempel](policy-samples.md)