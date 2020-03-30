---
title: Principer i Azure API Management | Microsoft-dokument
description: Lär dig hur du skapar, redigerar och konfigurerar principer i API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: c10939b50a66cd608d27a71f02d959fbc2380f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70072312"
---
# <a name="policies-in-azure-api-management"></a>Principer i Azure API Management

I Azure API Management (APIM) är principer en kraftfull funktion i systemet som gör det möjligt för utgivaren att ändra API:s beteende via konfiguration. Principer är en samling satser som körs sekventiellt på begäran eller svaret på ett API. Populära uttalanden inkluderar formatkonvertering från XML till JSON och samtalsfrekvensbegränsning för att begränsa mängden inkommande samtal från en utvecklare. Många fler principer är tillgängliga direkt.

Principer tillämpas i gatewayen som sitter mellan API-konsumenten och det hanterade API:et. Gatewayen tar emot alla begäranden och vidarebefordrar dem vanligtvis oförändrade till det underliggande API:et. En princip kan dock tillämpa ändringar på både inkommande begäran och utgående svar.

Principuttryck kan användas som attributvärden eller textvärden i API Management-principer, under förutsättning att principen tillåter det. Vissa principer som [Kontrollflöde][Control flow] och [Ange variabel][Set variable] baseras på principuttryck. Mer information finns i [Avancerade principer][Advanced policies] och [Principuttryck][Policy expressions].

## <a name="understanding-policy-configuration"></a><a name="sections"> </a>Förstå principkonfiguration

Principdefinitionen är ett enkelt XML-dokument som beskriver en sekvens av inkommande och utgående satser. XML-koden kan redigeras direkt i definitionsfönstret. En lista över utdrag tillhandahålls till höger och uttalanden som är tillämpliga på det aktuella scopet är aktiverade och markerade.

Om du klickar på en aktiverad sats läggs lämplig XML-kod på platsen för markören i definitionsvyn. 

> [!NOTE]
> Om principen som du vill lägga till inte är aktiverad kontrollerar du att du har rätt omfång för den principen. Varje principförklaring är utformad för användning i vissa scope och principavsnitt. Om du vill granska principavsnitten och scope för en princip kontrollerar du avsnittet **Användning** för den principen i [principreferensen][Policy Reference].
> 
> 

Konfigurationen är uppdelad `inbound` `backend`i `outbound`, `on-error`, och . Serien med angivna principsatser körs för en begäran och ett svar.

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

Om det finns ett fel under bearbetningen av `inbound`en `backend`begäran `outbound` hoppas alla återstående steg i avsnitten `on-error` , eller hoppar över och körningen hoppar till satserna i avsnittet. Genom att placera `on-error` principsatser i avsnittet kan `context.LastError` du granska felet med hjälp `set-body` av egenskapen, granska och anpassa felmeddelandet med hjälp av principen och konfigurera vad som händer om ett fel inträffar. Det finns felkoder för inbyggda steg och för fel som kan uppstå under bearbetningen av principuttalanden. Mer information finns [i Felhantering i API Management-principer](/azure/api-management/api-management-error-handling-policies).

## <a name="how-to-configure-policies"></a><a name="scopes"> </a>Konfigurera principer

Information om hur du konfigurerar principer finns i [Ange eller redigera principer](set-edit-policies.md).

## <a name="policy-reference"></a>Principreferens

Se [principreferensen](api-management-policy-reference.md) för en fullständig lista över principuttalanden och deras inställningar.

## <a name="policy-samples"></a>Policyexempel

Mer information finns i [Principexempel.](policy-samples.md)

## <a name="examples"></a>Exempel

### <a name="apply-policies-specified-at-different-scopes"></a>Tillämpa principer som anges vid olika scope

Om du har en princip på global nivå och en princip konfigurerad för ett API, tillämpas båda principerna när det specifika API:et används. API Management möjliggör deterministisk ordning av kombinerade principsatser via baselementet. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

I exemplet principdefinition ovan, skulle `cross-domain` uttalandet verkställas innan någon högre politik `find-and-replace` som i sin tur skulle följas av principen. 

### <a name="restrict-incoming-requests"></a>Begränsa inkommande begäranden

Om du vill lägga till ett nytt uttryck för att begränsa inkommande `inbound` begäranden till angivna IP-adresser placerar du markören precis i innehållet i XML-elementet och klickar på uttrycket **Begränsa anropar-IP-adresser.**

![Begränsningsprinciper][policies-restrict]

Detta lägger till ett XML-kodavsnitt i elementet `inbound` som ger vägledning om hur du konfigurerar uttrycket.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Om du vill begränsa inkommande begäranden och endast acceptera dem från en IP-adress på 1.2.3.4 ändrar du XML-koden på följande sätt:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du arbetar med principer finns i:

+ [Omvandla API:er](transform-api.md)
+ [Principreferens](api-management-policy-reference.md) för en fullständig lista över policyutdrag och deras inställningar
+ [Policyexempel](policy-samples.md)   

[Policy Reference]: api-management-policy-reference.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: api-management-howto-add-operations.md

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
