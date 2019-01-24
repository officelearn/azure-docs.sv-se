---
title: Azure API Management cachelagringsprinciper | Microsoft Docs
description: Läs mer om cachelagringsprinciperna tillgängliga för användning i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: 204f69ad37a7e38337c87e2053a10c96dadf380d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812666"
---
# <a name="api-management-caching-policies"></a>API Management cachelagringsprinciperna
Det här avsnittet innehåller en referens för följande API Management-principer. Information om att lägga till och konfigurerar principer finns i [principer i API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="CachingPolicies"></a> Cachelagringsprinciper

- Svaret cachelagringsprinciper
    - [Hämta från cache](api-management-caching-policies.md#GetFromCache) -utföra cache Leta upp och returnera en giltig cachelagrade svar när det är tillgängligt.
    - [Store till cache](api-management-caching-policies.md#StoreToCache) -cachelagrar svar enligt angivna cache-control-konfigurationen.
- Värdet som principer för cachelagring
    - [Hämta värdet från cachen](#GetFromCacheByKey) – hämta ett cachelagrat objekt med nyckeln.
    - [Store värdet i cache](#StoreToCacheByKey) -Store ett objekt i cacheminnet av nyckeln.
    - [Ta bort värdet från cache](#RemoveCacheByKey) -ta bort ett objekt i cachen med nyckeln.

## <a name="GetFromCache"></a> Hämta från cache
Använd den `cache-lookup` ska utföras cache Leta upp och returnera ett giltigt cachelagrade svar när det är tillgängligt. Den här principen kan tillämpas i fall där svarsinnehåll är statiskt under en viss tidsperiod. Cachelagring av svar minskar bandbredden och bearbetningskrav används på serverdelen web server och driftmiljön svarstiden uppfattas av API-kunderna.

> [!NOTE]
> Den här principen måste ha en motsvarande [Store till cache](api-management-caching-policies.md#StoreToCache) princip.

### <a name="policy-statement"></a>Principframställning

```xml
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" cache-preference="prefer-external | external | internal" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">
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
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true" cache-preference="internal" >
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
Det här exemplet visar hur du konfigurerar API Management svar lagringstiden som matchar svar cachelagring av backend-tjänsten som anges av tjänsten säkerhetskopierade `Cache-Control` direktiv. En demonstration av hur du konfigurerar och använder den här principen finns [Cloud Cover avsnittet 177: Fler API Management-funktioner med Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) och spola framåt till 25:25.

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

Mer information finns i [principuttryck](api-management-policy-expressions.md) och [sammanhangsvariabel](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|cache-sökning|Rotelement.|Ja|
|variera av rubrik|Starta cachelagring av svar per värde för angivna huvud, till exempel acceptera Accept-Charset Accept-Encoding, acceptera-språk, auktorisering, förväntade, från värden, If-Match.|Nej|
|vary-by-query-parameter|Starta cachelagring av svar per värde för angivna frågeparametrarna. Ange en eller flera parametrar. Använd semikolon som avgränsare. Om inget anges används alla Frågeparametrar.|Nej|

### <a name="attributes"></a>Attribut

| Namn                           | Beskrivning                                                                                                                                                                                                                                                                                                                                                 | Krävs | Standard           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| allow-private-response-caching | När värdet `true`, tillåter cachelagring av begäranden som innehåller ingen auktoriseringsrubrik.                                                                                                                                                                                                                                                                        | Nej       | false             |
| cache-preference               | Välj mellan följande värden för attributet:<br />- `internal` du använder inbyggda API Management-cache<br />- `external` du använder externa cache enligt beskrivningen i [använder en extern Azure Cache för Redis i Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` använda externa cachen om konfigurerat eller interna cacheminnet annars. | Nej       | `prefer-external` |
| downstream-caching-type        | Det här attributet måste anges till något av följande värden.<br /><br /> -Ingen - underordnade cachelagring är inte tillåtet.<br />-privat – underordnad privat cachelagring är tillåtet.<br />-offentliga - privata och delade underordnade cachelagring är tillåtet.                                                                                                          | Nej       | inga              |
| måste revalidate                | När underordnade cachelagring är aktiverat det här attributet aktiverar eller inaktiverar det `must-revalidate` cache-control-direktivet i gateway-svar.                                                                                                                                                                                                                      | Nej       | true              |
| vary-by-developer              | Ange `true` till cache svar per [prenumerationsnyckel](https://docs.microsoft.com/azure/api-management/api-management-subscriptions#what-is-subscriptions).                                                                                                                                                                                                                                                                                                         | Ja      |         False          |
| vary-by-developer-groups       | Ange `true` till cache svar per [användargrupp](https://docs.microsoft.com/azure/api-management/api-management-howto-create-groups).                                                                                                                                                                                                                                                                                                             | Ja      |       False            |

### <a name="usage"></a>Användning
Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Princip-avsnitt:** inkommande
- **Princip för omfång:** API: et, åtgärden, produkt

## <a name="StoreToCache"></a> Store till cache
Den `cache-store` princip cachelagrar svar enligt de angivna cache-inställningarna. Den här principen kan tillämpas i fall där svarsinnehåll är statiskt under en viss tidsperiod. Cachelagring av svar minskar bandbredden och bearbetningskrav används på serverdelen web server och driftmiljön svarstiden uppfattas av API-kunderna.

> [!NOTE]
> Den här principen måste ha en motsvarande [kom från cachen](api-management-caching-policies.md#GetFromCache) princip.

### <a name="policy-statement"></a>Principframställning

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
Det här exemplet visar hur du konfigurerar API Management svar lagringstiden som matchar svar cachelagring av backend-tjänsten som anges av tjänsten säkerhetskopierade `Cache-Control` direktiv. En demonstration av hur du konfigurerar och använder den här principen finns [Cloud Cover avsnittet 177: Fler API Management-funktioner med Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) och spola framåt till 25:25.

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

Mer information finns i [principuttryck](api-management-policy-expressions.md) och [sammanhangsvariabel](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|cache-store|Rotelement.|Ja|

### <a name="attributes"></a>Attribut

| Namn             | Beskrivning                                                                                                                                                                                                                                                                                                                                                 | Krävs | Standard           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| varaktighet         | Time-to-live posternas cachelagrade anges i sekunder.                                                                                                                                                                                                                                                                                                   | Ja      | Gäller inte               |

### <a name="usage"></a>Användning
Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Princip-avsnitt:** utgående
- **Princip för omfång:** API: et, åtgärden, produkt

## <a name="GetFromCacheByKey"></a> Hämta värdet från cachen
Använd den `cache-lookup-value` princip för att utföra cache-sökning av nyckeln och returnera ett cachelagrade värde. Nyckeln kan ha en godtycklig sträng-värde och anges vanligtvis med en principuttryck.

> [!NOTE]
> Den här principen måste ha en motsvarande [Store värdet i cache](#StoreToCacheByKey) princip.

### <a name="policy-statement"></a>Principframställning

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    cache-preference="prefer-external | external | internal" />
```

### <a name="example"></a>Exempel
Mer information och exempel på den här principen kan du se [anpassad cachelagring i Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|cache-sökning-value|Rotelement.|Ja|

### <a name="attributes"></a>Attribut

| Namn             | Beskrivning                                                                                                                                                                                                                                                                                                                                                 | Krävs | Standard           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| cache-preference | Välj mellan följande värden för attributet:<br />- `internal` du använder inbyggda API Management-cache<br />- `external` du använder externa cache enligt beskrivningen i [använder en extern Azure Cache för Redis i Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` använda externa cachen om konfigurerat eller interna cacheminnet annars. | Nej       | `prefer-external` |
| standard-värde    | Ett värde som ska tilldelas till variabeln om viktiga Cachesökning resulterade i en missa. Om det här attributet inte anges, `null` tilldelas.                                                                                                                                                                                                           | Nej       | `null`            |
| key              | Cachelagra viktiga värde som ska användas i sökningen.                                                                                                                                                                                                                                                                                                                       | Ja      | Gäller inte               |
| variabeln-name    | Namnet på den [sammanhangsvariabel](api-management-policy-expressions.md#ContextVariables) looked upp värdet kommer att tilldelas till, om sökningen lyckas. Om sökning resulterar i en missa, variabeln tilldelas värdet för den `default-value` attributet eller `null`, om den `default-value` attributet utelämnas.                                       | Ja      | Gäller inte               |

### <a name="usage"></a>Användning
Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Princip-avsnitt:** inkommande, utgående serverdelen, vid fel
- **Princip-scope:** globala, API, åtgärden, produkt

## <a name="StoreToCacheByKey"></a> Store-värdet i cache
Den `cache-store-value` utför cachelagring av nyckeln. Nyckeln kan ha en godtycklig sträng-värde och anges vanligtvis med en principuttryck.

> [!NOTE]
> Den här principen måste ha en motsvarande [hämta värdet från cachen](#GetFromCacheByKey) princip.

### <a name="policy-statement"></a>Principframställning

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" cache-preference="prefer-external | external | internal" />
```

### <a name="example"></a>Exempel
Mer information och exempel på den här principen kan du se [anpassad cachelagring i Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|cache-store-value|Rotelement.|Ja|

### <a name="attributes"></a>Attribut

| Namn             | Beskrivning                                                                                                                                                                                                                                                                                                                                                 | Krävs | Standard           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| cache-preference | Välj mellan följande värden för attributet:<br />- `internal` du använder inbyggda API Management-cache<br />- `external` du använder externa cache enligt beskrivningen i [använder en extern Azure Cache för Redis i Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` använda externa cachen om konfigurerat eller interna cacheminnet annars. | Nej       | `prefer-external` |
| varaktighet         | Värdet cachelagras för det angivna duration-värde som anges i sekunder.                                                                                                                                                                                                                                                                                 | Ja      | Gäller inte               |
| key              | Cachenyckel värdet lagras.                                                                                                                                                                                                                                                                                                                   | Ja      | Gäller inte               |
| värde            | Värdet som ska cachelagras.                                                                                                                                                                                                                                                                                                                                     | Ja      | Gäller inte               |
### <a name="usage"></a>Användning
Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Princip-avsnitt:** inkommande, utgående serverdelen, vid fel
- **Princip-scope:** globala, API, åtgärden, produkt

### <a name="RemoveCacheByKey"></a> Ta bort värdet från cache
Den `cache-remove-value` tar bort ett cachelagrade objekt som identifieras av dess nyckel. Nyckeln kan ha en godtycklig sträng-värde och anges vanligtvis med en principuttryck.

#### <a name="policy-statement"></a>Principframställning

```xml

<cache-remove-value key="cache key value" cache-preference="prefer-external | external | internal"  />

```

#### <a name="example"></a>Exempel

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|cache-remove-value|Rotelement.|Ja|

#### <a name="attributes"></a>Attribut

| Namn             | Beskrivning                                                                                                                                                                                                                                                                                                                                                 | Krävs | Standard           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| cache-preference | Välj mellan följande värden för attributet:<br />- `internal` du använder inbyggda API Management-cache<br />- `external` du använder externa cache enligt beskrivningen i [använder en extern Azure Cache för Redis i Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` använda externa cachen om konfigurerat eller interna cacheminnet annars. | Nej       | `prefer-external` |
| key              | Nyckeln för det tidigare cachelagrade värdet som ska tas bort från cachen.                                                                                                                                                                                                                                                                                        | Ja      | Gäller inte               |

#### <a name="usage"></a>Användning
Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .

- **Princip-avsnitt:** inkommande, utgående serverdelen, vid fel
- **Princip-scope:** globala, API, åtgärden, produkt

## <a name="next-steps"></a>Nästa steg

Arbeta med principer, Läs mer:

+ [Principer i API Management](api-management-howto-policies.md)
+ [Transformera API: er](transform-api.md)
+ [Principreferens för](api-management-policy-reference.md) för en fullständig lista över principrapporter och deras inställningar
+ [Princip-exempel](policy-samples.md)   
