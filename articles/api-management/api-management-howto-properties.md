---
title: "Hur du använder egenskaper i Azure API Management-principer"
description: "Lär dig hur du använder egenskaper i Azure API Management-principer."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 6f39b00f-cf6e-4cef-9bf2-1f89202c0bc0
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 517f434c8f7fabc1402fb938d5ff5c733b86f2fd
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Hur du använder egenskaper i Azure API Management-principer
API Management-principer är en kraftfull funktion för systemet som tillåter utgivaren för att ändra funktionssättet för API: et genom konfigurationen. Principer är en samling instruktioner som körs sekventiellt på begäran av eller efter ett svar från ett API. Principrapporter kan konstrueras med literal textvärden, principuttrycken och egenskaper. 

Varje instans för API Management-tjänsten har en egenskapssamling av nyckel/värde-par som är globala för tjänstinstansen. De här egenskaperna kan användas för att hantera konstanta strängvärden för alla API-konfiguration och principer. Varje egenskap har följande attribut.

| Attribut | Typ | Beskrivning |
| --- | --- | --- |
| Namn |Sträng |Namnet på egenskapen. Den kan innehålla endast bokstäver, siffror, period, bindestreck och understreck. |
| Värde |Sträng |Värdet för egenskapen. Kan inte vara tomt eller endast bestå av blanksteg. |
| Hemlighet |Booleskt värde |Anger om värdet är en hemlighet och ska krypteras eller inte. |
| Taggar |Strängmatris |Valfritt taggar som när det ges kan användas för att filtrera egenskapslistan. |

Egenskaper som är konfigurerade i publisher portal på den **egenskaper** fliken. I följande exempel konfigureras tre egenskaper.

![Egenskaper][api-management-properties]

Egenskapsvärden kan innehålla teckensträngar och [principuttrycken](https://msdn.microsoft.com/library/azure/dn910913.aspx). I följande tabell visas föregående tre exempel egenskaper och deras attribut. Värdet för `ExpressionProperty` är en principuttryck som returnerar en sträng som innehåller aktuellt datum och tid. Egenskapen `ContosoHeaderValue` är markerad som en hemlighet, så dess värde inte visas.

| Namn | Värde | Hemlighet | Taggar |
| --- | --- | --- | --- |
| ContosoHeader |trackingId |False |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |True |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |False | |

## <a name="to-use-a-property"></a>Att använda en egenskap
Om du vill använda en egenskap i en princip, placera egenskapsnamn i paret dubbla klammerparenteser som `{{ContosoHeader}}`som visas i följande exempel.

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

Observera att egenskapsvärden inte kan innehålla andra egenskaper medan egenskapsvärden kan innehålla principuttrycken. Om text som innehåller en referens som används för ett egenskapsvärde `Property value text {{MyProperty}}`, referens för att inte ersättas och inkluderas som en del av egenskapens värde.

## <a name="to-create-a-property"></a>Så här skapar du en egenskap
Klicka för att skapa en egenskap **Lägg till egenskap** på den **egenskaper** fliken.

![Lägg till egenskap][api-management-properties-add-property-menu]

**Namnet** och **värdet** är värden som krävs. Kontrollera om det här egenskapsvärdet är en hemlighet i **detta är en hemlighet** kryssrutan. Ange en eller flera valfria taggar för att ordna dina egenskaper och klicka på **spara**.

![Lägg till egenskap][api-management-properties-add-property]

När en ny egenskap sparas den **söka egenskapen** textruta fylls i med namnet på den nya egenskapen och den nya egenskapen visas. Om du vill visa alla egenskaper, avmarkera den **söka egenskapen** textrutan och tryck på RETUR.

![Egenskaper][api-management-properties-property-saved]

Information om hur du skapar en egenskap med hjälp av REST-API finns [skapa en egenskap med hjälp av REST-API](https://msdn.microsoft.com/library/azure/mt651775.aspx#Put).

## <a name="to-edit-a-property"></a>Så här redigerar du en egenskap
Om du vill redigera en egenskap, klickar du på **redigera** bredvid egenskapen för att redigera.

![Ändra egenskapen][api-management-properties-edit]

Gör eventuella ändringar och klicka på **spara**. Om du ändrar egenskapsnamnet uppdateras automatiskt de principer som refererar till den egenskapen om du vill använda det nya namnet.

![Ändra egenskapen][api-management-properties-edit-property]

Information om hur du redigerar en egenskap med hjälp av REST-API finns [redigera en egenskap med hjälp av REST-API](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>Ta bort en egenskap
Om du vill ta bort en egenskap, klickar du på **ta bort** bredvid egenskapen för att ta bort.

![Ta bort egenskapen][api-management-properties-delete]

Klicka på **Ja, ta bort den** att bekräfta.

![Bekräfta borttagning][api-management-delete-confirm]

> [!IMPORTANT]
> Om egenskapen refereras av principerna som du inte lyckas ta bort den tills du tar bort egenskapen från alla principer som använder den.
> 
> 

Mer information om du tar bort en egenskap med hjälp av REST-API finns [ta bort en egenskap med hjälp av REST-API](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-properties"></a>Att söka och filtrera egenskaper
Den **egenskaper** innehåller sökning och filtrering funktioner som hjälper dig att hantera dina egenskaper. Om du vill filtrera egenskapslistan av egenskapsnamn, ange ett sökvillkor i den **söka egenskapen** textruta. Om du vill visa alla egenskaper, avmarkera den **söka egenskapen** textrutan och tryck på RETUR.

![Söka][api-management-properties-search]

Om du vill filtrera egenskapslistan av värden, anger du en eller flera taggar i den **filtrera efter taggar** textruta. Om du vill visa alla egenskaper, avmarkera den **filtrera efter taggar** textrutan och tryck på RETUR.

![Filter][api-management-properties-filter]

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om hur du arbetar med principer
  * [Principer för i API-hantering](api-management-howto-policies.md)
  * [Principreferens](https://msdn.microsoft.com/library/azure/dn894081.aspx)
  * [Principuttryck](https://msdn.microsoft.com/library/azure/dn910913.aspx)

## <a name="watch-a-video-overview"></a>Titta på en videoöversikt
> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Use-Properties-in-Policies/player]
> 
> 

[api-management-properties]: ./media/api-management-howto-properties/api-management-properties.png
[api-management-properties-add-property]: ./media/api-management-howto-properties/api-management-properties-add-property.png
[api-management-properties-edit-property]: ./media/api-management-howto-properties/api-management-properties-edit-property.png
[api-management-properties-add-property-menu]: ./media/api-management-howto-properties/api-management-properties-add-property-menu.png
[api-management-properties-property-saved]: ./media/api-management-howto-properties/api-management-properties-property-saved.png
[api-management-properties-delete]: ./media/api-management-howto-properties/api-management-properties-delete.png
[api-management-properties-edit]: ./media/api-management-howto-properties/api-management-properties-edit.png
[api-management-delete-confirm]: ./media/api-management-howto-properties/api-management-delete-confirm.png
[api-management-properties-search]: ./media/api-management-howto-properties/api-management-properties-search.png
[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

