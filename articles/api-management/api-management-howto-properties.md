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
ms.devlang: na
ms.topic: article
ms.date: 07/22/2019
ms.author: apimpm
ms.openlocfilehash: 46f4e1b3df5f1c77a57d432297685d6d1a0a14a8
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405796"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Använda namngivna värden i Azure API Management-principer

API Management-principer är en kraftfull funktion i systemet som tillåter att Azure Portal ändrar beteendet för API: et genom konfigurationen. Principer är en samling instruktioner som körs sekventiellt på begäran av eller efter ett svar från ett API. Princip satser kan konstrueras med hjälp av textuella text värden, princip uttryck och namngivna värden.

Varje API Management tjänst instans har en egenskaps samling med nyckel/värde-par, som kallas namngivna värden som är globala för tjänst instansen. Det finns ingen begränsning för antalet objekt i samlingen. Namngivna värden kan användas för att hantera konstanta sträng värden för alla API-konfigurationer och-principer. Varje namngivet värde kan ha följande attribut:

| Attribut      | type            | Beskrivning                                                                                                                         |
| -------------- | --------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | sträng          | Används för att referera till egenskapen i principer. En sträng med en till 256 tecken. Endast bokstäver, siffror, punkter och bindestreck tillåts. |
| `Value`        | sträng          | Faktiskt värde. Får inte vara tom eller bestå enbart av blank steg. Högst 4096 tecken långt.                                     |
| `Secret`       | boolean         | Anger om värdet är en hemlighet och ska krypteras eller inte.                                                            |
| `Tags`         | strängmatris | Används för att filtrera egenskaps listan. Upp till 32 taggar.                                                                                    |

![Namngivna värden](./media/api-management-howto-properties/named-values.png)

Namngivna värden kan innehålla litterala strängar och [princip uttryck](/azure/api-management/api-management-policy-expressions). Värdet för `Expression` är till exempel ett princip uttryck som returnerar en sträng som innehåller aktuellt datum och aktuell tid. Det namngivna värdet `Credential` är markerat som en hemlighet, så värdet visas inte som standard.

| Namn       | Value                      | Secret | Tags          |
| ---------- | -------------------------- | ------ | ------------- |
| Värde      | 42                         | False  | vitala siffror |
| Autentiseringsuppgift | ••••••••••••••••••••••     | Sant   | säkerhet      |
| Uttryck | @(DateTime.Now.ToString()) | False  |               |

## <a name="to-add-and-edit-a-property"></a>Lägga till och redigera en egenskap

![Lägg till en egenskap](./media/api-management-howto-properties/add-property.png)

1. Välj **API: er** under **API-HANTERING**.
2. Välj **namngivna värden**.
3. Tryck på **+ Lägg till**.

    Namn och värde är obligatoriska värden. Om det här egenskap svärdet är en hemlighet, markerar du kryss rutan det här är en hemlig kryss ruta. Ange en eller flera valfria taggar som hjälper dig att ordna dina namngivna värden och klicka på Spara.

4. Klicka på **Skapa**.

När egenskapen har skapats kan du redigera den genom att klicka på egenskapen. Om du ändrar egenskaps namnet uppdateras alla principer som refererar till egenskapen automatiskt till att använda det nya namnet.

Information om hur du redigerar en egenskap med hjälp av REST API finns i [Redigera en egenskap med hjälp av REST API](/rest/api/apimanagement/2019-01-01/property?patch).

## <a name="to-delete-a-property"></a>Ta bort en egenskap

Ta bort en egenskap genom att klicka på **ta bort** bredvid den egenskap som ska tas bort.

> [!IMPORTANT]
> Om egenskapen refereras till av några principer kan du inte ta bort den förrän du tar bort egenskapen från alla principer som använder den.

Information om hur du tar bort en egenskap med hjälp av REST API finns i [ta bort en egenskap med hjälp av REST API](/rest/api/apimanagement/2019-01-01/property/delete).

## <a name="to-search-and-filter-named-values"></a>Söka efter och filtrera namngivna värden

Fliken **namngivna värden** innehåller söknings-och filtrerings funktioner som hjälper dig att hantera dina namngivna värden. Om du vill filtrera egenskaps listan efter egenskaps namn anger du en sökterm i text rutan **Sök egenskap** . Om du vill visa alla namngivna värden avmarkerar du text rutan **Sök egenskap** och trycker på RETUR.

Om du vill filtrera egenskaps listan efter tagg-värden anger du en eller flera taggar i text rutan **Filtrera efter Taggar** . Om du vill visa alla namngivna värden avmarkerar du text rutan **Filtrera efter Taggar** och trycker på RETUR.

## <a name="to-use-a-property"></a>Använda en egenskap

Om du vill använda en egenskap i en princip placerar du egenskaps namnet inuti ett dubbelt par av `{{ContosoHeader}}`klamrar, som du ser i följande exempel:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

I det här exemplet `ContosoHeader` används som namn på en rubrik i en `set-header` princip och `ContosoHeaderValue` används som värde för den rubriken. När den här principen utvärderas under en begäran eller ett svar på API Management Gateway `{{ContosoHeader}}` och `{{ContosoHeaderValue}}` ersätts med deras respektive egenskaps värden.

Namngivna värden kan användas som fullständiga attribut eller element värden, som du ser i föregående exempel, men de kan också infogas i eller kombineras med en del av ett sträng text uttryck som visas i följande exempel:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Namngivna värden kan också innehålla princip uttryck. I följande exempel `ExpressionProperty` används.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

När den här principen utvärderas `{{ExpressionProperty}}` ersätts med dess värde:. `@(DateTime.Now.ToString())` Eftersom värdet är ett princip uttryck utvärderas uttrycket och principen fortsätter med körningen.

Du kan testa detta i Developer-portalen genom att anropa en åtgärd som har en princip med namngivna värden i omfånget. I följande exempel anropas en åtgärd med de två tidigare exempel `set-header` principerna med namngivna värden. Observera att svaret innehåller två anpassade huvuden som har kon figurer ATS med hjälp av principer med namngivna värden.

![Utvecklarportal][api-management-send-results]

Om du tittar på [API-kontrollens spårning](api-management-howto-api-inspector.md) för ett samtal som innehåller de två tidigare exempel principerna med namngivna värden, kan du se de `set-header` två principerna med de egenskaps värden som har lagts till samt utvärdering av princip uttrycket för den egenskap som innehåller princip uttrycket.

![Spårning av API-kontroll][api-management-api-inspector-trace]

Egenskaps värden kan innehålla princip uttryck och egenskaps värden får inte innehålla andra namngivna värden. Om text som innehåller en egenskaps referens används för ett egenskaps värde, `Property value text {{MyProperty}}`t. ex., ersätts inte denna egenskaps referens och tas med som en del av egenskap svärdet.

## <a name="next-steps"></a>Nästa steg

-   Lär dig mer om att arbeta med principer
    -   [Principer i API Management](api-management-howto-policies.md)
    -   [Principreferens](/azure/api-management/api-management-policies)
    -   [Principuttryck](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
