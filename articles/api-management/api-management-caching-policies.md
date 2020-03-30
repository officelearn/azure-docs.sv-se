---
title: Cachelagringsprinciper för Azure API Management | Microsoft-dokument
description: Lär dig mer om cachelagringsprinciperna som är tillgängliga för användning i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: 06c4ede12f939e48973d3e0b502d90b848d199bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280305"
---
# <a name="api-management-caching-policies"></a>API Management caching policies (Cachelagringsprinciper i API Management)
Det här avsnittet innehåller en referens för följande API Management-principer. Information om hur du lägger till och konfigurerar principer finns [i Principer i API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="caching-policies"></a><a name="CachingPolicies"></a>Cachelagringspolicy

- Svarscacheningsprinciper
    - [Hämta från cache](api-management-caching-policies.md#GetFromCache) - Utför cachesökning och returnera ett giltigt cachelagrat svar när det är tillgängligt.
    - [Lagra till cacheminnet](api-management-caching-policies.md#StoreToCache) - Cachelagrar svar enligt den angivna cachekontrollkonfigurationen.
- Principer för värdecachelagring
    - [Hämta värde från cacheminnet](#GetFromCacheByKey) - Hämta ett cachelagrat objekt efter nyckel.
    - [Lagra värde i cacheminnet](#StoreToCacheByKey) - Lagra ett objekt i cachen efter nyckel.
    - [Ta bort värde från cacheminnet](#RemoveCacheByKey) - Ta bort ett objekt i cachen efter nyckel.

## <a name="get-from-cache"></a><a name="GetFromCache"></a>Hämta från cacheminnet
Använd `cache-lookup` principen för att utföra cachesökning och returnera ett giltigt cachelagrat svar när det är tillgängligt. Den här principen kan tillämpas i fall där svarsinnehåll förblir statiskt under en tidsperiod. Svarscachelagring minskar bandbredden och bearbetningskraven som ställs på serverdelsservern och sänker svarstiden som uppfattas av API-konsumenter.

> [!NOTE]
> Den här principen måste ha en motsvarande [Store-till-cache-princip.](api-management-caching-policies.md#StoreToCache)

### <a name="policy-statement"></a>Policyuttalande

```xml
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" caching-type="prefer-external | external | internal" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">
  <vary-by-header>Accept</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Accept-Charset</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Authorization</vary-by-header>
  <!-- should be present when allow-private-response-caching is "true"-->
  <vary-by-header>header name</vary-by-header>
  <!-- optional, can repeated several times -->
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>
  <!-- optional, can repeated several times -->
</cache-lookup>
```

### <a name="examples"></a>Exempel

#### <a name="example"></a>Exempel

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true" caching-type="internal" >
            <vary-by-query-parameter>version</vary-by-query-parameter>
        </cache-lookup>
    </inbound>
    <outbound>
        <cache-store duration="seconds" />
        <base />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Exempel med hjälp av principuttryck
Det här exemplet visar hur du konfigurerar API Management-svarscacheningsvarstid som matchar svarscachelagringen för serverdatjänsten som anges i den säkerhetskopierade tjänstens `Cache-Control` direktiv. En demonstration av att konfigurera och använda den här principen finns i [Cloud Cover Episode 177: Fler API Management-funktioner med Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) och snabbspola fram till 25:25.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Mer information finns i [Principuttryck](api-management-policy-expressions.md) och [kontextvariabel](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|cache-sökning|Rotelementet.|Ja|
|variera efter rubrik|Starta cachelagringssvar per värde för angivet huvud, till exempel Acceptera, Acceptera-Charset, Acceptera-kodning, Acceptera språk, Auktorisering, Förvänta, Från, Värd, If-Match.|Inga|
|variera efter frågeparameter|Börja cachelagringssvar per värde för angivna frågeparametrar. Ange en eller flera parametrar. Använd semikolon som avgränsare. Om ingen anges används alla frågeparametrar.|Inga|

### <a name="attributes"></a>Attribut

| Namn                           | Beskrivning                                                                                                                                                                                                                                                                                                                                                 | Krävs | Default           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| tillåta-privat-svar-cachelagring | När den `true`är inställd på kan cachelagring av begäranden som innehåller ett auktoriseringshuvud.                                                                                                                                                                                                                                                                        | Inga       | false             |
| cachelagring-typ               | Välj mellan följande värden för attributet:<br />- `internal`för att använda den inbyggda API Management-cachen,<br />- `external`om du vill använda den externa cachen enligt beskrivningen i [Använd en extern Azure-cache för Redis i Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external`om du vill använda extern cache om den är konfigurerad eller intern cache på annat sätt. | Inga       | `prefer-external` |
| nedströms-caching-typ        | Det här attributet måste anges till ett av följande värden.<br /><br /> - ingen - nedströms caching är inte tillåtet.<br />- privat - nedströms privat cachelagring är tillåtet.<br />- offentlig - privat och delad nedströms caching är tillåtet.                                                                                                          | Inga       | ingen              |
| måste-förnya                | När cachelagring i nedströms är aktiverat aktiveras eller inaktiveras `must-revalidate` attributet cachekontroll i gateway-svar.                                                                                                                                                                                                                      | Inga       | true              |
| variera mellan utvecklare              | Ställ `true` in på cachesvar per [prenumerationsnyckel](https://docs.microsoft.com/azure/api-management/api-management-subscriptions).                                                                                                                                                                                                                                                                                                         | Ja      |         False          |
| variera mellan utvecklare och grupper       | Ange `true` att cachesvar per [användargrupp](https://docs.microsoft.com/azure/api-management/api-management-howto-create-groups)ska cachelagras .                                                                                                                                                                                                                                                                                                             | Ja      |       False            |

### <a name="usage"></a>Användning
Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Policyavsnitt:** inkommande
- **Principomfattningar:** alla scope

## <a name="store-to-cache"></a><a name="StoreToCache"></a>Lagra till cache
Principen `cache-store` cachelagrar svar enligt de angivna cacheinställningarna. Den här principen kan tillämpas i fall där svarsinnehåll förblir statiskt under en tidsperiod. Svarscachelagring minskar bandbredden och bearbetningskraven som ställs på serverdelsservern och sänker svarstiden som uppfattas av API-konsumenter.

> [!NOTE]
> Den här principen måste ha en motsvarande [Get from-cacheprincip.](api-management-caching-policies.md#GetFromCache)

### <a name="policy-statement"></a>Policyuttalande

```xml
<cache-store duration="seconds" />
```

### <a name="examples"></a>Exempel

#### <a name="example"></a>Exempel

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">
            <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
        </cache-lookup>
    </inbound>
    <outbound>
        <base />
        <cache-store duration="3600" />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Exempel med hjälp av principuttryck
Det här exemplet visar hur du konfigurerar API Management-svarscacheningsvarstid som matchar svarscachelagringen för serverdatjänsten som anges i den säkerhetskopierade tjänstens `Cache-Control` direktiv. En demonstration av att konfigurera och använda den här principen finns i [Cloud Cover Episode 177: Fler API Management-funktioner med Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) och snabbspola fram till 25:25.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Mer information finns i [Principuttryck](api-management-policy-expressions.md) och [kontextvariabel](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|cache-butik|Rotelementet.|Ja|

### <a name="attributes"></a>Attribut

| Namn             | Beskrivning                                                                                                                                                                                                                                                                                                                                                 | Krävs | Default           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| varaktighet         | Tid att leva för de cachelagrade posterna, som anges i sekunder.                                                                                                                                                                                                                                                                                                   | Ja      | Ej tillämpligt               |

### <a name="usage"></a>Användning
Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Principavsnitt:** utgående
- **Principomfattningar:** alla scope

## <a name="get-value-from-cache"></a><a name="GetFromCacheByKey"></a>Hämta värde från cacheminnet
Använd `cache-lookup-value` principen för att utföra cachesökning efter nyckel och returnera ett cachelagrat värde. Nyckeln kan ha ett godtyckligt strängvärde och tillhandahålls vanligtvis med hjälp av ett principuttryck.

> [!NOTE]
> Den här principen måste ha ett motsvarande [Store-värde i cacheprincipen.](#StoreToCacheByKey)

### <a name="policy-statement"></a>Policyuttalande

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Exempel
Mer information och exempel på den här principen finns [i Anpassad cachelagring i Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|cache-uppslagsvärde|Rotelementet.|Ja|

### <a name="attributes"></a>Attribut

| Namn             | Beskrivning                                                                                                                                                                                                                                                                                                                                                 | Krävs | Default           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| cachelagring-typ | Välj mellan följande värden för attributet:<br />- `internal`för att använda den inbyggda API Management-cachen,<br />- `external`om du vill använda den externa cachen enligt beskrivningen i [Använd en extern Azure-cache för Redis i Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external`om du vill använda extern cache om den är konfigurerad eller intern cache på annat sätt. | Inga       | `prefer-external` |
| standardvärde    | Ett värde som ska tilldelas variabeln om cachenyckelsökningen resulterade i en miss. Om det här attributet `null` inte anges tilldelas det.                                                                                                                                                                                                           | Inga       | `null`            |
| key              | Cachenyckelvärde som ska användas i sökningen.                                                                                                                                                                                                                                                                                                                       | Ja      | Ej tillämpligt               |
| variabel-namn    | Namnet på [kontextvariabeln](api-management-policy-expressions.md#ContextVariables) som uppslagsvärdet tilldelas om sökningen lyckas. Om sökningen resulterar i en miss tilldelas variabeln `default-value` värdet `null`för `default-value` attributet eller , om attributet utelämnas.                                       | Ja      | Ej tillämpligt               |

### <a name="usage"></a>Användning
Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Principavsnitt:** inkommande, utgående, bakåtsträvande, på-fel
- **Principomfattningar:** alla scope

## <a name="store-value-in-cache"></a><a name="StoreToCacheByKey"></a>Butiksvärde i cacheminnet
Den `cache-store-value` utför cachelagring efter nyckel. Nyckeln kan ha ett godtyckligt strängvärde och tillhandahålls vanligtvis med hjälp av ett principuttryck.

> [!NOTE]
> Den här principen måste ha ett motsvarande [Hämta värde från cacheprincipen.](#GetFromCacheByKey)

### <a name="policy-statement"></a>Policyuttalande

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Exempel
Mer information och exempel på den här principen finns [i Anpassad cachelagring i Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|cache-butik-värde|Rotelementet.|Ja|

### <a name="attributes"></a>Attribut

| Namn             | Beskrivning                                                                                                                                                                                                                                                                                                                                                 | Krävs | Default           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| cachelagring-typ | Välj mellan följande värden för attributet:<br />- `internal`för att använda den inbyggda API Management-cachen,<br />- `external`om du vill använda den externa cachen enligt beskrivningen i [Använd en extern Azure-cache för Redis i Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external`om du vill använda extern cache om den är konfigurerad eller intern cache på annat sätt. | Inga       | `prefer-external` |
| varaktighet         | Värdet cachelagras för det angivna varaktighetsvärdet, som anges i sekunder.                                                                                                                                                                                                                                                                                 | Ja      | Ej tillämpligt               |
| key              | Cachenyckel värdet kommer att lagras under.                                                                                                                                                                                                                                                                                                                   | Ja      | Ej tillämpligt               |
| värde            | Värdet som ska cachelagras.                                                                                                                                                                                                                                                                                                                                     | Ja      | Ej tillämpligt               |
### <a name="usage"></a>Användning
Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Principavsnitt:** inkommande, utgående, bakåtsträvande, på-fel
- **Principomfattningar:** alla scope

### <a name="remove-value-from-cache"></a><a name="RemoveCacheByKey"></a>Ta bort värde från cacheminnet
Det `cache-remove-value` tar bort ett cachelagrat objekt som identifieras av dess nyckel. Nyckeln kan ha ett godtyckligt strängvärde och tillhandahålls vanligtvis med hjälp av ett principuttryck.

#### <a name="policy-statement"></a>Policyuttalande

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>Exempel

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|cache-ta bort-värde|Rotelementet.|Ja|

#### <a name="attributes"></a>Attribut

| Namn             | Beskrivning                                                                                                                                                                                                                                                                                                                                                 | Krävs | Default           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| cachelagring-typ | Välj mellan följande värden för attributet:<br />- `internal`för att använda den inbyggda API Management-cachen,<br />- `external`om du vill använda den externa cachen enligt beskrivningen i [Använd en extern Azure-cache för Redis i Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external`om du vill använda extern cache om den är konfigurerad eller intern cache på annat sätt. | Inga       | `prefer-external` |
| key              | Nyckeln till det tidigare cachelagrade värdet som ska tas bort från cacheminnet.                                                                                                                                                                                                                                                                                        | Ja      | Ej tillämpligt               |

#### <a name="usage"></a>Användning
Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .

- **Principavsnitt:** inkommande, utgående, bakåtsträvande, på-fel
- **Principomfattningar:** alla scope

## <a name="next-steps"></a>Nästa steg

Mer information om hur du arbetar med principer finns i:

+ [Principer i API-hantering](api-management-howto-policies.md)
+ [Omvandla API:er](transform-api.md)
+ [Principreferens](api-management-policy-reference.md) för en fullständig lista över policyutdrag och deras inställningar
+ [Policyexempel](policy-samples.md)
