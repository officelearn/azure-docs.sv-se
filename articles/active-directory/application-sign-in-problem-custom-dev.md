---
title: Problem med att logga till en anpassad utvecklade program | Microsoft Docs
description: "Vanliga rrors som kan vara orsaken till att inte kunna logga in på ett program som du har utvecklat med Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: b0df23e040a73d18968f547eef7347f14cc577c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="problems-signing-in-to-an-custom-developed-application"></a>Problem med att logga till en anpassad utvecklade program

Det finns flera fel som kan vara orsaken till att inte kunna logga in på en app. Den största orsaken personer stöter problemet är felkonfigurerad appar.

## <a name="errors-related-to--misconfigured-apps"></a>Fel som rör felkonfigurerad appar

* Kontrollera att båda konfigurationerna i portalen matchar vad du har i din app. Mer specifikt jämför klient/program-ID, svars-URL: er, klienten hemligheter/nycklar och App-ID-URI.

* Jämför den resurs som du ska få åtkomst till i kod med behörigheterna konfigurerade i den **krävs resurser** flik och kontrollera att du bara begära resurser som du har konfigurerat.

* Se [Azure AD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory) för alla liknande fel eller problem.

## <a name="next-steps"></a>Nästa steg

[Utvecklarhandbok för Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Medgivande och integrera appar till Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications>)<br>

[Medgivande och Permissioning för Azure AD v2.0 konvergerat appar](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD-StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory>)
