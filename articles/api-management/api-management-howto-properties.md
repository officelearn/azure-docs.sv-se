---
title: Så här använder du namngivna värden i Azure API Management-principer
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
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: 4362d0875ac2c20fc6963d404f86898a12387dad
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260929"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Så här använder du namngivna värden i Azure API Management-principer

API Management-principer är en kraftfull funktion i systemet som gör att Azure-portalen kan ändra api:ets beteende genom konfiguration. Principer är en samling instruktioner som körs sekventiellt på begäran av eller efter ett svar från ett API. Principsatser kan konstrueras med hjälp av bokstavliga textvärden, principuttryck och namngivna värden.

Varje API Management-tjänstinstans har en samling nyckel-/värdepar, som kallas namngivna värden, som är globala för tjänstinstansen. Det finns ingen påtvingad gräns för antalet artiklar i samlingen. Namngivna värden kan användas för att hantera konstanta strängvärden för alla API-konfigurationer och principer. Varje namngivet värde kan ha följande attribut:

| Attribut      | Typ            | Beskrivning                                                                                                                            |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | sträng          | Används för att referera till det namngivna värdet i principer. En sträng med ett till 256 tecken. Endast bokstäver, siffror, punkt och streck är tillåtna. |
| `Value`        | sträng          | Verkligt värde. Får inte vara tom eller bestå endast av blanktecken. Högst 4096 tecken långt.                                        |
| `Secret`       | boolean         | Avgör om värdet är en hemlighet och ska krypteras eller inte.                                                               |
| `Tags`         | strängmatris | Används för att filtrera den namngivna värdelistan. Upp till 32 taggar.                                                                                    |

![Namngivna värden](./media/api-management-howto-properties/named-values.png)

Namngivna värden kan innehålla bokstavliga strängar och [principuttryck](/azure/api-management/api-management-policy-expressions). Värdet `Expression` för är till exempel ett principuttryck som returnerar en sträng som innehåller aktuellt datum och aktuell tid. Det namngivna värdet `Credential` markeras som en hemlighet, så dess värde visas inte som standard.

| Namn       | Värde                      | Hemlighet | Taggar          |
| ---------- | -------------------------- | ------ | ------------- |
| Värde      | 42                         | False  | vitala siffror |
| Autentiseringsuppgift | ••••••••••••••••••••••     | True   | security      |
| Uttryck | @(DateTime.Now.ToString()) | False  |               |

> [!NOTE]
> I stället för namngivna värden som lagras i en API Management-tjänst kan du använda värden som lagras i [Azure Key Vault-tjänsten](https://azure.microsoft.com/services/key-vault/) enligt det här [exemplet](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Look%20up%20Key%20Vault%20secret%20using%20Managed%20Service%20Identity.policy.xml).

## <a name="to-add-and-edit-a-named-value"></a>Så här lägger du till och redigerar ett namngivet värde

![Lägga till ett namngivet värde](./media/api-management-howto-properties/add-property.png)

1. Välj **API: er** under **API-HANTERING**.
2. Välj **Namngivna värden**.
3. Tryck på **+Lägg till**.

    Namn och värde är obligatoriska värden. Om värdet är en hemlighet kontrollerar du kryssrutan _Det här är en hemlig._ Ange en eller flera valfria taggar som hjälper dig att ordna dina namngivna värden och klicka på Spara.

4. Klicka på **Skapa**.

När det namngivna värdet har skapats kan du redigera det genom att klicka på det. Om du ändrar det namngivna värdenamnet uppdateras alla principer som refererar till det namngivna värdet automatiskt för att använda det nya namnet.

Information om hur du redigerar ett namngivet värde med REST API finns i [Redigera ett namngivet värde med REST API](/rest/api/apimanagement/2019-12-01/property?patch).

## <a name="to-delete-a-named-value"></a>Så här tar du bort ett namngivet värde

Om du vill ta bort ett namngivet värde klickar du på **Ta bort** bredvid det namngivna värdet som ska tas bort.

> [!IMPORTANT]
> Om det namngivna värdet refereras av några principer kan du inte ta bort det förrän du tar bort det namngivna värdet från alla principer som använder det.

Information om hur du tar bort ett namngivet värde med REST API finns i [Ta bort ett namngivet värde med REST API](/rest/api/apimanagement/2019-12-01/property/delete).

## <a name="to-search-and-filter-named-values"></a>Så här söker du efter och filtrerar namngivna värden

På fliken **Namngivna värden** ingår sök- och filtreringsfunktioner som hjälper dig att hantera namngivna värden. Om du vill filtrera listan med namngivna värden efter namn anger du en sökterm i textrutan **Sökgene.** Om du vill visa alla namngivna värden avmarkerar du textrutan **Sökgene** och trycker på Retur.

Om du vill filtrera listan efter tagg anger du en eller flera taggar i **textrutan Filter efter taggar.** Om du vill visa alla namngivna värden **avmarkerar du textrutan Filter efter taggar** och trycker på Retur.

## <a name="to-use-a-named-value"></a>Så här använder du ett namngivet värde

Om du vill använda ett namngivet värde i en princip `{{ContosoHeader}}`placerar du namnet i ett dubbelt par klammerpar som , som visas i följande exempel:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

I det `ContosoHeader` här exemplet används det som `set-header` namn på `ContosoHeaderValue` ett huvud i en princip och används som värdet för det huvudet. När den här principen utvärderas under en begäran `{{ContosoHeader}}` eller `{{ContosoHeaderValue}}` svar på API Management gateway och ersätts med sina respektive värden.

Namngivna värden kan användas som fullständiga attribut- eller elementvärden som visas i föregående exempel, men de kan också infogas i eller kombineras med en del av ett litteralt textuttryck som visas i följande exempel:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Namngivna värden kan också innehålla principuttryck. I följande exempel `ExpressionProperty` används den.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

När den här principen `{{ExpressionProperty}}` utvärderas ersätts `@(DateTime.Now.ToString())`den med dess värde: . Eftersom värdet är ett principuttryck utvärderas uttrycket och principen fortsätter med körningen.

Du kan testa detta i utvecklarportalen genom att anropa en åtgärd som har en princip med namngivna värden i omfånget. I följande exempel anropas en åtgärd med `set-header` de två föregående exempelprinciperna med namngivna värden. Observera att svaret innehåller två anpassade rubriker som har konfigurerats med principer med namngivna värden.

![Utvecklarportalen][api-management-send-results]

Om du tittar på [API Inspector-spårningen](api-management-howto-api-inspector.md) för ett anrop som innehåller de `set-header` två föregående exempelprinciperna med namngivna värden, kan du se de två principerna med namngivna värden infogade samt principuttrycksutvärderingen för det namngivna värdet som innehöll principuttrycket.

![API-inspektörsspårning][api-management-api-inspector-trace]

Namngivna värden kan innehålla principuttryck, men de kan inte innehålla andra namngivna värden. Om text som innehåller en namngiven värdereferens `Text: {{MyProperty}}`används för ett värde, till exempel , matchas och ersätts inte den referensen.

## <a name="next-steps"></a>Nästa steg

-   Läs mer om hur du arbetar med principer
    -   [Principer i API-hantering](api-management-howto-policies.md)
    -   [Principreferens](/azure/api-management/api-management-policies)
    -   [Principuttryck](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
