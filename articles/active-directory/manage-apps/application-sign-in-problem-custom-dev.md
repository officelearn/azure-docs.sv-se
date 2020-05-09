---
title: Problem med att logga in till ett anpassat program som har utvecklats | Microsoft Docs
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
ms.openlocfilehash: 01a424129abf88c18500c96bd1889fc6dcce2ec6
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890706"
---
# <a name="problems-signing-in-to-a-custom-developed-application"></a>Problem med att logga in till ett anpassat, utvecklat program

Det finns flera fel som kan orsaka att du inte kan logga in på en app. Det största skälet till att människor stöter på det här problemet är felkonfigurerade appar.

## <a name="errors-related-to--misconfigured-apps"></a>Fel som rör felkonfigurerade appar

* Kontrol lera att båda konfigurationerna i portalen matchar det du har i din app. Mer specifikt jämför klient/program-ID, svars-URL: er, klient hemligheter/nycklar och app-ID-URI.

* Jämför resursen som du begär åtkomst till i kod med de konfigurerade behörigheterna på fliken **obligatoriska resurser** för att se till att du endast begär resurser som du har konfigurerat.

* Se [Azure AD-StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory) för liknande fel eller problem.

## <a name="next-steps"></a>Nästa steg

[Guide för Azure AD-utvecklare](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Samtycke och integrering av appar till Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Behörigheter och medgivande i Microsoft Identity Platform-slutpunkten](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD-StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory>)
