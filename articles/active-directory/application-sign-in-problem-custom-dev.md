---
title: Problem med att logga till en anpassad utvecklade program | Microsoft Docs
description: Vanliga rrors som kan vara orsaken till att inte kunna logga in på ett program som du har utvecklat med Azure AD
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 57b620f45d1985351064020e122c088584bcdcf5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
ms.locfileid: "26614145"
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
