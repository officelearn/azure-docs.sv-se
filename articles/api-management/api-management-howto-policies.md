---
title: Principer i Azure API Management | Microsoft Docs
description: "Lär dig mer om att skapa, redigera och konfigurera principer i API-hantering."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 537e5caf-708b-430e-a83f-72b70af28aa9
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 7c1f235343074ec11c635097f2b094a10f3fe781
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="policies-in-azure-api-management"></a>Principer i Azure API Management
I Azure API Management är-principer en kraftfull funktion för systemet som tillåter utgivaren för att ändra funktionssättet för API: et genom konfigurationen. Principer är en samling av instruktioner som utförs i tur och ordning på begäran eller svar på en API. Populära instruktioner med Formatkonvertering från XML till JSON och anropa hastighet begränsa om du vill begränsa mängden inkommande samtal från en utvecklare. Många fler principer är tillgängliga direkt.

Finns det [principreferens] [ Policy Reference] för en fullständig lista över principrapporter och deras inställningar.

Principerna tillämpas i gatewayen som placeras mellan API-klienten och hanterade API. Gatewayen tar emot alla förfrågningar och vanligtvis vidarebefordrar dem utan ändringar underliggande API: et. En princip kan dock använda ändringar till både inkommande begäran och utgående svar.

Principuttryck kan användas som attributvärden eller textvärden i API Management-principer, under förutsättning att principen tillåter det. Vissa principer som den [Åtkomstkontrollflödet] [ Control flow] och [ange variabel] [ Set variable] principer är baserade på principuttrycken. Mer information finns i [avancerade principer] [ Advanced policies] och [principuttrycken][Policy expressions].

## <a name="scopes"></a>Hur du konfigurerar principer
Principer kan konfigureras globalt eller i omfånget för en [produkten][Product], [API] [ API] eller [åtgärden] [Operation]. Om du vill konfigurera en princip, navigerar du till redigeraren principer i publisher-portalen.

![Principer-menyn][policies-menu]

Redigeraren principer består av tre huvudavsnitt: principområdet (överst), principdefinitionen instruktionerna lista över (höger) och där principer redigeras (vänster):

![Redigeraren för principer][policies-editor]

Du måste välja vilka principen ska gälla för att börja konfigurera en princip. På skärmbilden nedan i **Starter** produkt har valts. Observera att kvadratisk symbolen bredvid namnet på principen anger att en princip används redan på den här nivån.

![Omfång][policies-scope]

Eftersom en princip har redan tillämpats, visas konfigurationen i vyn definition.

![Konfigurera][policies-configure]

Principen visas skrivskyddad först. För att kunna redigera definition Klicka på **konfigurera principen för** åtgärd.

![Redigera][policies-edit]

Principdefinitionen är ett enkelt XML-dokument som beskriver en sekvens av inkommande och utgående rapporter. XML-filen kan redigeras direkt i definitionsfönstret. En lista över uttryck har angetts till höger och instruktioner gäller för den aktuella omfattningen är aktiverad och markerat; som framgår av de **gränsen anropa hastighet** instruktionen i skärmbilden ovan.

Klicka på en aktiverad instruktion lägger till rätt XML vid för markören i vyn definition. 

> [!NOTE]
> Se till att du är i rätt omfattning för principen om den princip som du vill lägga till inte är aktiverad. Varje Principframställning är avsedd för användning i vissa scope och principen avsnitt. Om du vill granska princip avsnitt och scope för en princip, kontrollera den **användning** avsnittet för grupprincipobjekt i den [principreferens][Policy Reference].
> 
> 

En fullständig lista över principrapporter och deras inställningar är tillgängliga i den [principreferens][Policy Reference].

Till exempel för att lägga till en ny rapport för att begränsa inkommande begäranden till den angivna IP-adresser för markören innanför innehållet i den `inbound` XML-elementet och klicka på den **begränsa anroparen IP-adresser** instruktionen.

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

![Spara][policies-save]

När du är klar konfigurerar instruktioner för principen, klickar du på **spara** och ändringarna sprids till API Management gateway omedelbart.

## <a name="sections"></a>Förstå principkonfiguration
En princip är en serie som utförs för en begäran och ett svar. Konfigurationen är korrekt indelat i `inbound`, `backend`, `outbound`, och `on-error` avsnitten som visas i följande konfiguration.

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

Eftersom principer kan anges på olika nivåer (global, produkt, api och åtgärden) kan konfigurationen du ange vilken ordning som de principdefinitionen instruktioner köra med avseende på den överordnade principen. 

Princip för scope utvärderas i följande ordning.

1. Globalt scope
2. Produkten omfång
3. API-scope
4. Åtgärden omfång

Instruktionerna i dem utvärderas enligt placeringen av den `base` element, om den finns. Globala principen har ingen överordnad princip och använder den `<base>` element i den har ingen effekt.

Till exempel om du har en princip på global nivå och en princip som konfigurerats för ett API som tillämpas sedan varje gång det specifika API används båda principerna. API Management gör deterministiska sorteringen av kombinerade hanteringsprinciper via baselementet. 

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

Klicka för att visa principer i det aktuella omfånget i Redigeraren för grupprinciper **beräkna om princip för valda omfång**.

## <a name="next-steps"></a>Nästa steg
Gå igenom följande video på principuttrycken.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

[Policy Reference]: api-management-policy-reference.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md#add-apis 
[Operation]: api-management-howto-add-operations.md

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-menu]: ./media/api-management-howto-policies/api-management-policies-menu.png
[policies-editor]: ./media/api-management-howto-policies/api-management-policies-editor.png
[policies-scope]: ./media/api-management-howto-policies/api-management-policies-scope.png
[policies-configure]: ./media/api-management-howto-policies/api-management-policies-configure.png
[policies-edit]: ./media/api-management-howto-policies/api-management-policies-edit.png
[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
[policies-save]: ./media/api-management-howto-policies/api-management-policies-save.png
