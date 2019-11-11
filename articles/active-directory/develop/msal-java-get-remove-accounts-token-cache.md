---
title: Hämta och ta bort konton från token-cachen med MSAL for Java (MSAL4j)
titleSuffix: Microsoft identity platform
description: Lär dig hur du visar och tar bort konton från token-cachen med Microsoft Authentication Library för Java.
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
ms.openlocfilehash: 343abd87d3b5e8b82989b8b370cef61ec6d051df
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905516"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java-msal4j"></a>Hämta och ta bort konton från token-cachen med MSAL for Java (MSAL4j)

MSAL4J tillhandahåller en token-cache i minnet som standard. Token cache i minnet varar i varaktighet för program instansen.

## <a name="see-which-accounts-are-in-the-cache"></a>Se vilka konton som finns i cachen

Du kan kontrol lera vilka konton som finns i cachen genom att anropa `PublicClientApplication.getAccounts()` som visas i följande exempel:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Ta bort konton från cachen

Om du vill ta bort ett konto från cachen letar du reda på det konto som måste tas bort och anropar `PublicClientApplicatoin.removeAccount()` som visas i följande exempel:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Läs mer

Om du använder MSAL för Java kan du läsa mer om [anpassad cachelagring av token i MSAL för Java](msal-java-token-cache-serialization.md).
