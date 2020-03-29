---
title: Hämta & ta bort konton från tokencachen (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du visar och tar bort konton från tokencachen med Hjälp av Microsoft Authentication Library for Java.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.openlocfilehash: 2b138678b186cc41b76254658ad604c2da2d76c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696205"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Hämta och ta bort konton från tokencachen med MSAL för Java

MSAL för Java tillhandahåller som standard en tokencache i minnet. Tokencachen i minnet varar programinstansens varaktighet.

## <a name="see-which-accounts-are-in-the-cache"></a>Se vilka konton som finns i cacheminnet

Du kan kontrollera vilka konton som `PublicClientApplication.getAccounts()` finns i cacheminnet genom att ringa enligt följande exempel:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Ta bort konton från cacheminnet

Om du vill ta bort ett konto från cacheminnet `PublicClientApplicatoin.removeAccount()` hittar du kontot som måste tas bort och anropar sedan som visas i följande exempel:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Läs mer

Om du använder MSAL för Java kan du läsa om serialisering av [anpassad tokencache i MSAL för Java](msal-java-token-cache-serialization.md).
