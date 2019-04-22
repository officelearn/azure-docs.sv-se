---
title: Principer i Azure API Management | Microsoft Docs
description: Lär dig mer om att skapa, redigera och konfigurera principer i API Management.
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
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: 99f756b5415811b3d4c2ee0167f98b31c905df1a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58793691"
---
# <a name="policies-in-azure-api-management"></a>Principer i Azure API Management

I Azure API Management (APIM) är principer en kraftfull funktion i systemet som gör att utgivaren kan ändra beteendet för API: ets konfiguration. Principer är en samling med instruktioner som körs sekventiellt på begäran eller en API-svar. Populära instruktioner är Formatkonvertering från XML till JSON och begränsning för att begränsa antalet inkommande anrop från en utvecklare av anropsfrekvens. Många fler principer är tillgängliga direkt.

Principer som används i den gateway som är placerad mellan API konsumenten och hanterade API: et. Gatewayen tar emot alla begäranden och vanligtvis vidarebefordrar dem utan ändringar i underliggande API: et. Men en princip kan tillämpa ändringar på både inkommande begäran och utgående svar.

Principuttryck kan användas som attributvärden eller textvärden i API Management-principer, under förutsättning att principen tillåter det. Vissa principer som den [Åtkomstkontrollflödet] [ Control flow] och [ange variabel] [ Set variable] baseras på principuttryck. Mer information finns i [avancerade principer] [ Advanced policies] och [principuttryck][Policy expressions].

## <a name="sections"> </a>Förstå principkonfiguration

Principdefinitionen är en enkel XML-dokument som beskriver en sekvens av inkommande och utgående uttryck. XML-filen kan redigeras direkt i definitionsfönstret. En lista över uttryck har angetts till höger och instruktioner gäller för den aktuella omfattningen är aktiverade och markerat.

Att klicka på en aktiverad instruktion lägger till rätt XML-filen på platsen för markören i vyn definition. 

> [!NOTE]
> Om den princip som du vill lägga till inte är aktiverad kan du kontrollera att du är i rätt omfattning för principen. Varje Principframställning är avsedd för användning i vissa områden och principen avsnitt. Om du vill granska princip avsnitt och omfång för en princip, kontrollera den **användning** för principen i den [principreferens][Policy Reference].
> 
> 

Konfigurationen är uppdelad i `inbound`, `backend`, `outbound`, och `on-error`. Antal angivna principrapporter är körs för en begäran och svaret.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

Om det finns ett fel under bearbetningen av en begäran, alla eventuella resterande steg i den `inbound`, `backend`, eller `outbound` avsnitt hoppas över och körningen hoppar till instruktionerna i den `on-error` avsnittet. Genom att placera principrapporter i den `on-error` avsnitt hittar du felet med hjälp av den `context.LastError` egenskapen inspektera och anpassa fel svar med den `set-body` principen, och konfigurera vad händer om ett fel inträffar. Det finns felkoder för inbyggda åtgärder och fel som kan uppstå under bearbetning av principrapporter. Mer information finns i [felhantering i API Management-principer](/azure/api-management/api-management-error-handling-policies).

## <a name="scopes"> </a>Så här konfigurerar du principer

Information om hur du konfigurerar principer finns i [ange eller redigera principer](set-edit-policies.md).

## <a name="policy-reference"></a>Principreferens

Se den [principreferens](api-management-policy-reference.md) för en fullständig lista över principrapporter och deras inställningar.

## <a name="policy-samples"></a>Princip-exempel

Se [princip exempel](policy-samples.md) mer kodexempel.

## <a name="examples"></a>Exempel

### <a name="apply-policies-specified-at-different-scopes"></a>Tillämpa principer som angetts i olika omfång

Om du har en princip på global nivå och en princip som konfigurerats för ett API kan sedan tillämpas varje gång et används båda principerna. API Management möjliggör deterministisk sorteringen av kombinerade principrapporter via baselementet. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

I ovanstående exempel principdefinitionen den `cross-domain` instruktionen skulle köras innan eventuella högre principer som skulle ha gjort i tur och ordning följas av den `find-and-replace` principen. 

### <a name="restrict-incoming-requests"></a>Begränsa inkommande begäranden

Lägg till en ny rapport för att begränsa inkommande begäranden till den angivna IP-adresser genom att placera markören precis mellan innehållet i den `inbound` XML-elementet och klicka på den **begränsa anroparen IP-adresser** instruktionen.

![Principer för begränsning av][policies-restrict]

Detta lägger till en XML-kodfragment till den `inbound` element som innehåller råd om hur du konfigurerar instruktionen.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Om du vill begränsa inkommande begäranden och acceptera ändra endast de från IP-adressen 1.2.3.4 XML-filen på följande sätt:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Nästa steg

Arbeta med principer, Läs mer:

+ [Transformera API: er](transform-api.md)
+ [Principreferens för](api-management-policy-reference.md) för en fullständig lista över principrapporter och deras inställningar
+ [Princip-exempel](policy-samples.md)   

[Policy Reference]: api-management-policy-reference.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: api-management-howto-add-operations.md

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
