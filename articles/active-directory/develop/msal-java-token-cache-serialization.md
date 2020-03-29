---
title: Anpassad serialisering av tokencache (MSAL4j)
titleSuffix: Microsoft identity platform
description: Lär dig hur du serialiserar tokencachen för MSAL för Java
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: bcb34d83365112b97769186ad74dfd762b05c2e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696171"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>Anpassad tokencache serialisering i MSAL för Java

Om du vill spara tokencachen mellan instanserna av ditt program måste du anpassa serialiseringen. Java-klasserna och gränssnitten som ingår i serialisering av tokencache är följande:

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): Gränssnitt som representerar säkerhetstokencache.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): Gränssnitt som representerar drift av körningskod före och efter åtkomst. Du @Override skulle *innanCacheAccess* och *afterCacheAccess* med logiken som ansvarar för serialisering och deserialisering av cachen.
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): Gränssnitt som representerar kontext där tokencachen används. 

Nedan finns en naiv implementering av anpassad serialisering av token cache serialisering /deserialization. Kopiera och klistra inte in den i en produktionsmiljö.

```Java
static class TokenPersistence implements ITokenCacheAccessAspect {
String data;

TokenPersistence(String data) {
        this.data = data;
}

@Override
public void beforeCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        iTokenCacheAccessContext.tokenCache().deserialize(data);
}

@Override
public void afterCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        data = iTokenCacheAccessContext.tokenCache().serialize();
}
```

```Java
// Loads cache from file
String dataToInitCache = readResource(this.getClass(), "/cache_data/serialized_cache.json");

ITokenCacheAccessAspect persistenceAspect = new TokenPersistence(dataToInitCache);

// By setting *TokenPersistence* on the PublicClientApplication, MSAL will call *beforeCacheAccess()* before accessing the cache and *afterCacheAccess()* after accessing the cache. 
PublicClientApplication app = 
PublicClientApplication.builder("my_client_id").setTokenCacheAccessAspect(persistenceAspect).build();
```

## <a name="learn-more"></a>Läs mer

Lär dig mer om [Hämta och ta bort konton från tokencachen med MSAL för Java](msal-java-get-remove-accounts-token-cache.md).
