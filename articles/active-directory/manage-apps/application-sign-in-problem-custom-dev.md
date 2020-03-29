---
title: Problem med att logga in på ett specialutvecklat program | Microsoft-dokument
description: Vanliga fel som kan orsaka att du inte kan logga in på ett program som du har utvecklat med Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8ad2499aea8bf4e41ca00d6c78d76e112f0493e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65825243"
---
# <a name="problems-signing-in-to-a-custom-developed-application"></a>Problem med att logga in på ett specialutvecklat program

Det finns flera fel som kan orsaka att du inte kan logga in på en app. Den största anledningen till att personer stöter på det här problemet är felkonfigurerade appar.

## <a name="errors-related-to--misconfigured-apps"></a>Fel relaterade till felkonfigurerade appar

* Kontrollera att båda konfigurationerna i portalen matchar det du har i appen. Jämför specifikt klient-/program-ID, svarsadresser, klienthemligheter/nycklar och app-ID-URI.

* Jämför den resurs som du begär åtkomst till i kod med de konfigurerade behörigheterna på fliken **Obligatoriska resurser** för att se till att du bara begär resurser som du har konfigurerat.

* Se [Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory) för liknande fel eller problem.

## <a name="next-steps"></a>Nästa steg

[Utvecklarhandboken för Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Samtycka och integrera appar till Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Medgivande och behörighet för Azure AD v2.0-konvergerade appar](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory>)
