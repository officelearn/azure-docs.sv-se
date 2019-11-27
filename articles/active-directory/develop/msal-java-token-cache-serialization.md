---
title: Anpassad token cache-serialisering i MSAL för Java
titleSuffix: Microsoft identity platform
description: Lär dig hur du serialiserar token-cachen för MSAL för Java
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
manager: henrikm
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7323a47095c58958503cecf16189ed875fac3b4d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452597"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>Anpassad token cache-serialisering i MSAL för Java

Om du vill bevara token-cachen mellan instanser av ditt program måste du anpassa serialiseringen. Java-klasser och gränssnitt som ingår i cachelagring av token är följande:

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): gränssnitt som representerar cache för säkerhetstoken.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): gränssnitt som representerar körning av kod före och efter åtkomst. Du skulle @Override *beforeCacheAccess* och *afterCacheAccess* med den logik som ansvarar för serialisering och deserialisering av cacheminnet.
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): gränssnitt som representerar kontexten där token cache används. 

Nedan visas en naïve-implementering av anpassad serialisering för serialisering/deserialisering för token. Kopiera och klistra inte in detta i en produktions miljö.

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

Lär dig mer om [att hämta och ta bort konton från token-cachen med MSAL för Java](msal-java-get-remove-accounts-token-cache.md).
