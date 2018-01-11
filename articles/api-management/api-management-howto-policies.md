---
title: Principer i Azure API Management | Microsoft Docs
description: "Lär dig mer om att skapa, redigera och konfigurera principer i API-hantering."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: 8576d590568a24df4c3320ec283edba7a28ab032
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="policies-in-azure-api-management"></a>Principer i Azure API Management

I Azure API Management (APIM) är-principer en kraftfull funktion för systemet som tillåter utgivaren för att ändra funktionssättet för API: et genom konfigurationen. Principer är en samling av instruktioner som utförs i tur och ordning på begäran eller svar på en API. Populära instruktioner med Formatkonvertering från XML till JSON och anropa hastighet begränsa om du vill begränsa mängden inkommande samtal från en utvecklare. Många fler principer är tillgängliga direkt.

Principerna tillämpas i gatewayen som placeras mellan API-klienten och hanterade API. Gatewayen tar emot alla förfrågningar och vanligtvis vidarebefordrar dem utan ändringar underliggande API: et. En princip kan dock använda ändringar till både inkommande begäran och utgående svar.

Principuttryck kan användas som attributvärden eller textvärden i API Management-principer, under förutsättning att principen tillåter det. Vissa principer som den [Åtkomstkontrollflödet] [ Control flow] och [ange variabel] [ Set variable] principer är baserade på principuttrycken. Mer information finns i [avancerade principer] [ Advanced policies] och [principuttrycken][Policy expressions].

## <a name="sections"></a>Förstå principkonfiguration

Principdefinitionen är ett enkelt XML-dokument som beskriver en sekvens av inkommande och utgående rapporter. XML-filen kan redigeras direkt i definitionsfönstret. En lista över uttryck har angetts till höger och instruktioner gäller för den aktuella omfattningen är aktiverad och markerat.

Klicka på en aktiverad instruktion lägger till rätt XML vid för markören i vyn definition. 

> [!NOTE]
> Se till att du är i rätt omfattning för principen om den princip som du vill lägga till inte är aktiverad. Varje Principframställning är avsedd för användning i vissa scope och principen avsnitt. Om du vill granska princip avsnitt och scope för en princip, kontrollera den **användning** avsnittet för grupprincipobjekt i den [principreferens][Policy Reference].
> 
> 

Konfigurationen är indelat i `inbound`, `backend`, `outbound`, och `on-error`. Antal angivna principrapporter är kör för en begäran och ett svar.

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

Om det finns ett fel under bearbetningen av en begäran, alla eventuella resterande steg i den `inbound`, `backend`, eller `outbound` avsnitt hoppas över och körning som leder till instruktionerna i den `on-error` avsnitt. Genom att placera principrapporter i den `on-error` avsnitt som du kan granska felet med hjälp av den `context.LastError` egenskapen inspektera och anpassa den fel svar med hjälp av den `set-body` principen, och konfigurera vad händer om ett fel inträffar. Det finns felkoder för inbyggda steg och fel som kan uppstå under bearbetning av principrapporter. Mer information finns i [felhantering i API Management-principer](https://msdn.microsoft.com/library/azure/mt629506.aspx).

## <a name="scopes"></a>Hur du konfigurerar principer

Mer information om hur du konfigurerar principer finns [ange eller redigera principer](set-edit-policies.md).

## <a name="policy-reference"></a>Principreferens

Finns det [principreferens](api-management-policy-reference.md) för en fullständig lista över principrapporter och deras inställningar.

## <a name="policy-samples"></a>Princip-exempel

Se [princip prover](policy-samples.md) fler kodexempel.

## <a name="examples"></a>Exempel

### <a name="apply-policies-specified-at-different-scopes"></a>Tillämpa principer som anges på olika omfång

Om du har en princip på global nivå och en princip som konfigurerats för ett API som sedan tillämpas varje gång det specifika API används båda principerna. API Management gör deterministiska sorteringen av kombinerade hanteringsprinciper via baselementet. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

I ovanstående exempel principdefinitionen den `cross-domain` instruktionen skulle köras innan de högre principer som i sin tur följas av den `find-and-replace` princip. 

### <a name="restrict-incoming-requests"></a>Begränsa inkommande begäranden

Om du vill lägga till en ny rapport för att begränsa inkommande begäranden till den angivna IP-adresser, placera markören innanför innehållet i den `inbound` XML-elementet och klicka på den **begränsa anroparen IP-adresser** instruktionen.

![Principer för begränsning av][policies-restrict]

Detta lägger till en XML-fragment till den `inbound` element som innehåller information om hur du konfigurerar instruktionen.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Begränsa inkommande begäranden och godkänner endast de från IP-adressen 1.2.3.4 ändra XML-filen på följande sätt:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

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
