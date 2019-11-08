---
title: Använda namngivna värden i Azure API Management-principer
description: Lär dig hur du använder namngivna värden i Azure API Management-principer.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/05/2019
ms.author: apimpm
ms.openlocfilehash: d11239aa49a53a90a38f2b5336d36cea6c97e9df
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824163"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Använda namngivna värden i Azure API Management-principer

API Management-principer är en kraftfull funktion i systemet som tillåter att Azure Portal ändrar beteendet för API: et genom konfigurationen. Principer är en samling instruktioner som körs sekventiellt på begäran av eller efter ett svar från ett API. Princip satser kan konstrueras med hjälp av textuella text värden, princip uttryck och namngivna värden.

Varje API Management tjänst instans har en egenskaps samling med nyckel/värde-par, som kallas namngivna värden som är globala för tjänst instansen. Det finns ingen begränsning för antalet objekt i samlingen. Namngivna värden kan användas för att hantera konstanta sträng värden för alla API-konfigurationer och-principer. Varje namngivet värde kan ha följande attribut:

| Attribut      | Typ            | Beskrivning                                                                                                                         |
| -------------- | --------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | sträng          | Används för att referera till det namngivna värdet i principer. En sträng med en till 256 tecken. Endast bokstäver, siffror, punkter och bindestreck tillåts. |
| `Value`        | sträng          | Faktiskt värde. Får inte vara tom eller bestå enbart av blank steg. Högst 4096 tecken långt.                                     |
| `Secret`       | boolesk         | Anger om värdet är en hemlighet och ska krypteras eller inte.                                                            |
| `Tags`         | strängmatris | Används för att filtrera listan med namngivna värden. Upp till 32 taggar.                                                                                    |

![Namngivna värden](./media/api-management-howto-properties/named-values.png)

Namngivna värden kan innehålla litterala strängar och [princip uttryck](/azure/api-management/api-management-policy-expressions). Värdet för `Expression` är till exempel ett princip uttryck som returnerar en sträng som innehåller aktuellt datum och aktuell tid. Det namngivna värdet `Credential` är markerat som en hemlighet, så värdet visas inte som standard.

| Namn       | Värde                      | Hemlighet | Taggar          |
| ---------- | -------------------------- | ------ | ------------- |
| Värde      | 42                         | False  | vitala siffror |
| Autentiseringsuppgift | ••••••••••••••••••••••     | True   | security      |
| Uttryck | @ (DateTime. Now. ToString ()) | False  |               |

## <a name="to-add-and-edit-a-named-value"></a>Lägga till och redigera ett namngivet värde

![Lägg till ett namngivet värde](./media/api-management-howto-properties/add-property.png)

1. Välj **API: er** under **API-HANTERING**.
2. Välj **namngivna värden**.
3. Tryck på **+ Lägg till**.

    Namn och värde är obligatoriska värden. Om värdet är en hemlighet, markera kryss rutan *det här är en hemlig* kryss ruta. Ange en eller flera valfria taggar som hjälper dig att ordna dina namngivna värden och klicka på Spara.

4. Klicka på **Skapa**.

När det namngivna värdet har skapats kan du redigera det genom att klicka på det. Om du ändrar namnet på det namngivna värdet uppdateras alla principer som refererar till det namngivna värdet automatiskt till att använda det nya namnet.

Information om hur du redigerar ett namngivet värde med hjälp av REST API finns i [Redigera ett namngivet värde med hjälp av REST API](/rest/api/apimanagement/2019-01-01/property?patch).

## <a name="to-delete-a-named-value"></a>Ta bort ett namngivet värde

Ta bort ett namngivet värde genom att klicka på **ta bort** bredvid det namngivna värdet som ska tas bort.

> [!IMPORTANT]
> Om det namngivna värdet refereras till av alla principer kan du inte ta bort det tills du tar bort det namngivna värdet från alla principer som använder det.

Information om hur du tar bort ett namngivet värde med hjälp av REST API finns i [ta bort ett namngivet värde med hjälp av REST API](/rest/api/apimanagement/2019-01-01/property/delete).

## <a name="to-search-and-filter-named-values"></a>Söka efter och filtrera namngivna värden

Fliken **namngivna värden** innehåller söknings-och filtrerings funktioner som hjälper dig att hantera dina namngivna värden. Om du vill filtrera listan med namngivna värden efter namn anger du en sökterm i text rutan **Sök egenskap** . Om du vill visa alla namngivna värden avmarkerar du text rutan **Sök egenskap** och trycker på RETUR.

Om du vill filtrera listan efter tagg anger du en eller flera taggar i text rutan **Filtrera efter Taggar** . Om du vill visa alla namngivna värden avmarkerar du text rutan **Filtrera efter Taggar** och trycker på RETUR.

## <a name="to-use-a-named-value"></a>Så här använder du ett namngivet värde

Om du vill använda ett namngivet värde i en princip placerar du dess namn inuti ett dubbelt par av klamrar som `{{ContosoHeader}}`, som du ser i följande exempel:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

I det här exemplet används `ContosoHeader` som namn på en rubrik i en `set-header` princip och `ContosoHeaderValue` används som värde för den rubriken. När den här principen utvärderas under en begäran eller ett svar på API Management gatewayen ersätts `{{ContosoHeader}}` och `{{ContosoHeaderValue}}` med deras respektive värden.

Namngivna värden kan användas som fullständiga attribut eller element värden, som du ser i föregående exempel, men de kan också infogas i eller kombineras med en del av ett sträng text uttryck som du ser i följande exempel: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Namngivna värden kan också innehålla princip uttryck. I följande exempel används `ExpressionProperty`.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

När den här principen utvärderas ersätts `{{ExpressionProperty}}` med dess värde: `@(DateTime.Now.ToString())`. Eftersom värdet är ett princip uttryck utvärderas uttrycket och principen fortsätter med körningen.

Du kan testa detta i Developer-portalen genom att anropa en åtgärd som har en princip med namngivna värden i omfånget. I följande exempel anropas en åtgärd med de två föregående exemplet `set-header` principer med namngivna värden. Observera att svaret innehåller två anpassade huvuden som har kon figurer ATS med hjälp av principer med namngivna värden.

![Utvecklarportal][api-management-send-results]

Om du tittar på [API-kontrollens spårning](api-management-howto-api-inspector.md) för ett samtal som innehåller de två tidigare exempel principerna med namngivna värden, kan du se de två `set-header` principerna med de namngivna värdena infogade samt utvärdering av princip uttryck för det namngivna värde som innehåller princip uttrycket.

![Spårning av API-kontroll][api-management-api-inspector-trace]

Namngivna värden kan innehålla princip uttryck, de får inte innehålla andra namngivna värden. Om text som innehåller en namngiven värde referens används för ett värde, till exempel `Text: {{MyProperty}}`, matchas inte den referensen och ersätts inte.

## <a name="next-steps"></a>Nästa steg

-   Lär dig mer om att arbeta med principer
    -   [Principer i API Management](api-management-howto-policies.md)
    -   [Principreferens](/azure/api-management/api-management-policies)
    -   [Principuttryck](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
