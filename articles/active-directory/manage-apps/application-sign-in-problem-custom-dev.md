---
title: Problem vid inloggning i ett egenutvecklat program | Microsoft Docs
description: Vanliga fel som kan vara orsaken du inte kunna logga in på ett program som du har utvecklat med Azure AD
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65825243"
---
# <a name="problems-signing-in-to-a-custom-developed-application"></a>Problem vid inloggning ett egenutvecklat program

Det finns flera fel som kan vara orsaken du inte kunna logga in på en app. Den största orsaken personer inträffar det här problemet är felkonfigurerad appar.

## <a name="errors-related-to--misconfigured-apps"></a>Fel som rör felkonfigurerad appar

* Kontrollera båda konfigurationerna i portalen matchar vad du har i din app. Mer specifikt jämför klienter/program-ID, svars-URL, klient hemligheter/nycklar och App-ID-URI.

* Jämför den resurs som du ska få åtkomst till i kod med behörigheterna som är konfigurerade i den **krävs resurser** flik för att se till att du bara begära resurser som du har konfigurerat.

* Se [Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory) för alla liknande fel eller problem.

## <a name="next-steps"></a>Nästa steg

[Utvecklarguide för Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Medgivande och integrera appar i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Medgivande och ge behörighet till för Azure AD v2.0 konvergerat appar](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory>)
