---
title: Ange eller redigera Azure API Management-principer | Microsoft-dokument
description: Det här avsnittet visar hur du anger eller redigerar Azure API Management-principer.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2018
ms.author: apimpm
ms.openlocfilehash: 2df57477ae5270405a1774b7a4f04ed185fea396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70071709"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Så anger eller redigerar du Azure API Management-principer

Principdefinitionen är ett XML-dokument som beskriver en sekvens av inkommande och utgående satser. XML-koden kan redigeras direkt i definitionsfönstret. Du kan också välja en fördefinierad princip i listan som anges till höger om principfönstret. De satser som är tillämpliga på det aktuella scopet är aktiverade och markerade. Om du klickar på en aktiverad sats läggs lämplig XML till på platsen för markören i definitionsvyn. 

Detaljerad information om principer finns [i Principer i Azure API Management](api-management-howto-policies.md).

## <a name="set-or-edit-a-policy"></a>Ange eller redigera en princip

Så här anger eller redigerar du en princip:

1. Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).
2. Bläddra till APIM-instansen.
3. Klicka på fliken **API:er**.

    ![Redigera princip](./media/set-edit-policies/code-editor.png)

4. Välj ett av de API:er som du tidigare har importerat.
5. Välj fliken **Design**.
6. Välj en åtgärd som du vill använda principen för. Om du vill tillämpa principen på alla operationer väljer du **Alla operationer**.
7. Välj **</>** ikonen (kodredigeraren) i avsnittet **Inkommande bearbetning** eller **Utgående bearbetning.**
8. Klistra in önskad principkod i ett av lämpliga block.

    ```XML
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
    ```
 
## <a name="configure-scope"></a>Konfigurera scope

Principer kan konfigureras globalt eller inom ramen för ett produkt-, API- eller åtgärdsscope. Om du vill börja konfigurera en princip måste du först välja det scope som principen ska gälla för.

Principomfattningar utvärderas i följande ordning:

1. Globalt scope
2. Produktens omfattning
3. API-scope
4. Åtgärdens omfattning

Uttalandena inom principer utvärderas enligt placeringen av elementet, `base` om det finns. Den globala principen har ingen `<base>` överordnad princip och att använda elementet i den har ingen effekt.

Om du vill visa principerna i det aktuella scopet i principredigeraren klickar du på **Beräkna om den gällande principen för valt scope**.

### <a name="global-scope"></a>Globalt scope

Globalt scope har konfigurerats för **alla API:er** i APIM-instansen.

1. Logga in på [Azure-portalen](https://portal.azure.com/) och navigera till din APIM-instans.
2. Klicka på **Alla API:er**.

    ![Globalt scope](./media/api-management-howto-policies/global-scope.png)

3. Klicka på triangelikonen.
4. Välj **Kodredigeraren**.
5. Lägg till eller redigera principer.
6. Tryck på **Spara**. 

    Ändringarna sprids till API Management-gatewayen omedelbart.

### <a name="product-scope"></a>Produktens omfattning

Produktomfattningen är konfigurerad för den valda produkten.

1. Klicka på **Produkter**.

    ![Produktens omfattning](./media/api-management-howto-policies/product-scope.png)

2. Välj den produkt som du vill använda principer för.
3. Klicka på **Principer**.
4. Lägg till eller redigera principer.
5. Tryck på **Spara**. 

### <a name="api-scope"></a>API-scope

API-scopet har konfigurerats för **alla åtgärder** för det valda API:et.

1. Välj det **API** som du vill tillämpa principer på.

    ![API-scope](./media/api-management-howto-policies/api-scope.png)

2. Välj **Alla åtgärder**
3. Klicka på triangelikonen.
4. Välj **Kodredigeraren**.
5. Lägg till eller redigera principer.
6. Tryck på **Spara**. 

### <a name="operation-scope"></a>Åtgärdens omfattning 

Åtgärdsomfånget har konfigurerats för den valda åtgärden.

1. Välj ett **API**.
2. Välj den åtgärd som du vill tillämpa principer på.

    ![Åtgärdens omfattning](./media/api-management-howto-policies/operation-scope.png)

3. Klicka på triangelikonen.
4. Välj **Kodredigeraren**.
5. Lägg till eller redigera principer.
6. Tryck på **Spara**. 

## <a name="next-steps"></a>Nästa steg

Se följande relaterade ämnen:

+ [Omvandla API:er](transform-api.md)
+ [Principreferens](api-management-policy-reference.md) för en fullständig lista över policyutdrag och deras inställningar
+ [Policyexempel](policy-samples.md)
