---
title: Azure App konfiguration REST API-autentisering
description: Referens sidor för autentisering med Azure App konfigurations REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 21a43a005b78c8916d06e97ca9d2ba21d5a585a3
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424529"
---
# <a name="authentication"></a>Autentisering

Alla HTTP-begäranden måste autentiseras. Följande autentiseringsscheman stöds.

## <a name="hmac"></a>HMAC

[HMAC-autentisering](./rest-api-authentication-hmac.md) använder en slumpmässigt genererad hemlighet för att signera nytto laster för begäran. Information om hur förfrågningar som använder den här autentiseringsmetoden är auktoriserade finns i avsnittet [HMAC-auktorisering](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD)](/azure/active-directory/authentication/overview-authentication) använder en Bearer-token som erhålls från Azure Active Directory för att autentisera begär Anden. Information om hur begär Anden som använder den här autentiseringsmetoden är auktoriserade i avsnittet [Azure AD-auktorisering](./rest-api-authorization-azure-ad.md) .
