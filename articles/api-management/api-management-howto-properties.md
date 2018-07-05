---
title: Hur du använder med namnet värden i Azure API Management-principer
description: Lär dig hur du använder med namnet värden i Azure API Management-principer.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: apimpm
ms.openlocfilehash: 829d6bc6cb3f8e78d065d7aaca4937634e7349c8
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437073"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Hur du använder med namnet värden i Azure API Management-principer
API Management-principer är en kraftfull funktion i systemet som tillåter Azure portal för att ändra funktionssättet för API: ets konfiguration. Principer är en samling instruktioner som körs sekventiellt på begäran av eller efter ett svar från ett API. Principrapporter kan konstrueras med literal textvärden, principuttryck, och namngivna värden. 

Varje API Management-tjänstinstans har en egenskapssamling för nyckel/värde-par, som kallas med namnet värden, som är globala för tjänstinstansen. Dessa värden med namnet kan användas för att hantera konstant strängvärden i alla API-konfiguration och principer. Varje egenskap kan ha följande attribut:

| Attribut | Typ | Beskrivning |
| --- | --- | --- |
| Visningsnamn |sträng |Alfanumerisk sträng som används för att hänvisa till egenskapen i principerna. |
| Värde |sträng |Värdet på egenskapen. Den kan inte vara tomt eller enbart bestå av blanksteg. |
|Hemlighet|boolesk|Anger om värdet är en hemlighet och ska krypteras eller inte.|
| Taggar |strängmatris |Valfritt taggar som men som tillhandahålls kan användas för att filtrera egenskapslistan. |

![Namngivna värden](./media/api-management-howto-properties/named-values.png)

Egenskapsvärden kan innehålla literala strängar och [principuttryck](https://msdn.microsoft.com/library/azure/dn910913.aspx). Till exempel värdet för `ExpressionProperty` är ett principuttryck som returnerar en sträng som innehåller den aktuella datum och tid. Egenskapen `ContosoHeaderValue` är markerad som en hemlighet, så inte visas dess värde.

| Namn | Värde | Hemlighet | Taggar |
| --- | --- | --- | --- |
| ContosoHeader |spårnings-ID |False |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |True |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |False | |

## <a name="to-add-and-edit-a-property"></a>Lägga till och redigera en egenskap

![Lägga till en egenskap](./media/api-management-howto-properties/add-property.png)

1. Välj **API: er** under **API-HANTERING**.
2. Välj **namngivna värden**.
3. Tryck på **+ Lägg till**.

  Namn och värde är värden som krävs. Om det här egenskapsvärdet är en hemlighet, kontrollerar du den här är en hemlig kryssruta. Ange en eller flera valfria taggar för att sortera dina namngivna värden och klicka på Spara.
4. Klicka på **Skapa**.

När egenskapen har skapats kan redigera du den genom att klicka på egenskapen. Om du ändrar egenskapsnamnet kan uppdateras automatiskt eventuella principer som refererar till egenskapen för att använda det nya namnet.

Information om hur du redigerar en egenskap med hjälp av REST-API finns i [redigera en egenskap med hjälp av REST-API](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>Ta bort en egenskap

Ta bort en egenskap genom att klicka på **ta bort** bredvid egenskapen att ta bort.

> [!IMPORTANT]
> Om egenskapen är refereras till av principerna som du inte togs bort förrän du tar bort egenskapen från alla principer som använder den.
> 
> 

Information om att ta bort en egenskap med hjälp av REST-API finns i [ta bort en egenskap med hjälp av REST-API](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-named-values"></a>Söka och filtrera med namnet värden

Den **namngivna värden** fliken innehåller sökning och filtrering funktioner för att hjälpa dig att hantera din namngivna värden. Om du vill filtrera egenskapslistan efter egenskapsnamn, ange en sökterm i den **Sök egenskapen** textrutan. Visa alla namngivna värden genom att avmarkera den **Sök egenskapen** textrutan och tryck på RETUR.

Om du vill filtrera egenskapslistan efter taggvärden, anger du en eller flera taggar i den **filtrera efter taggar** textrutan. Visa alla namngivna värden genom att avmarkera den **filtrera efter taggar** textrutan och tryck på RETUR.

## <a name="to-use-a-property"></a>Att använda en egenskap

Om du vill använda en egenskap i en princip, placera egenskapsnamnet inuti ett dubbla par av klammerparenteser som `{{ContosoHeader}}`, enligt följande exempel:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

I det här exemplet `ContosoHeader` används som namnet på en rubrik i en `set-header` principen och `ContosoHeaderValue` används som värde för den rubriken. När den här principen utvärderas under en begäran eller ett svar till API Management-gatewayen `{{ContosoHeader}}` och `{{ContosoHeaderValue}}` har ersatts med sina respektive egenskapsvärden.

Namngivna värden kan användas som fullständig attribut eller värden som visas i exemplet ovan, men de kan också läggs till eller kombineras med en del av ett uttryck med exakt text som visas i följande exempel: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Namngivna värden kan också innehålla principuttryck. I följande exempel visas den `ExpressionProperty` används.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

När den här principen utvärderas `{{ExpressionProperty}}` ersätts med värdet: `@(DateTime.Now.ToString())`. Eftersom värdet är ett principuttryck är uttrycket ska utvärderas och principen fortsätter med den kan körningen.

Du kan testa detta i developer-portalen genom att anropa en åtgärd som har en princip med namngivna värden i omfånget. I följande exempel kallas en åtgärd med två föregående exempel `set-header` principer med namngivna värden. Observera att svaret innehåller två anpassade sidhuvuden som konfigurerades med med namngivna värden.

![Utvecklarportalen][api-management-send-results]

Om du tittar på den [API Inspector trace](api-management-howto-api-inspector.md) för samtal som innehåller de två föregående exempel principerna med namngivna värden, kan du se två `set-header` principer med egenskapsvärden infogas samt Principuttrycket utvärdering för egenskapen som innehöll Principuttrycket.

![API Inspector spårning][api-management-api-inspector-trace]

Medan egenskapsvärden kan innehålla principuttryck får inte egenskapsvärden innehålla andra namngivna värden. Om text som innehåller en referens för egenskaper som används för ett egenskapsvärde `Property value text {{MyProperty}}`, som egenskapen refererar inte ersättas och ska ingå som en del av egenskapsvärdet.

## <a name="next-steps"></a>Nästa steg
* Läs mer om hur du arbetar med principer
  * [Principer i API Management](api-management-howto-policies.md)
  * [Principreferens](https://msdn.microsoft.com/library/azure/dn894081.aspx)
  * [Principuttryck](https://msdn.microsoft.com/library/azure/dn910913.aspx)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

