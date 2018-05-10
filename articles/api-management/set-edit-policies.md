---
title: Hur man eller redigera Azure API Management-principer | Microsoft Docs
description: Det här avsnittet visar hur du ange eller redigera Azure API Management-principer.
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: aaf86a440328e27c8c47b809536951eeaf2104b9
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Hur man eller redigera Azure API Management-principer

Principdefinitionen är ett XML-dokument som beskriver en sekvens av inkommande och utgående rapporter. XML-filen kan redigeras direkt i definitionsfönstret. Du kan också välja en fördefinierad princip i listan som har angetts till höger i fönstret princip. Instruktioner gäller för den aktuella omfattningen är aktiverat och markerat. Om du klickar på uttrycket aktiverad läggs rätt XML vid för markören i vyn definition. 

Detaljerad information om principer finns [principer i Azure API Management](api-management-howto-policies.md).

## <a name="set-or-edit-a-policy"></a>Ange eller redigera en princip

Om du vill ange eller redigera en princip, gör du:

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Bläddra till APIM-instansen.
3. Klicka på den **API: er** fliken.
4. Välj en av de API: er som du tidigare har importerats.
5. Välj den **Design** fliken.
6. Välj en åtgärd som du vill tillämpa principen. Om du vill tillämpa principen på alla åtgärder, väljer **alla åtgärder**.
7. Klicka på triangeln bredvid den **inkommande** eller **utgående** blyertspennor.
8. Välj den **redigerare** objekt.

    ![Redigera princip](./media/set-edit-policies/set-edit-policies01.png)

9. Klistra in koden önskad princip i en lämplig block.
         
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
 
## <a name="configure-scope"></a>Konfigurera scope

Principer kan konfigureras globalt eller i omfånget för en produkt, API eller åtgärden. Om du vill börja konfigurera en princip, måste du välja vilka principen ska gälla.

Princip för scope utvärderas i följande ordning:

1. Globalt scope
2. Produkten omfång
3. API-scope
4. Åtgärden omfång

Uttrycken inom principer utvärderas enligt placeringen av den `base` element, om den finns. Globala principen har ingen överordnad princip och använder den `<base>` element i den har ingen effekt.

Klicka för att visa principer i det aktuella omfånget i Redigeraren för grupprinciper **beräkna om princip för valda omfång**.

### <a name="global-scope"></a>Globalt scope

Globalt scope har konfigurerats för **alla API: er** i din APIM-instans.

1. Logga in på den [Azure-portalen](https://portal.azure.com/) och navigera till din APIM-instans.
2. Klicka på **alla API: er**.

    ![Globalt scope](./media/api-management-howto-policies/global-scope.png)

3. Klicka på triangelikonen.
4. Välj **Kodredigeraren**.
5. Lägg till eller redigera principer.
6. Tryck på **spara**. 

    Ändringarna sprids till API Management gateway omedelbart.

### <a name="product-scope"></a>Produkten omfång

Produkten scope har konfigurerats för den valda produkten.

1. Klicka på **produkter**.

    ![Produkten omfång](./media/api-management-howto-policies/product-scope.png)

2. Välj den produkt som du vill tillämpa principer.
3. Klicka på **principer**.
4. Lägg till eller redigera principer.
5. Tryck på **spara**. 

### <a name="api-scope"></a>API-scope

API-scope har konfigurerats för **alla åtgärder** för det valda API: T.

1. Välj den **API** du vill tillämpa principer för.

    ![API-scope](./media/api-management-howto-policies/api-scope.png)

2. Välj **alla åtgärder**
3. Klicka på triangelikonen.
4. Välj **Kodredigeraren**.
5. Lägg till eller redigera principer.
6. Tryck på **spara**. 

### <a name="operation-scope"></a>Åtgärden omfång 

Åtgärden scope har konfigurerats för den valda åtgärden.

1. Välj en **API**.
2. Markera den åtgärd som du vill tillämpa principer för.

    ![Åtgärden omfång](./media/api-management-howto-policies/operation-scope.png)

3. Klicka på triangelikonen.
4. Välj **Kodredigeraren**.
5. Lägg till eller redigera principer.
6. Tryck på **spara**. 

## <a name="next-steps"></a>Nästa steg

Se följande Närliggande ämnen:

+ [Transformera API: er](transform-api.md)
+ [Principreferens för](api-management-policy-reference.md) för en fullständig lista över principrapporter och deras inställningar
+ [Princip-exempel](policy-samples.md)