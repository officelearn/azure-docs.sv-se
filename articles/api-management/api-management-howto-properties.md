---
title: Hur du använder egenskaper i Azure API Management-principer
description: Lär dig hur du använder egenskaper i Azure API Management-principer.
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
ms.openlocfilehash: e0559380f6d686a4e559779c4271ea85106558d6
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
ms.locfileid: "28197120"
---
# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Hur du använder egenskaper i Azure API Management-principer
API Management-principer är en kraftfull funktion för systemet som tillåter Azure portal för att ändra funktionssättet för API: et genom konfigurationen. Principer är en samling instruktioner som körs sekventiellt på begäran av eller efter ett svar från ett API. Principrapporter kan konstrueras med literal textvärden, principuttrycken och egenskaper. 

Varje instans för API Management-tjänsten har en egenskapssamling av nyckel/värde-par som är globala för tjänstinstansen. De här egenskaperna kan användas för att hantera konstanta strängvärden för alla API-konfiguration och principer. Varje egenskap kan ha följande attribut:

| Attribut | Typ | Beskrivning |
| --- | --- | --- |
| Visningsnamn |sträng |Alfanumerisk sträng som används för att hänvisa till egenskapen i principerna. |
| Värde |sträng |Värdet på egenskapen. Kan inte vara tomt eller endast bestå av blanksteg. |
|Hemlighet|boolesk|Anger om värdet är en hemlighet och ska krypteras eller inte.|
| Taggar |Strängmatris |Valfritt taggar som när det ges kan användas för att filtrera egenskapslistan. |

![Namngivna värden](./media/api-management-howto-properties/named-values.png)

Egenskapsvärden kan innehålla teckensträngar och [principuttrycken](https://msdn.microsoft.com/library/azure/dn910913.aspx). Exempelvis värdet för `ExpressionProperty` är en principuttryck som returnerar en sträng som innehåller aktuellt datum och tid. Egenskapen `ContosoHeaderValue` är markerad som en hemlighet, så dess värde inte visas.

| Namn | Värde | Hemlighet | Taggar |
| --- | --- | --- | --- |
| ContosoHeader |TrackingId |False |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |True |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |False | |

## <a name="to-add-and-edit-a-property"></a>Lägga till och redigera en egenskap

![Lägga till en egenskap](./media/api-management-howto-properties/add-property.png)

1. Välj **API: er** från under **API MANAGEMENT**.
2. Välj **namngivna värden**.
3. Tryck på **+ Lägg till**.

  Namn och värde är värden som krävs. Om det här egenskapsvärdet är en hemlighet kontrollerar du den här är en hemlig kryssruta. Ange en eller flera valfria taggar för att sortera dina egenskaper och klicka på Spara.
4. Klicka på **Skapa**.

När egenskapen har skapats kan redigera du den genom att klicka på egenskapen. Om du ändrar egenskapsnamnet uppdateras automatiskt de principer som refererar till den egenskapen om du vill använda det nya namnet.

Information om hur du redigerar en egenskap med hjälp av REST-API finns [redigera en egenskap med hjälp av REST-API](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>Ta bort en egenskap

Om du vill ta bort en egenskap, klickar du på **ta bort** bredvid egenskapen för att ta bort.

> [!IMPORTANT]
> Om egenskapen refereras av principerna som du inte lyckas ta bort den tills du tar bort egenskapen från alla principer som använder den.
> 
> 

Mer information om du tar bort en egenskap med hjälp av REST-API finns [ta bort en egenskap med hjälp av REST-API](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-properties"></a>Att söka och filtrera egenskaper

Den **namngivna värden** innehåller sökning och filtrering funktioner som hjälper dig att hantera dina egenskaper. Om du vill filtrera egenskapslistan av egenskapsnamn, ange ett sökvillkor i den **söka egenskapen** textruta. Om du vill visa alla egenskaper, avmarkera den **söka egenskapen** textrutan och tryck på RETUR.

Om du vill filtrera egenskapslistan av värden, anger du en eller flera taggar i den **filtrera efter taggar** textruta. Om du vill visa alla egenskaper, avmarkera den **filtrera efter taggar** textrutan och tryck på RETUR.

## <a name="to-use-a-property"></a>Att använda en egenskap

Om du vill använda en egenskap i en princip, placera egenskapsnamn i paret dubbla klammerparenteser som `{{ContosoHeader}}`som visas i följande exempel:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

I det här exemplet `ContosoHeader` används som namnet på en rubrik i en `set-header` principen och `ContosoHeaderValue` används som värdet för det huvudet. När den här principen utvärderas under en begäran eller ett svar till API Management-gateway `{{ContosoHeader}}` och `{{ContosoHeaderValue}}` ersättas med deras respektive egenskapsvärden.

Egenskaper som kan användas som slutförts attribut eller elementvärden som visas i föregående exempel, men de kan också läggs till eller i kombination med en del av ett textuttryck som literal som visas i följande exempel:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Egenskaper kan också innehålla princip uttryck. I följande exempel visas den `ExpressionProperty` används.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

När den här principen utvärderas `{{ExpressionProperty}}` ersätts med värdet: `@(DateTime.Now.ToString())`. Eftersom värdet är ett principuttryck, uttrycket utvärderas och principen fortsätter med körningen.

Du kan testa detta i developer-portalen genom att anropa en åtgärd som har en princip med egenskaper i sitt omfång. I följande exempel kallas en åtgärd med två föregående exempel `set-header` principer med egenskaper. Observera att svaret innehåller två anpassade huvuden som har konfigurerats med principer med egenskaper.

![Utvecklarportalen][api-management-send-results]

Om du tittar på det [API Inspector trace](api-management-howto-api-inspector.md) för samtal som innehåller de två föregående exempel principerna med egenskaper, kan du se två `set-header` principer med egenskapsvärden infogas samt uttrycksutvärdering princip för egenskapen som innehöll Principuttrycket.

![API-Inspector spårning][api-management-api-inspector-trace]

Medan egenskapsvärden kan innehålla principuttrycken får inte egenskapsvärden innehålla andra egenskaper. Om text som innehåller en referens som används för ett egenskapsvärde `Property value text {{MyProperty}}`, referens för att inte ersättas och inkluderas som en del av egenskapens värde.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om hur du arbetar med principer
  * [Principer för i API-hantering](api-management-howto-policies.md)
  * [Principreferens](https://msdn.microsoft.com/library/azure/dn894081.aspx)
  * [Principuttryck](https://msdn.microsoft.com/library/azure/dn910913.aspx)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

