---
title: Använda namngivna värden i Azure API Management-principer
description: Lär dig hur du använder namngivna värden i Azure API Management-principer. Namngivna värden kan innehålla litterala strängar och princip uttryck.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: 3f317276ae92e6121d519553b7883677dab89705
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87852199"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Använda namngivna värden i Azure API Management-principer

API Management-principer är en kraftfull funktion i systemet som tillåter att Azure Portal ändrar beteendet för API: et genom konfigurationen. Principer är en samling instruktioner som körs sekventiellt på begäran av eller efter ett svar från ett API. Princip satser kan konstrueras med hjälp av textuella text värden, princip uttryck och namngivna värden.

Varje API Management tjänst instans har en samling nyckel/värde-par, som kallas namngivna värden som är globala för tjänst instansen. Det finns ingen begränsning för antalet objekt i samlingen. Namngivna värden kan användas för att hantera konstanta sträng värden för alla API-konfigurationer och-principer. Varje namngivet värde kan ha följande attribut:

| Attribut      | Typ            | Description                                                                                                                            |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | sträng          | Används för att referera till det namngivna värdet i principer. En sträng med en till 256 tecken. Endast bokstäver, siffror, punkter och bindestreck tillåts. |
| `Value`        | sträng          | Faktiskt värde. Får inte vara tom eller bestå enbart av blank steg. Högst 4096 tecken långt.                                        |
| `Secret`       | boolean         | Anger om värdet är en hemlighet och ska krypteras eller inte.                                                               |
| `Tags`         | strängmatris | Används för att filtrera listan med namngivna värden. Upp till 32 taggar.                                                                                    |

![Namngivna värden](./media/api-management-howto-properties/named-values.png)

Namngivna värden kan innehålla litterala strängar och [princip uttryck](./api-management-policy-expressions.md). Värdet för är till exempel `Expression` ett princip uttryck som returnerar en sträng som innehåller aktuellt datum och aktuell tid. Det namngivna värdet `Credential` är markerat som en hemlighet, så värdet visas inte som standard.

| Namn       | Värde                      | Hemlighet | Taggar          |
| ---------- | -------------------------- | ------ | ------------- |
| Värde      | 42                         | Falskt  | vitala siffror |
| Autentiseringsuppgift | ••••••••••••••••••••••     | Sant   | security      |
| Uttryck | @ (DateTime. Now. ToString ()) | Falskt  |               |

> [!NOTE]
> I stället för namngivna värden som lagras i en API Management-tjänst kan du använda värden som lagras i tjänsten [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) som det visas i det här [exemplet](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Look%20up%20Key%20Vault%20secret%20using%20Managed%20Service%20Identity.policy.xml).

## <a name="to-add-and-edit-a-named-value"></a>Lägga till och redigera ett namngivet värde

![Lägg till ett namngivet värde](./media/api-management-howto-properties/add-property.png)

1. Välj **API: er** under **API-HANTERING**.
2. Välj **namngivna värden**.
3. Tryck på **+ Lägg till**.

    Namn och värde är obligatoriska värden. Om värdet är en hemlighet, markera kryss rutan _det här är en hemlig_ kryss ruta. Ange en eller flera valfria taggar som hjälper dig att ordna dina namngivna värden och klicka på Spara.

4. Klicka på **Skapa**.

När det namngivna värdet har skapats kan du redigera det genom att klicka på det. Om du ändrar namnet på det namngivna värdet uppdateras alla principer som refererar till det namngivna värdet automatiskt till att använda det nya namnet.

## <a name="to-delete-a-named-value"></a>Ta bort ett namngivet värde

Ta bort ett namngivet värde genom att klicka på **ta bort** bredvid det namngivna värdet som ska tas bort.

> [!IMPORTANT]
> Om det namngivna värdet refereras till av alla principer kan du inte ta bort det tills du tar bort det namngivna värdet från alla principer som använder det.

## <a name="to-search-and-filter-named-values"></a>Söka efter och filtrera namngivna värden

Fliken **namngivna värden** innehåller söknings-och filtrerings funktioner som hjälper dig att hantera dina namngivna värden. Om du vill filtrera listan med namngivna värden efter namn anger du en sökterm i text rutan **Sök egenskap** . Om du vill visa alla namngivna värden avmarkerar du text rutan **Sök egenskap** och trycker på RETUR.

Om du vill filtrera listan efter tagg anger du en eller flera taggar i text rutan **Filtrera efter Taggar** . Om du vill visa alla namngivna värden avmarkerar du text rutan **Filtrera efter Taggar** och trycker på RETUR.

## <a name="to-use-a-named-value"></a>Så här använder du ett namngivet värde

Om du vill använda ett namngivet värde i en princip placerar du dess namn inuti ett dubbelt par klammerparenteser `{{ContosoHeader}}` , som du ser i följande exempel:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

I det här exemplet `ContosoHeader` används som namn på en rubrik i en `set-header` princip och `ContosoHeaderValue` används som värde för den rubriken. När den här principen utvärderas under en begäran eller ett svar på API Management Gateway `{{ContosoHeader}}` och `{{ContosoHeaderValue}}` ersätts med deras respektive värden.

Namngivna värden kan användas som fullständiga attribut eller element värden, som du ser i föregående exempel, men de kan också infogas i eller kombineras med en del av ett sträng text uttryck som visas i följande exempel: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Namngivna värden kan också innehålla princip uttryck. I följande exempel `ExpressionProperty` används.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

När den här principen utvärderas `{{ExpressionProperty}}` ersätts med dess värde: `@(DateTime.Now.ToString())` . Eftersom värdet är ett princip uttryck utvärderas uttrycket och principen fortsätter med körningen.

Du kan testa detta i Developer-portalen genom att anropa en åtgärd som har en princip med namngivna värden i omfånget. I följande exempel anropas en åtgärd med de två tidigare exempel `set-header` principerna med namngivna värden. Observera att svaret innehåller två anpassade huvuden som har kon figurer ATS med hjälp av principer med namngivna värden.

![Utvecklarportalen][api-management-send-results]

Om du tittar på [API-kontrollens spårning](api-management-howto-api-inspector.md) för ett samtal som innehåller de två tidigare exempel principerna med namngivna värden, kan du se de två `set-header` principerna med de namngivna värdena infogade samt princip uttrycks utvärderingen för det namngivna värde som innehöll princip uttrycket.

![Spårning av API-kontroll][api-management-api-inspector-trace]

Namngivna värden kan innehålla princip uttryck, de får inte innehålla andra namngivna värden. Om text som innehåller en namngiven värde referens används för ett värde, till exempel `Text: {{MyProperty}}` , kommer den här referensen inte att matchas och ersättas.

## <a name="next-steps"></a>Nästa steg

-   Lär dig mer om att arbeta med principer
    -   [Principer i API Management](api-management-howto-policies.md)
    -   [Principreferens](./api-management-policies.md)
    -   [Principuttryck](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
