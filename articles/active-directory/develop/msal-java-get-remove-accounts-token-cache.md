---
title: Hämta & ta bort konton från token-cachen (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du visar och tar bort konton från token-cachen med Microsoft Authentication Library för Java.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.openlocfilehash: 150708e7d54e456e1a4e35cfe381e5de5d0f1e8c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85479359"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Hämta och ta bort konton från token-cachen med MSAL för Java

MSAL för Java tillhandahåller en token-cache i minnet som standard. Token cache i minnet varar i varaktighet för program instansen.

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

Om du vill ta bort ett konto från cachen letar du reda på det konto som måste tas bort och anropar sedan det `PublicClientApplicatoin.removeAccount()` som visas i följande exempel:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Läs mer

Om du använder MSAL för Java kan du läsa mer om [anpassad cachelagring av token i MSAL för Java](msal-java-token-cache-serialization.md).
