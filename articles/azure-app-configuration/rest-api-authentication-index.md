---
title: Azure App konfiguration REST API-autentisering
description: Referens sidor för autentisering med Azure App konfigurations REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 56416009395ebf8270ad0fa8d141277424dd6d9a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183472"
---
# <a name="authentication"></a>Autentisering

Alla HTTP-begäranden måste autentiseras. Följande autentiseringsscheman stöds.

## <a name="hmac"></a>HMAC

[HMAC-autentisering](./rest-api-authentication-hmac.md) använder en slumpmässigt genererad hemlighet för att signera nytto laster för begäran. Information om hur förfrågningar som använder den här autentiseringsmetoden är auktoriserade finns i avsnittet [HMAC-auktorisering](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD)](../active-directory/authentication/overview-authentication.md) använder en Bearer-token som erhålls från Azure Active Directory för att autentisera begär Anden. Information om hur begär Anden som använder den här autentiseringsmetoden är auktoriserade i avsnittet [Azure AD-auktorisering](./rest-api-authorization-azure-ad.md) .